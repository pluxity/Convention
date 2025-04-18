
# Spring/JPA 코딩 컨벤션

## 목차
- 패키지 구조
- 클래스 명명 규칙
- 엔티티 설계
- 리포지토리 명명 규칙
- 서비스 계층 설계
- 컨트롤러 설계
- 트랜잭션 관리
- JPA 쿼리 메소드
- N+1 문제 해결

## 패키지 구조

계층형 구조와 기능형 구조 중 프로젝트 규모와 목적에 맞게 선택하세요.

#### 해결책

계층형 구조는 기술 계층별로 패키지를 구성하고, 기능형 구조는 비즈니스 도메인별로 패키지를 구성합니다.

```
// Bad - 혼합된 구조
com.example.project
  ├── controller
  │   └── UserController.java
  ├── user
  │   ├── UserService.java
  │   └── UserRepository.java
  └── entity
      └── User.java

// Good - 계층형 구조
com.example.project
  ├── controller
  │   └── UserController.java
  ├── service
  │   └── UserService.java
  ├── repository
  │   └── UserRepository.java
  └── entity
      └── User.java

// Good - 기능형 구조 (DDD 스타일)
com.example.project
  ├── domain
  │   ├── user
  │   │   ├── User.java (엔티티)
  │   │   ├── UserRepository.java (인터페이스)
  │   │   ├── Email.java (값 객체)
  │   │   └── UserCreatedEvent.java (도메인 이벤트)
  │   └── order
  │       ├── Order.java (애그리거트 루트)
  │       ├── OrderItem.java (엔티티)
  │       └── OrderRepository.java (인터페이스)
  ├── application
  │   ├── user
  │   │   ├── UserService.java
  │   │   └── UserEventHandler.java
  │   └── order
  │       └── OrderService.java
  ├── infrastructure
  │   ├── persistence
  │   │   ├── JpaUserRepository.java
  │   │   └── JpaOrderRepository.java
  │   └── security
  │       └── SecurityConfig.java
  └── presentation
      ├── api
      │   ├── UserController.java
      │   └── OrderController.java
      └── dto
          ├── UserResponseDto.java
          └── OrderRequestDto.java
```

## 클래스 명명 규칙

Spring 컴포넌트와 JPA 엔티티는 명확한 네이밍 규칙을 따라야 합니다.

#### 해결책

각 레이어에 맞는 접미사를 일관되게 사용하세요.

```java
// Bad
@Entity
public class UserInfo { ... }

@Repository
public class UserData { ... }

@Service
public class UserBusinessLogic { ... }

@Controller
public class ManageUsers { ... }

// Good
@Entity
public class User { ... }

@Repository
public class UserRepository { ... }

@Service
public class UserService { ... }

@Controller
public class UserController { ... }
```

## 엔티티 설계

엔티티는 데이터베이스 테이블과 매핑되는 객체로, 명확하고 일관된 방식으로 설계해야 합니다.

#### 해결책

엔티티는 private 필드와 getter를 사용하고, 상태 변경은 의미 있는 비즈니스 메소드로 캡슐화하세요.

```java
// Bad
@Entity
public class User {
    public Long id;
    public String name;
    public List<Order> orders = new ArrayList<>();
    
    public void setName(String name) {
        this.name = name;
    }
    
    public void setEmail(String email) {
        this.email = email;
    }
}

// Good
@Entity
@Table(name = "user_account") // 스네이크 케이스로 테이블명 지정
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED) // 접근 제한을 가진 기본 생성자
public class User {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false, length = 50)
    private String name;
    
    @Embedded
    private Email email;
    
    @Enumerated(EnumType.STRING)
    private UserStatus status = UserStatus.ACTIVE;
    
    @OneToMany(mappedBy = "user", fetch = FetchType.LAZY)
    private List<Order> orders = new ArrayList<>();
    
    @Builder
    public User(String name, Email email) {
        this.name = name;
        this.email = email;
    }
    
    // 의미 있는 도메인 메소드로 비즈니스 로직 구현
    public void changeName(String newName) {
        if (newName == null || newName.isBlank()) {
            throw new IllegalArgumentException("이름은 비어있을 수 없습니다");
        }
        this.name = newName;
    }
    
    public void deactivate() {
        if (this.status != UserStatus.ACTIVE) {
            throw new IllegalStateException("이미 비활성화된 사용자입니다");
        }
        this.status = UserStatus.INACTIVE;
    }
    
    public Order createOrder() {
        Order order = new Order(this);
        this.orders.add(order);
        return order;
    }
}

@Embeddable
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Email {
    @Column(name = "email", nullable = false, unique = true)
    private String value;
    
    public Email(String value) {
        if (!isValid(value)) {
            throw new IllegalArgumentException("유효하지 않은 이메일 형식입니다");
        }
        this.value = value;
    }
    
    private boolean isValid(String email) {
        // 이메일 유효성 검사 로직
        return email != null && email.contains("@");
    }
}
```

