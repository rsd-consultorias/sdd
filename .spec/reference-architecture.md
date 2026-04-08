# Reference Architecture & Design Patterns

**Purpose:** Document reusable architectural patterns and design decisions for consistent implementation across all features.

**Version:** 1.0  
**Last Updated:** 2026-04-07  
**Architecture Style:** Domain-Driven Design (DDD) with Layered Architecture

---

## Table of Contents

1. [Layered Architecture Pattern](#layered-architecture-pattern)
2. [Domain-Driven Design Patterns](#domain-driven-design-patterns)
3. [Error Handling Pattern](#error-handling-pattern)
4. [Service Layer Pattern](#service-layer-pattern)
5. [Repository Pattern](#repository-pattern)
6. [DTO & Mapping Pattern](#dto--mapping-pattern)
7. [Configuration Management](#configuration-management)
8. [Testing Patterns](#testing-patterns)
9. [Performance Optimization Patterns](#performance-optimization-patterns)
10. [Anti-Patterns to Avoid](#anti-patterns-to-avoid)

---

## Layered Architecture Pattern

### Overview

Our architecture separates concerns into four distinct layers, allowing independent development and testing of each layer.

```
┌─────────────────────────────────────────┐
│      PRESENTATION LAYER (Controller)    │  REST API, HTTP
├─────────────────────────────────────────┤
│    APPLICATION LAYER (Service)          │  Use Cases, Orchestration
├─────────────────────────────────────────┤
│     DOMAIN LAYER (Business Logic)       │  Entities, Value Objects
├─────────────────────────────────────────┤
│   INFRASTRUCTURE LAYER (Technical)      │  DB, External Services
└─────────────────────────────────────────┘
```

### Layer Responsibilities

#### 1. **Domain Layer** (`core/domain/`)
**Why:** Encapsulate business rules independent of framework

**Responsibilities:**
- Define entities (aggregate roots)
- Define value objects (immutable data)
- Define domain services (multi-entity business logic)
- Define domain exceptions
- Validate business rules

**Constraints:**
- ❌ NO Spring annotations
- ❌ NO database code
- ❌ NO HTTP logic
- ✅ Pure Java business logic

**Example:**
```java
// src/main/java/br/com/rsdconsultoria/demo/core/domain/entities/User.java
package br.com.rsdconsultoria.demo.core.domain.entities;

/**
 * User aggregate root.
 * Encapsulates user identity and authentication.
 * Implements domain rule: Password must be bcrypted (cost 12 minimum).
 */
public class User {
    private Long id;
    private String username;
    private String passwordHash;
    private Set<String> roles;
    
    // Factory method for creating new users
    public static User createNew(String username, String plainPassword, String email) {
        if (plainPassword.length() < 8) {
            throw new InvalidPasswordException("Password too short");
        }
        return new User(username, hashPassword(plainPassword), email);
    }
    
    // Domain rule enforcement
    public boolean isPasswordValid(String plainPassword) {
        // Never expose plain password
        return BCrypt.checkpw(plainPassword, this.passwordHash);
    }
    
    public Set<String> getRoles() {
        return Set.copyOf(this.roles); // Immutable
    }
}
```

---

#### 2. **Application Layer** (`core/application/`)
**Why:** Orchestrate domain objects for specific use cases

**Responsibilities:**
- Define use case workflows
- Coordinate domain entities and services
- Manage transactions (Spring @Transactional)
- Map between domain objects and DTOs
- Handle cross-cutting concerns (logging, metrics)

**Constraints:**
- ❌ NO HTTP logic
- ❌ NO direct database queries (use repositories)
- ✅ Coordinate domain logic
- ✅ Use Spring dependency injection

**Example:**
```java
// src/main/java/br/com/rsdconsultoria/demo/core/application/services/LoginService.java
package br.com.rsdconsultoria.demo.core.application.services;

@Service
public class LoginService {
    private final UserRepository userRepository;
    private final TokenService tokenService;
    
    @Transactional(readOnly = true)
    public LoginResponse login(String username, String password) {
        // Step 1: Load domain entity
        User user = userRepository.findByUsername(username)
            .orElseThrow(() -> new InvalidCredentialsException());
        
        // Step 2: Execute domain rule
        if (!user.isPasswordValid(password)) {
            throw new InvalidCredentialsException();
        }
        
        // Step 3: Delegate to another service
        Token token = tokenService.generateToken(user);
        
        // Step 4: Map to DTO for response
        return new LoginResponse(token.getAccessToken(), token.getRefreshToken());
    }
}
```

---

#### 3. **Infrastructure Layer** (`infrastructure/`)
**Why:** Implement technical details (persistence, external services)

**Responsibilities:**
- Implement repository interfaces
- Implement external service clients
- Configure database access
- Manage caches
- File storage, messaging, etc.

**Constraints:**
- ❌ NO business logic
- ✅ Implement ports defined by domain/application
- ✅ Use Spring Data JPA, external clients

**Example:**
```java
// src/main/java/br/com/rsdconsultoria/demo/infrastructure/repositories/JpaUserRepository.java
package br.com.rsdconsultoria.demo.infrastructure.repositories;

@Repository
public class JpaUserRepository implements UserRepository {
    private final UserJpaEntity repository; // Spring Data interface
    
    @Override
    public Optional<User> findByUsername(String username) {
        return repository.findByUsername(username)
            .map(UserJpaEntity::toDomain); // Map to domain entity
    }
    
    @Override
    public void save(User user) {
        UserJpaEntity entity = UserJpaEntity.fromDomain(user);
        repository.save(entity);
    }
}
```

---

#### 4. **Presentation Layer** (`controller/`)
**Why:** Handle HTTP requests/responses

**Responsibilities:**
- Define REST endpoints
- Validate input (request validation)
- Serialize/deserialize JSON
- Handle HTTP status codes
- Global exception handling

**Constraints:**
- ❌ NO business logic (except input validation)
- ✅ Delegate to application services
- ✅ Map DTOs to domain objects

**Example:**
```java
// src/main/java/br/com/rsdconsultoria/demo/controller/AuthController.java
package br.com.rsdconsultoria.demo.controller;

@RestController
@RequestMapping("/api/auth")
public class AuthController {
    private final LoginService loginService;
    
    @PostMapping("/login")
    public ResponseEntity<LoginResponse> login(@Valid @RequestBody LoginRequest request) {
        // Validation done by Spring (annotations on LoginRequest)
        // Delegate to application service
        LoginResponse response = loginService.login(request.username(), request.password());
        return ResponseEntity.ok(response);
    }
}
```

---

## Domain-Driven Design Patterns

### 1. Aggregate Pattern

**Definition:** A cluster of associated objects that we treat as a single unit for data changes.

**Why:** Enforce consistency boundaries, simplify transactions

**Pattern:**

```
Aggregate Root
    ↓
  Entities (within aggregate)
    ↓
  Value Objects (immutable)
```

**Example:**
```java
// LeaveRequest is the AGGREGATE ROOT
public class LeaveRequest {
    private Long id;
    private String requestId;
    
    // Entities within aggregate
    private List<LeaveApprovalStep> approvalSteps;
    private List<LeaveComment> comments;
    
    // Value objects
    private DateRange leaveRange; // Immutable value object
    private LeaveStatus status;   // Enum = value object
    
    // Only modify through aggregate root
    public void approve(String approverId, String reason) {
        this.approvalSteps.add(new LeaveApprovalStep(approverId, reason));
        this.status = LeaveStatus.APPROVED;
    }
    
    // Only expose copies of internal entities
    public List<LeaveComment> getComments() {
        return List.copyOf(comments); // Immutable
    }
}

// Can only be persisted/retrieved as whole
// Repository<LeaveRequest> not Repository<LeaveComment>
```

**Key Rules:**
- ✅ One root entity per aggregate
- ✅ External references only to root
- ✅ Internal entities accessible only through root
- ✅ Repository only for aggregate root
- ✅ Transactions cover entire aggregate

---

### 2. Value Object Pattern

**Definition:** Immutable objects identified by value, not identity.

**Why:** Simplify logic, guarantee consistency

**Pattern:**

```java
// Value Object: DateRange
public class DateRange {
    private final LocalDate startDate;
    private final LocalDate endDate;
    
    public DateRange(LocalDate startDate, LocalDate endDate) {
        if (startDate.isAfter(endDate)) {
            throw new InvalidDateRangeException("Start after end");
        }
        this.startDate = startDate;
        this.endDate = endDate;
    }
    
    // No setters - immutable
    // Equality based on values, not identity
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof DateRange that)) return false;
        return Objects.equals(startDate, that.startDate) &&
               Objects.equals(endDate, that.endDate);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(startDate, endDate);
    }
    
    public long getDays() {
        return ChronoUnit.DAYS.between(startDate, endDate);
    }
}

// Usage
DateRange vacation = new DateRange(LocalDate.of(2026, 4, 15), LocalDate.of(2026, 4, 20));
// vacation.startDate = new LocalDate(...) // ❌ COMPILE ERROR - no setter
```

**When to use:**
- ✅ When equality based on values
- ✅ When immutability needed
- ✅ When business logic is small/focused
- ❌ When identity matters

---

### 3. Domain Service Pattern

**Definition:** Stateless service holding domain logic that doesn't fit in entity.

**Why:** Handle logic spanning multiple aggregates

**Pattern:**

```java
// Domain Service (no Spring annotation)
public class LeaveBalanceService {
    /**
     * Calculate available leave balance for employee.
     * Logic spans multiple aggregates (Employee, LeaveRequest, LeavePolicy).
     */
    public int calculateAvailableBalance(Employee employee, LeavePolicy policy) {
        int allocated = policy.getAllocationForRole(employee.getRole());
        int used = employee.getUsedLeave();
        return allocated - used;
    }
}

// Usage in Application Service
@Service
public class LeaveApprovalService {
    private final LeaveBalanceService balanceService; // Injected by Spring
    
    public void approveLeaveRequest(LeaveRequest request, Employee employee) {
        LeavePolicy policy = findPolicy(employee);
        int available = balanceService.calculateAvailableBalance(employee, policy);
        
        if (request.getDays() > available) {
            throw new InsufficientLeaveException();
        }
        request.approve();
    }
}
```

---

## Error Handling Pattern

### Domain Exceptions

**Purpose:** Express domain-specific business logic violations

```java
// Hierarchy
RuntimeException
    ├─ DomainException (base for all domain errors)
    │   ├─ InvalidCredentialsException
    │   ├─ InsufficientLeaveException
    │   ├─ UnauthorizedException
    │   └─ [Other business domain exceptions]
    └─ [Framework exceptions]

// Define in domain layer
package br.com.rsdconsultoria.demo.core.domain.exception;

public class InsufficientLeaveException extends DomainException {
    private final int available;
    private final int requested;
    
    public InsufficientLeaveException(int available, int requested) {
        super(String.format(
            "Insufficient leave balance. Available: %d days, Requested: %d days",
            available, requested
        ));
        this.available = available;
        this.requested = requested;
    }
    
    public int getAvailable() { return available; }
    public int getRequested() { return requested; }
}

// Throw from domain
public void approveLeaveRequest(LeaveRequest request) {
    if (this.availableLeave < request.getDays()) {
        throw new InsufficientLeaveException(this.availableLeave, request.getDays());
    }
}
```

### Global Exception Handler

**Purpose:** Convert exceptions to HTTP responses

```java
// In Presentation Layer
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    // Domain exceptions → 400 Bad Request
    @ExceptionHandler(DomainException.class)
    public ResponseEntity<ErrorResponse> handleDomainException(DomainException ex) {
        return ResponseEntity
            .status(HttpStatus.BAD_REQUEST)
            .body(new ErrorResponse(
                "BUSINESS_RULE_VIOLATION",
                ex.getMessage()
            ));
    }
    
    // Authorization exceptions → 403 Forbidden
    @ExceptionHandler(UnauthorizedException.class)
    public ResponseEntity<ErrorResponse> handleUnauthorized(UnauthorizedException ex) {
        return ResponseEntity
            .status(HttpStatus.FORBIDDEN)
            .body(new ErrorResponse("UNAUTHORIZED", ex.getMessage()));
    }
    
    // Validation exceptions → 422 Unprocessable Entity
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidation(MethodArgumentNotValidException ex) {
        List<String> errors = ex.getBindingResult()
            .getFieldErrors()
            .stream()
            .map(e -> e.getField() + ": " + e.getDefaultMessage())
            .collect(Collectors.toList());
        
        return ResponseEntity
            .status(HttpStatus.UNPROCESSABLE_ENTITY)
            .body(new ErrorResponse("VALIDATION_FAILED", errors.toString()));
    }
    
    // Unexpected exceptions → 500 Internal Server Error
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleUnexpected(Exception ex) {
        logger.error("Unexpected error", ex);
        return ResponseEntity
            .status(HttpStatus.INTERNAL_SERVER_ERROR)
            .body(new ErrorResponse("INTERNAL_ERROR", "An unexpected error occurred"));
    }
}
```

---

## Service Layer Pattern

### Application Service Structure

```java
@Service
public class LeaveApprovalService {
    
    private final LeaveRequestRepository requestRepository;
    private final EmailService emailService;
    private final AuditService auditService;
    
    // Constructor injection for all dependencies
    public LeaveApprovalService(
        LeaveRequestRepository requestRepository,
        EmailService emailService,
        AuditService auditService
    ) {
        this.requestRepository = requestRepository;
        this.emailService = emailService;
        this.auditService = auditService;
    }
    
    // Use case method
    @Transactional
    public void approveLeaveRequest(String requestId, String approverId, String reason) {
        // 1. Load aggregate
        LeaveRequest request = requestRepository.findById(requestId)
            .orElseThrow(() -> new LeaveRequestNotFoundException());
        
        // 2. Execute domain logic
        request.approve(approverId, reason);
        
        // 3. Persist changes
        requestRepository.save(request);
        
        // 4. Post-domain actions (notifications, audit)
        emailService.sendApprovalNotification(request.getEmployeeId());
        auditService.log("LEAVE_APPROVED", requestId, approverId, reason);
    }
}
```

---

## Repository Pattern

### Interface Definition (in Domain/Application)

```java
// Define repository interface in domain layer
public interface LeaveRequestRepository {
    void save(LeaveRequest request);
    Optional<LeaveRequest> findById(String id);
    List<LeaveRequest> findPendingByManager(String managerId);
    List<LeaveRequest> findByEmployeeAndDateRange(String employeeId, DateRange range);
}
```

### Implementation (in Infrastructure)

```java
@Repository
public class JpaLeaveRequestRepository implements LeaveRequestRepository {
    
    private final LeaveRequestJpaRepository jpaRepository;
    
    @Override
    public void save(LeaveRequest request) {
        LeaveRequestEntity entity = LeaveRequestEntity.fromDomain(request);
        jpaRepository.save(entity);
    }
    
    @Override
    public Optional<LeaveRequest> findById(String id) {
        return jpaRepository.findById(id)
            .map(LeaveRequestEntity::toDomain);
    }
    
    @Override
    public List<LeaveRequest> findPendingByManager(String managerId) {
        return jpaRepository.findByStatusAndManagerId(LeaveStatus.PENDING, managerId)
            .stream()
            .map(LeaveRequestEntity::toDomain)
            .collect(Collectors.toList());
    }
}

// Spring Data interface
@org.springframework.data.repository.Repository
public interface LeaveRequestJpaRepository 
        extends org.springframework.data.jpa.repository.JpaRepository<LeaveRequestEntity, String> {
    List<LeaveRequestEntity> findByStatusAndManagerId(LeaveStatus status, String managerId);
}
```

---

## DTO & Mapping Pattern

### Request DTOs (Input)

```java
// src/main/java/br/com/rsdconsultoria/demo/dto/LeaveApprovalRequest.java
package br.com.rsdconsultoria.demo.dto;

@Data
@NoArgsConstructor
public class LeaveApprovalRequest {
    @NotBlank(message = "Reason required")
    private String reason;
    
    @NotNull(message = "Action required")
    private ApprovalAction action; // APPROVE or REJECT
    
    // Validated by Spring before controller method called
}

// Enum for validation
public enum ApprovalAction {
    APPROVE, REJECT
}
```

### Response DTOs (Output)

```java
public class LeaveApprovalResponse {
    private String requestId;
    private LeaveStatus status;
    private LocalDateTime approvedAt;
    private String approverId;
    
    // Factory method for conversion
    public static LeaveApprovalResponse fromDomain(LeaveRequest request, String approverId) {
        LeaveApprovalResponse dto = new LeaveApprovalResponse();
        dto.requestId = request.getId();
        dto.status = request.getStatus();
        dto.approvedAt = LocalDateTime.now();
        dto.approverId = approverId;
        return dto;
    }
}
```

### Mapping in Application Service

```java
@Service
public class LeaveApprovalService {
    
    @Transactional
    public LeaveApprovalResponse approveLeaveRequest(
        String requestId,
        LeaveApprovalRequest request, // Request DTO
        String approverId
    ) {
        // 1. Load domain entity
        LeaveRequest leaveRequest = requestRepository.findById(requestId)
            .orElseThrow(() -> new LeaveRequestNotFoundException());
        
        // 2. Execute domain logic (no DTO here)
        leaveRequest.approve(approverId, request.reason);
        
        // 3. Persist
        requestRepository.save(leaveRequest);
        
        // 4. Map back to Response DTO
        return LeaveApprovalResponse.fromDomain(leaveRequest, approverId);
    }
}
```

---

## Configuration Management

### Environment-Based Configuration

```java
// src/main/java/br/com/rsdconsultoria/demo/config/ApplicationProperties.java
@Configuration
@ConfigurationProperties(prefix = "app")
public class ApplicationProperties {
    
    private Jwt jwt = new Jwt();
    private Leave leave = new Leave();
    
    public static class Jwt {
        private long expirationMillis = 3600000; // 1 hour
        private String algorithm = "RS256";
        // getters/setters
    }
    
    public static class Leave {
        private int defaultAllocationDays = 20;
        private boolean requireManagerApproval = true;
        // getters/setters
    }
}

// application.yml
app:
  jwt:
    expirationMillis: 3600000
    algorithm: RS256
  leave:
    defaultAllocationDays: 20
    requireManagerApproval: true
```

---

## Testing Patterns

### Unit Tests (Domain Layer)

```java
@DisplayName("User Password Validation")
class UserPasswordTests {
    
    private User user;
    
    @BeforeEach
    void setUp() {
        // Arrange: Create domain entity
        user = User.createNew("john.doe", "SecurePass123", "john@example.com");
    }
    
    @Test
    @DisplayName("should accept valid password")
    void testValidPassword() {
        // Act & Assert
        assertTrue(user.isPasswordValid("SecurePass123"));
    }
    
    @Test
    @DisplayName("should reject invalid password")
    void testInvalidPassword() {
        // Act & Assert
        assertFalse(user.isPasswordValid("WrongPassword"));
    }
    
    @Test
    @DisplayName("should throw exception for short password")
    void testShortPassword() {
        // Assert: Exception thrown during construction
        assertThrows(InvalidPasswordException.class, () -> {
            User.createNew("jane.doe", "short", "jane@example.com");
        });
    }
}
```

### Integration Tests (Application Layer)

```java
@SpringBootTest
@Transactional
class LeaveApprovalServiceTests {
    
    @Autowired
    private LeaveApprovalService service;
    
    @Autowired
    private LeaveRequestRepository repository;
    
    @Test
    @DisplayName("should approve leave request and send notification")
    void testApproveLeaveRequest() {
        // Arrange
        LeaveRequest request = new LeaveRequest(...);
        repository.save(request);
        
        // Act
        service.approveLeaveRequest(request.getId(), "manager123", "Approved");
        
        // Assert
        LeaveRequest approved = repository.findById(request.getId()).orElseThrow();
        assertEquals(LeaveStatus.APPROVED, approved.getStatus());
        // Verify email sent (mock EmailService)
    }
}
```

---

## Performance Optimization Patterns

### 1. Database Query Optimization

```java
// ❌ BAD: N+1 query problem
@Repository
public class LeaveRequestRepository {
    public List<LeaveRequest> findByManager(String managerId) {
        List<LeaveRequestEntity> entities = jpaRepository.findByManagerId(managerId);
        // For each LeaveRequest, fetches associated Employee → N+1 queries
        return entities.stream()
            .map(this::toDomain)
            .collect(Collectors.toList());
    }
}

// ✅ GOOD: Eager load relationships
@Repository
public class LeaveRequestRepository {
    public List<LeaveRequest> findByManager(String managerId) {
        return jpaRepository.findByManagerIdWithEmployee(managerId)
            .stream()
            .map(this::toDomain)
            .collect(Collectors.toList());
    }
}

// In JPA interface
@Query("""
    SELECT l FROM LeaveRequestEntity l
    JOIN FETCH l.employee  // Eager load
    WHERE l.managerId = :managerId
""")
List<LeaveRequestEntity> findByManagerIdWithEmployee(@Param("managerId") String managerId);
```

### 2. Caching Pattern

```java
@Service
public class LeaveBalanceService {
    
    @Cacheable(value = "leaveBalance", key = "#employeeId + '-' + #year")
    public int getAvailableBalance(String employeeId, int year) {
        // Only called once per employee/year
        // Result cached for subsequent calls
        return calculateFromDatabase(employeeId, year);
    }
    
    @CacheEvict(value = "leaveBalance", key = "#employeeId + '-' + #year")
    @Transactional
    public void updateBalance(String employeeId, int year, int newBalance) {
        // Update in database
        // Invalidate cache
    }
}
```

---

## Anti-Patterns to Avoid

### ❌ Service Locator

```java
// ❌ DON'T DO THIS
public class LeaveApprovalService {
    public void approve(String requestId) {
        UserRepository userRepo = ServiceLocator.get(UserRepository.class);
        EmailService email = ServiceLocator.get(EmailService.class);
        // Hard to test, hidden dependencies
    }
}

// ✅ DO THIS INSTEAD
public class LeaveApprovalService {
    private final UserRepository userRepository;
    private final EmailService emailService;
    
    public LeaveApprovalService(UserRepository userRepository, EmailService emailService) {
        this.userRepository = userRepository;
        this.emailService = emailService;
    }
}
```

### ❌ Anemic Domain Model

```java
// ❌ DON'T DO THIS - all logic in service
public class LeaveRequest {
    public String id;
    public String employeeId;
    public LocalDate startDate;
    public LocalDate endDate;
    // No methods, just data container
}

public class LeaveApprovalService {
    public void approve(LeaveRequest request, String approverId) {
        if (LocalDate.now().isAfter(request.endDate)) {
            throw new Exception("Can't approve past leave");
        }
        request.status = "APPROVED";
        // All business logic in service
    }
}

// ✅ DO THIS INSTEAD - business logic in entity
public class LeaveRequest {
    private String id;
    private String employeeId;
    private LocalDate startDate;
    private LocalDate endDate;
    
    public void approve(String approverId) {
        if (LocalDate.now().isAfter(this.endDate)) {
            throw new CannotApproveExpiredLeaveRequestException();
        }
        this.status = LeaveStatus.APPROVED;
    }
}
```

### ❌ Mixing Concerns in Controller

```java
// ❌ DON'T DO THIS
@RestController
public class LeaveController {
    @PostMapping("/approve")
    public void approve(@RequestBody LeaveApprovalRequest request) {
        // Business logic in controller
        LeaveRequest leave = findLeaveInDatabase(request.id);
        if (leave.status.equals("PENDING")) {
            leave.status = "APPROVED";
            saveLeaveToDatabase(leave);
            sendEmail(leave.employeeId);
        }
    }
}

// ✅ DO THIS INSTEAD
@RestController
public class LeaveController {
    private final LeaveApprovalService service;
    
    @PostMapping("/approve")
    public ResponseEntity<LeaveApprovalResponse> approve(
        @RequestBody @Valid LeaveApprovalRequest request
    ) {
        // Delegate to application service
        LeaveApprovalResponse response = service.approveLeaveRequest(request.id, request.reason);
        return ResponseEntity.ok(response);
    }
}
```

---

## Conclusion

These patterns provide:
- ✅ **Consistency** across features
- ✅ **Testability** of business logic
- ✅ **Maintainability** for future changes
- ✅ **Scalability** as system grows

**When implementing new features:**

1. Reference this document
2. Follow the layer responsibilities
3. Check anti-patterns list
4. Validate with team code review

---

**Document Version:** 1.0  
**Last Updated:** 2026-04-07  
**Maintained By:** Architecture Team  
**Review Frequency:** Quarterly