## 엔티티 간 연관관계 설정

DDD 기반의 애그리거트 패턴을 적용하여 엔티티간 관계를 설계하세요.

#### 해결책

양방향 관계에서는 연관관계 편의 메소드를 사용하고, 애그리거트 루트를 통해 관계를 관리하세요.

```java
// Bad
@Entity
public class Order {
    @ManyToOne // FetchType 미설정
    private User user;
    
    @OneToMany(cascade = CascadeType.ALL, orphanRemoval = true) // 과도한 cascade 설정
    private List<OrderItem> orderItems;
    
    public void setUser(User user) {
        this.user = user;
    }
    
    public void addOrderItem(OrderItem item) {
        this.orderItems.add(item);
    }
}

// Good - DDD 패턴 적용
@Entity
@Table(name = "orders")
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Order {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToOne(fetch = FetchType.LAZY) // 항상 LAZY 로딩 사용
    @JoinColumn(name = "user_id") // 외래 키 이름 명시
    private User user;
    
    @OneToMany(mappedBy = "order", cascade = CascadeType.PERSIST) // PERSIST만 사용
    private List<OrderItem> orderItems = new ArrayList<>(); // NPE 방지를 위한 초기화
    
    @Enumerated(EnumType.STRING)
    private OrderStatus status = OrderStatus.CREATED;
    
    @Embedded
    private Money totalAmount = Money.ZERO;
    
    @Builder
    public Order(User user) {
        this.user = user;
    }
    
    // 도메인 로직을 포함한 비즈니스 메소드
    public void addItem(Product product, int quantity) {
        if (status != OrderStatus.CREATED) {
            throw new IllegalStateException("생성 상태의 주문만 상품을 추가할 수 있습니다");
        }
        
        OrderItem item = new OrderItem(this, product, quantity);
        orderItems.add(item);
        recalculateTotalAmount();
    }
    
    public void cancel() {
        if (status != OrderStatus.CREATED && status != OrderStatus.PENDING) {
            throw new IllegalStateException("생성 또는 대기 상태의 주문만 취소할 수 있습니다");
        }
        
        this.status = OrderStatus.CANCELLED;
        
        // 도메인 이벤트 발행
        Events.publish(new OrderCancelledEvent(this.id));
    }
    
    public void confirm() {
        if (status != OrderStatus.PENDING) {
            throw new IllegalStateException("대기 상태의 주문만 확정할 수 있습니다");
        }
        
        this.status = OrderStatus.CONFIRMED;
    }
    
    private void recalculateTotalAmount() {
        this.totalAmount = orderItems.stream()
                .map(OrderItem::getItemAmount)
                .reduce(Money.ZERO, Money::add);
    }
}

@Entity
@Table(name = "order_item")
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class OrderItem {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "order_id")
    private Order order;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "product_id")
    private Product product;
    
    private int quantity;
    
    @Embedded
    private Money itemAmount;
    
    public OrderItem(Order order, Product product, int quantity) {
        this.order = order;
        this.product = product;
        this.quantity = quantity;
        this.itemAmount = product.getPrice().multiply(quantity);
    }
    
    public void updateQuantity(int newQuantity) {
        if (newQuantity <= 0) {
            throw new IllegalArgumentException("수량은 1개 이상이어야 합니다");
        }
        this.quantity = newQuantity;
        this.itemAmount = product.getPrice().multiply(newQuantity);
    }
}
```

## N+1 문제 해결

N+1 문제는 JPA에서 흔히 발생하는 성능 이슈로, 적절한 페치 전략으로 해결해야 합니다.

#### 해결책

엔티티에서 모두 Lazy 로딩을 설정하고, N+1 문제는 Repository 레벨에서 해결하세요.

```java
// Bad - N+1 문제 발생 가능성
@Entity
public class User {
    @OneToMany(fetch = FetchType.EAGER) // EAGER 로딩 사용
    private List<Order> orders;
}

// 사용 시
List<User> users = userRepository.findAll();
// 각 사용자마다 주문을 조회하는 쿼리가 추가 실행됨

// Good - 1. EntityGraph 사용
public interface UserRepository extends JpaRepository<User, Long> {
    @EntityGraph(attributePaths = {"orders"})
    List<User> findAllWithOrders();
    
    // 2. fetch join 사용
    @Query("""
        SELECT u FROM User u 
        LEFT JOIN FETCH u.orders 
        WHERE u.id = :id
    """)
    Optional<User> findWithOrdersById(@Param("id") Long id);
    
    // 3. 컬렉션 fetch join과 페이징을 함께 사용할 때는 ID 조회 후 IN 절 활용
    @Query("SELECT u.id FROM User u")
    List<Long> findAllUserIds(Pageable pageable);
    
    @Query("""
        SELECT u FROM User u 
        LEFT JOIN FETCH u.orders 
        WHERE u.id IN :ids
    """)
    List<User> findByIdsWithOrders(@Param("ids") List<Long> ids);
    
    // 4. 최후의 수단: NativeQuery (타입 안전성 부족, 유지보수 어려움 증가)
    @Query(value = """
        SELECT u.*, o.* 
        FROM user_account u 
        LEFT JOIN orders o ON u.id = o.user_id 
        WHERE u.id = :id
    """, nativeQuery = true)
    User findWithOrdersByIdNative(@Param("id") Long id);
}
```

## 리포지토리 명명 규칙

리포지토리 인터페이스와 메소드 이름은 명확하고 일관되게 작성해야 합니다.

#### 해결책

리포지토리 이름은 엔티티 이름 + Repository로 하고, 메소드 이름은 JPA 쿼리 메소드 규칙을 따르세요.

```java
// Bad
public interface UserData extends JpaRepository<User, Long> {
    List<User> getActiveUsers();
    User selectByEmail(String email);
}

// Good
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByActiveTrueOrderByNameAsc();
    Optional<User> findByEmailValue(String email);
    
    // 정적 쿼리는 @Query에 triple quote로 작성
    @Query("""
        SELECT u FROM User u
        WHERE u.lastLoginDate > :date
        ORDER BY u.name ASC
    """)
    List<User> findRecentlyActiveUsers(@Param("date") LocalDateTime date);
}
```

## 서비스 계층 설계

DDD에서는 서비스를 애플리케이션 서비스와 도메인 서비스로 구분합니다.

#### 해결책

애플리케이션 서비스는 Use Case 오케스트레이션에 집중하고, 비즈니스 로직은 도메인 객체에 위임하세요.

```java
// Bad - 서비스에 비즈니스 로직 집중
@Service
public class OrderService {
    private final OrderRepository orderRepository;
    private final ProductRepository productRepository;
    
    @Transactional
    public Order createOrder(Long userId, List<OrderItemDto> items) {
        User user = userRepository.findById(userId)
            .orElseThrow(() -> new EntityNotFoundException("사용자를 찾을 수 없습니다."));
        
        Order order = new Order();
        order.setUser(user);
        
        BigDecimal totalAmount = BigDecimal.ZERO;
        
        for (OrderItemDto itemDto : items) {
            Product product = productRepository.findById(itemDto.getProductId())
                .orElseThrow(() -> new EntityNotFoundException("상품을 찾을 수 없습니다."));
            
            if (product.getStockQuantity() < itemDto.getQuantity()) {
                throw new IllegalStateException("재고가 부족합니다.");
            }
            
            OrderItem orderItem = new OrderItem();
            orderItem.setOrder(order);
            orderItem.setProduct(product);
            orderItem.setQuantity(itemDto.getQuantity());
            orderItem.setPrice(product.getPrice());
            
            BigDecimal itemAmount = product.getPrice().multiply(BigDecimal.valueOf(itemDto.getQuantity()));
            totalAmount = totalAmount.add(itemAmount);
            
            product.setStockQuantity(product.getStockQuantity() - itemDto.getQuantity());
            
            order.getOrderItems().add(orderItem);
        }
        
        order.setTotalAmount(totalAmount);
        order.setStatus(OrderStatus.CREATED);
        
        return orderRepository.save(order);
    }
}

// Good - DDD 패턴 적용
// 애플리케이션 서비스 - 유스케이스 오케스트레이션에 집중
@Service
@RequiredArgsConstructor
public class OrderService {
    private final OrderRepository orderRepository;
    private final UserRepository userRepository;
    private final ProductRepository productRepository;
    
    @Transactional
    public Order createOrder(Long userId, List<OrderItemDto> itemDtos) {
        // 엔티티 조회
        User user = userRepository.findById(userId)
            .orElseThrow(() -> new EntityNotFoundException("사용자를 찾을 수 없습니다"));
        
        // 도메인 객체에 비즈니스 로직 위임
        Order order = user.createOrder();
        
        // 각 상품에 대해 주문 항목 추가 (도메인 로직 호출)
        for (OrderItemDto itemDto : itemDtos) {
            Product product = productRepository.findById(itemDto.getProductId())
                .orElseThrow(() -> new EntityNotFoundException("상품을 찾을 수 없습니다"));
            
            product.decreaseStock(itemDto.getQuantity());
            order.addItem(product, itemDto.getQuantity());
        }
        
        // 저장
        return orderRepository.save(order);
    }
    
    @Transactional
    public void cancelOrder(Long orderId) {
        Order order = orderRepository.findById(orderId)
            .orElseThrow(() -> new EntityNotFoundException("주문을 찾을 수 없습니다"));
        
        // 도메인 로직을 엔티티에 위임
        order.cancel();
        
        // 주문 취소에 따른 재고 원복 (도메인 이벤트 처리로도 구현 가능)
        order.getOrderItems().forEach(item -> 
            item.getProduct().increaseStock(item.getQuantity()));
    }
}

// 도메인 서비스 - 여러 애그리거트에 걸친 로직, 외부 API와의 상호작용 등
@Service
@RequiredArgsConstructor
public class OrderPriceCalculationService {
    private final DiscountPolicyService discountPolicyService;
    
    // 여러 애그리거트 간의 로직이나 외부 의존성이 필요한 경우 도메인 서비스로 구현
    public Money calculateFinalPrice(Order order, User user) {
        Money totalPrice = order.getTotalAmount();
        
        // 회원 등급에 따른 할인 적용
        Money discount = discountPolicyService.applyUserDiscount(user.getMembershipLevel(), totalPrice);
        
        // 쿠폰, 프로모션 등 복잡한 할인 정책 적용
        return totalPrice.subtract(discount);
    }
}
```

## 컨트롤러 설계

컨트롤러는 클라이언트의 요청을 처리하고 결과를 반환하는 역할을 합니다.

#### 해결책

컨트롤러는 요청 유효성 검사와 응답 변환에 집중하고, 비즈니스 로직은 서비스에 위임하세요.

```java
// Bad
@RestController
@RequestMapping("/users")
public class UserController {
    private final UserRepository userRepository;
    
    public UserController(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    @PostMapping
    public User createUser(@RequestBody User user) {
        // 컨트롤러에서 바로 리포지토리 호출
        return userRepository.save(user);
    }
}

// Good
@RestController
@RequestMapping("/users")
@RequiredArgsConstructor
public class UserController {
    private final UserService userService;
    
    @PostMapping
    public ResponseEntity<UserResponseDto> createUser(@Valid @RequestBody UserCreateRequestDto request) {
        User user = userService.createUser(request.getName(), request.getEmail());
        UserResponseDto response = UserResponseDto.from(user);
        return ResponseEntity.status(HttpStatus.CREATED).body(response);
    }
}
```

## 트랜잭션 관리

트랜잭션은 비즈니스 요구사항에 맞게 적절한 격리 수준과 전파 속성을 설정해야 합니다.

#### 해결책

서비스 계층에서 트랜잭션을 관리하고, 읽기 전용 메소드에는 readOnly 속성을 활용하세요.

```java
// Bad
@Service
public class OrderService {
    // 트랜잭션 annotation 없음
    public Order createOrder(Long userId, List<OrderItem> items) {
        // ...
    }
    
    // 읽기 전용 메소드에 readOnly 미적용
    @Transactional
    public List<Order> findUserOrders(Long userId) {
        // ...
    }
}

// Good
@Service
public class OrderService {
    @Transactional
    public Order createOrder(Long userId, List<OrderItemDto> items) {
        // ...
    }
    
    @Transactional(readOnly = true)
    public List<Order> findUserOrders(Long userId) {
        // ...
    }
}
```

## JPA 쿼리 메소드

JPA 쿼리 메소드는 명명 규칙을 따라 가독성 있게 작성해야 합니다.

#### 해결책

메소드 이름은 find, query, get으로 시작하고, 조건은 By 키워드와 필드명을 사용하세요. 복잡한 쿼리는 QueryDSL을 활용하세요.

```java
// Bad
public interface ProductRepository extends JpaRepository<Product, Long> {
    // 명명 규칙을 따르지 않음
    List<Product> selectActiveProducts();
    
    // 복잡한 쿼리를 메소드 이름으로 표현
    List<Product> findByPriceGreaterThanEqualAndPriceLessThanEqualAndCategoryIdAndNameContaining(
        BigDecimal minPrice, BigDecimal maxPrice, Long categoryId, String name);
}

// Good
public interface ProductRepository extends JpaRepository<Product, Long>, QuerydslPredicateExecutor<Product> {
    List<Product> findByActiveTrue();
    
    // 복잡한 쿼리는 @Query 사용
    @Query("""
        SELECT p FROM Product p 
        WHERE p.price BETWEEN :minPrice AND :maxPrice 
        AND p.category.id = :categoryId 
        AND p.name LIKE %:name%
    """)
    List<Product> findProductsByPriceRangeAndCategoryAndName(
        @Param("minPrice") BigDecimal minPrice,
        @Param("maxPrice") BigDecimal maxPrice,
        @Param("categoryId") Long categoryId,
        @Param("name") String name);
}

// 더 복잡한 동적 쿼리는 QueryDSL 사용
@Repository
@RequiredArgsConstructor
public class ProductQueryRepository {
    private final JPAQueryFactory queryFactory;
    
    public List<Product> findProductsByComplexConditions(ProductSearchCriteria criteria) {
        return queryFactory
            .selectFrom(product)
            .leftJoin(product.category, category).fetchJoin()
            .where(
                isActiveEq(criteria.getActive()),
                priceGoe(criteria.getMinPrice()),
                priceLoe(criteria.getMaxPrice()),
                categoryEq(criteria.getCategoryId()),
                nameLike(criteria.getName())
            )
            .orderBy(product.name.asc())
            .fetch();
    }
    
    private BooleanExpression isActiveEq(Boolean active) {
        return active != null ? product.active.eq(active) : null;
    }
    
    private BooleanExpression priceGoe(BigDecimal minPrice) {
        return minPrice != null ? product.price.goe(minPrice) : null;
    }
    
    // 기타 조건 메소드 생략...
}
```

## 값 타입 사용

가능한 경우 값 타입을 활용하여 도메인 모델을 풍부하게 만드세요.

#### 해결책

공통적으로 사용되는 값들은 불변 값 객체로 추출하세요.

```java
// Bad
@Entity
public class Order {
    private String city;
    private String street;
    private String zipCode;
    
    private String paymentMethod;
    private String cardNumber;
    private String cardExpiry;
    
    public void setCity(String city) {
        this.city = city;
    }
    
    public void setPaymentMethod(String method) {
        this.paymentMethod = method;
    }
}

// Good
@Entity
@Table(name = "orders")
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Order {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Embedded
    private Address address;
    
    @Embedded
    @AttributeOverrides({
        @AttributeOverride(name = "method", column = @Column(name = "payment_method")),
        @AttributeOverride(name = "cardNumber", column = @Column(name = "card_number")),
        @AttributeOverride(name = "cardExpiry", column = @Column(name = "card_expiry"))
    })
    private Payment payment;
    
    @Builder
    public Order(Address address, Payment payment) {
        this.address = address;
        this.payment = payment;
    }
    
    // 값 객체는 불변이므로 변경 시 새로운 객체로 교체
    public void updateAddress(Address newAddress) {
        this.address = newAddress;
    }
    
    public void updatePayment(Payment newPayment) {
        this.payment = newPayment;
    }
}

@Embeddable
@Getter
@EqualsAndHashCode // 값 객체는 값이 같으면 동일하다고 판단하므로 equals/hashCode 구현
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Address {
    private String city;
    private String street;
    private String zipCode;
    
    @Builder
    public Address(String city, String street, String zipCode) {
        this.city = city;
        this.street = street;
        this.zipCode = zipCode;
    }
    
    // 불변 객체로 설계 - setter 대신 새 객체 생성
    public Address withCity(String newCity) {
        return new Address(newCity, this.street, this.zipCode);
    }
}

@Embeddable
@Getter
@EqualsAndHashCode
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Payment {
    private String method;
    private String cardNumber;
    private String cardExpiry;
    
    @Builder
    public Payment(String method, String cardNumber, String cardExpiry) {
        this.method = method;
        this.cardNumber = maskCardNumber(cardNumber);
        this.cardExpiry = cardExpiry;
    }
    
    private String maskCardNumber(String number) {
        if (number == null || number.length() < 4) {
            return number;
        }
        return "****-****-****-" + number.substring(number.length() - 4);
    }
}
```
