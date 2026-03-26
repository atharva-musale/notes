# Spring framework

## Maven

### What is Maven?

Maven is a tool that helps you manage Java projects. Think of it as an assistant that handles the repetitive tasks of building and organizing your code.

When you write a Java application, you need to:
- Compile your code (turn .java files into .class files)
- Download external libraries your project needs
- Run tests to make sure everything works
- Package everything into a single file you can run

Maven automates all of these tasks for you. Instead of doing everything manually, you just tell Maven what your project needs, and it takes care of the rest.

### The pom.xml File

Every Maven project has a file called `pom.xml` (Project Object Model). This is like a recipe card for your project. It tells Maven:
- What your project is called
- What version it is
- What external libraries (dependencies) it needs

Here's a simple example:
```xml
<project>
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0-SNAPSHOT</version>
    
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>3.2.0</version>
        </dependency>
    </dependencies>
</project>
```

**Understanding the parts:**
- `groupId`: Usually your company or organization name (like com.example)
- `artifactId`: The name of your project (like my-app)
- `version`: Which version of your project this is
- `dependencies`: The list of external libraries your project needs

### How Maven Builds Your Project

Maven follows a series of steps called the "build lifecycle". You don't need to remember all of these, but here are the main ones:

1. **validate** - Checks if your project setup is correct
2. **compile** - Converts your .java files into .class files that can run
3. **test** - Runs your automated tests to check if everything works
4. **package** - Bundles everything into a JAR or WAR file (a single file you can distribute)
5. **install** - Saves this package to your computer so other projects can use it
6. **deploy** - Uploads the package to a shared server so your team can use it

When you run a Maven command like `mvn package`, Maven automatically runs all the steps before it too. So `mvn package` will validate, compile, test, and then package your code.

### Managing Dependencies (External Libraries)

One of Maven's best features is dependency management. When your project needs an external library (like Spring or JUnit), you just add it to your pom.xml file.

Maven then:
1. Downloads the library from the internet (usually from Maven Central Repository)
2. Saves it to your computer (in a folder called `.m2/repository` in your home directory)
3. Makes it available to your project

If that library needs other libraries to work (called transitive dependencies), Maven downloads those too automatically.

### Project Folder Structure

Maven expects your project to be organized in a specific way. This makes it easy for anyone to understand your project structure:

```
my-project/
├── src/
│   ├── main/
│   │   ├── java/         Your actual Java code goes here
│   │   └── resources/    Configuration files (like application.properties)
│   └── test/
│       ├── java/         Your test code goes here
│       └── resources/    Test configuration files
├── target/               Maven puts compiled code here (generated automatically)
└── pom.xml              The project configuration file
```

You don't have to create this structure manually - Maven commands or your IDE can set it up for you.

### Common Commands You'll Use

Here are the Maven commands you'll use most often:

```bash
mvn clean           # Deletes the target folder (removes old compiled files)
mvn compile         # Compiles your Java code
mvn test            # Runs all your tests
mvn package         # Creates a JAR or WAR file you can run
mvn install         # Saves the package to your local computer
mvn clean install   # Cleans old files and builds everything fresh
mvn spring-boot:run # Runs a Spring Boot application directly
```

### Why Use Maven?

- **No manual downloads**: Maven downloads all libraries automatically
- **Consistent structure**: All Maven projects are organized the same way
- **Easy to share**: Other developers can build your project with one command
- **Saves time**: Automates repetitive build tasks
- **Works everywhere**: Same commands work on Windows, Mac, and Linux

## Dependency Injection with Beans

### What is Dependency Injection?

Dependency Injection (DI) is a design pattern where objects don't create the things they need - instead, those things are provided to them from outside.

Think of it like ordering food at a restaurant:
- **Without DI**: You go to the kitchen, find ingredients, and cook your own meal
- **With DI**: You order from a menu, and the restaurant brings you the finished meal

In code terms, if Class A needs to use Class B:
- **Without DI**: Class A creates a new instance of Class B inside itself
- **With DI**: Someone else creates Class B and gives it to Class A

**Example without DI:**
```java
public class Car {
  private Engine engine;
  
  public Car() {
    this.engine = new Engine(); // Car creates its own engine
  }
}
```

**Example with DI:**
```java
public class Car {
  private Engine engine;
  
  public Car(Engine engine) {
    this.engine = engine; // Engine is provided from outside
  }
}
```

### What are Beans?

In Spring, a **Bean** is simply an object that Spring creates and manages for you. Think of Spring as a factory that builds objects and keeps track of them.

When you mark a class as a bean, you're telling Spring:
1. "Please create an instance of this class for me"
2. "Keep it ready so I can use it whenever I need it"
3. "If other classes need it, give them this same instance"

**Creating a Bean:**
```java
@Configuration
public class AppConfig {
  @Bean
  public Engine engine() {
    return new Engine(); // Spring will create and manage this Engine
  }
  
  @Bean
  public Car car(Engine engine) {
    return new Car(engine); // Spring automatically provides the Engine bean
  }
}
```

### How Spring Manages Beans

Spring keeps all beans in something called the **Application Context** (or IoC Container). This is like a warehouse where Spring stores all the objects it creates.

When your application starts:
1. Spring scans your code for classes marked as beans
2. Creates instances of these classes
3. Stores them in the Application Context
4. When one bean needs another, Spring injects it automatically

### Types of Dependency Injection

**1. Constructor Injection (Recommended)**
```java
@Component
public class UserService {
  private final UserRepository userRepository;
  
  public UserService(UserRepository userRepository) {
    this.userRepository = userRepository;
  }
}
```
The dependency is provided through the constructor. This is the preferred method because it makes required dependencies clear.

**2. Setter Injection**
```java
@Component
public class UserService {
  private UserRepository userRepository;
  
  @Autowired
  public void setUserRepository(UserRepository userRepository) {
    this.userRepository = userRepository;
  }
}
```
The dependency is provided through a setter method. Useful for optional dependencies.

**3. Field Injection (Not Recommended)**
```java
@Component
public class UserService {
  @Autowired
  private UserRepository userRepository;
}
```
Spring directly injects into the field. While convenient, it makes testing harder and hides dependencies.

### Bean Scopes

Beans can have different lifecycles, called **scopes**:

- **Singleton (Default)**: Spring creates only one instance and reuses it everywhere
  ```java
  @Bean
  @Scope("singleton")
  public Engine engine() {
    return new Engine();
  }
  ```

- **Prototype**: Spring creates a new instance every time someone asks for it
  ```java
  @Bean
  @Scope("prototype")
  public Car car() {
    return new Car();
  }
  ```

- **Request** (Web apps only): New instance for each HTTP request
- **Session** (Web apps only): New instance for each user session

### Benefits of Dependency Injection

1. **Easier Testing**: You can easily replace real objects with test objects
   ```java
   // In tests, provide a fake UserRepository
   UserService service = new UserService(new FakeUserRepository());
   ```

2. **Loose Coupling**: Classes don't need to know how to create their dependencies
3. **Flexibility**: Easy to swap implementations without changing code
4. **Centralized Configuration**: All object creation happens in one place

## Annotations for dependency injection

Spring uses annotations (special markers that start with @) to configure dependency injection. These tell Spring how to create and inject beans.

### Core DI Annotations

**@Component**

Marks a class as a Spring bean. Spring will automatically create an instance of this class.

```java
@Component
public class EmailService {
  public void sendEmail(String message) {
    // Send email logic
  }
}
```

When your application starts, Spring creates an `EmailService` bean automatically.

**@Autowired**

Tells Spring to inject a dependency automatically. Spring finds the matching bean and provides it.

```java
@Component
public class UserService {
  private final EmailService emailService;
  
  @Autowired  // Optional on constructors if there's only one constructor
  public UserService(EmailService emailService) {
    this.emailService = emailService;
  }
  
  public void registerUser(String user) {
    // Register user
    emailService.sendEmail("Welcome!");
  }
}
```

Spring sees that `UserService` needs an `EmailService`, finds the `EmailService` bean, and injects it.

### Stereotype Annotations

These are specialized versions of `@Component` that give more meaning to your classes:

**@Service**

Use for business logic classes (the main functionality of your application).

```java
@Service
public class OrderService {
  public void processOrder(Order order) {
    // Business logic for processing orders
  }
}
```

**@Repository**

Use for data access classes (classes that interact with databases).

```java
@Repository
public class UserRepository {
  public User findById(Long id) {
    // Database query to find user
  }
}
```

Bonus: `@Repository` also translates database exceptions into Spring's exception hierarchy.

**@Controller**

Use for web controllers that handle HTTP requests.

```java
@Controller
public class HomeController {
  @GetMapping("/")
  public String home() {
    return "home"; // Returns view name
  }
}
```

**@RestController**

Combines `@Controller` and `@ResponseBody`. Use for REST APIs that return data (usually JSON).

```java
@RestController
public class UserController {
  @GetMapping("/users")
  public List<User> getUsers() {
    return userList; // Automatically converted to JSON
  }
}
```

### Configuration Annotations

**@Configuration**

Marks a class as a source of bean definitions. Use with `@Bean` methods.

```java
@Configuration
public class AppConfig {
    
  @Bean
  public DataSource dataSource() {
    // Create and configure a database connection
    return new DataSource();
  }
}
```

**@Bean**

Used inside `@Configuration` classes to explicitly define beans. Useful for third-party classes you can't annotate.

```java
@Configuration
public class AppConfig {
  @Bean
  public ObjectMapper objectMapper() {
    ObjectMapper mapper = new ObjectMapper();
    mapper.configure(SerializationFeature.INDENT_OUTPUT, true);
    return mapper;
  }
}
```

### Handling Multiple Implementations

Sometimes you have multiple beans of the same type. Spring needs to know which one to inject.

**@Primary**

Marks the default bean to use when multiple options exist.

```java
@Component
@Primary
public class EmailNotification implements NotificationService {
  // This will be used by default
}

@Component
public class SmsNotification implements NotificationService {
  // This is available but not the default
}
```

**@Qualifier**

Specifies exactly which bean to inject by name.

```java
@Component
public class NotificationManager {
  private final NotificationService notificationService;
  
  @Autowired
  public NotificationManager(
    @Qualifier("smsNotification") NotificationService notificationService
  ) {
    this.notificationService = notificationService;
  }
}
```

### Property Injection

**@Value**

Injects values from properties files into your beans.

```java
@Component
public class AppSettings {
  @Value("${app.name}")
  private String appName;
  
  @Value("${app.version}")
  private String version;
  
  @Value("${server.port:8080}") // 8080 is default if not specified
  private int port;
}
```

In your `application.properties`:
```properties
app.name=MyApp
app.version=1.0.0
server.port=9000
```

**@ConfigurationProperties**

Binds a group of properties to a Java object.

```java
@Component
@ConfigurationProperties(prefix = "database")
public class DatabaseConfig {
    private String url;
    private String username;
    private String password;
    
    // Getters and setters
}
```

In `application.properties`:
```properties
database.url=jdbc:mysql://localhost:3306/mydb
database.username=root
database.password=secret
```

### Conditional Beans

**@Conditional Annotations**

Create beans only when certain conditions are met.

```java
@Configuration
public class ConditionalConfig {
    
    @Bean
    @ConditionalOnProperty(name = "feature.enabled", havingValue = "true")
    public FeatureService featureService() {
        return new FeatureService(); // Only created if feature.enabled=true
    }
    
    @Bean
    @ConditionalOnMissingBean
    public DefaultService defaultService() {
        return new DefaultService(); // Only if no other DefaultService bean exists
    }
}
```

### Lifecycle Annotations

**@PostConstruct**

Runs after the bean is created and dependencies are injected. Use for initialization logic.

```java
@Component
public class DataLoader {
    @Autowired
    private DatabaseService databaseService;
    
    @PostConstruct
    public void init() {
        // This runs after databaseService is injected
        databaseService.loadInitialData();
    }
}
```

**@PreDestroy**

Runs before the bean is destroyed. Use for cleanup logic.

```java
@Component
public class ConnectionManager {
    private Connection connection;
    
    @PreDestroy
    public void cleanup() {
        // Close connections before shutdown
        if (connection != null) {
            connection.close();
        }
    }
}
```

### Quick Reference

| Annotation | Purpose |
|------------|--------|
| `@Component` | General Spring bean |
| `@Service` | Business logic layer |
| `@Repository` | Data access layer |
| `@Controller` | Web MVC controller |
| `@RestController` | REST API controller |
| `@Autowired` | Inject dependency |
| `@Qualifier` | Specify which bean to inject |
| `@Primary` | Default bean when multiple exist |
| `@Value` | Inject property value |
| `@Configuration` | Define beans with @Bean methods |
| `@Bean` | Explicit bean definition |

## Auto configuration annotation

### What is Auto Configuration?

Auto configuration is Spring Boot's way of automatically setting up your application based on what libraries you have in your project. It's like Spring Boot saying, "I see you have a database library - let me set up database connections for you automatically."

Without auto configuration, you would need to manually configure every component. With Spring Boot, it does most of the work for you.

### @EnableAutoConfiguration

This annotation tells Spring Boot to start auto-configuring your application based on the dependencies in your classpath.

```java
@EnableAutoConfiguration
@ComponentScan
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

However, you rarely use `@EnableAutoConfiguration` directly because it's already included in `@SpringBootApplication`.

### @SpringBootApplication

This is the most important annotation for Spring Boot applications. It combines three annotations:

```java
@SpringBootApplication
// Is equivalent to:
// @EnableAutoConfiguration
// @ComponentScan
// @Configuration
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

**What each part does:**
- `@EnableAutoConfiguration`: Automatically configures your application
- `@ComponentScan`: Finds and registers your beans
- `@Configuration`: Allows you to define additional beans

### How Auto Configuration Works

Spring Boot looks at your dependencies and makes smart decisions:

**Example 1: Database Auto Configuration**
If you add a database dependency to your `pom.xml`:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

Spring Boot automatically:
- Sets up a DataSource (database connection)
- Configures JPA/Hibernate
- Creates a transaction manager
- Sets up entity scanning

**Example 2: Web Application Auto Configuration**
If you add the web starter:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

Spring Boot automatically:
- Starts an embedded Tomcat server
- Configures Spring MVC
- Sets up JSON message converters
- Configures error handling

### Customizing Auto Configuration

You can override auto configuration with your own settings in `application.properties` or `application.yml`.

**application.properties:**
```properties
# Override default server port
server.port=9090

# Database configuration
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=secret
```

**application.yml:**
```yaml
server:
  port: 9090

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mydb
    username: root
    password: secret
```

### @Conditional Annotations

These annotations control when auto configuration happens. Spring Boot uses them internally, but you can use them too.

**@ConditionalOnClass**

Configuration only applies if a certain class is present in the classpath.

```java
@Configuration
@ConditionalOnClass(DataSource.class)
public class DatabaseConfig {
    // This config only loads if DataSource is available
    @Bean
    public DataSource dataSource() {
        return new HikariDataSource();
    }
}
```

**@ConditionalOnMissingBean**

Only creates a bean if one doesn't already exist. This lets you override Spring Boot's defaults.

```java
@Configuration
public class MyConfig {
    @Bean
    @ConditionalOnMissingBean
    public RestTemplate restTemplate() {
        // Spring Boot uses this only if you haven't defined your own RestTemplate
        return new RestTemplate();
    }
}
```

**@ConditionalOnProperty**

Configuration applies only if a specific property is set.

```java
@Configuration
@ConditionalOnProperty(name = "feature.enabled", havingValue = "true")
public class FeatureConfig {
    // Only loads if feature.enabled=true in application.properties
    @Bean
    public FeatureService featureService() {
        return new FeatureService();
    }
}
```

**Other Conditional Annotations:**
- `@ConditionalOnMissingClass`: Applies when a class is NOT present
- `@ConditionalOnBean`: Applies when a specific bean exists
- `@ConditionalOnResource`: Applies when a specific file exists
- `@ConditionalOnWebApplication`: Applies only in web applications
- `@ConditionalOnNotWebApplication`: Applies only in non-web applications

### Excluding Auto Configuration

Sometimes you don't want certain auto configurations. You can exclude them:

**Method 1: Using @SpringBootApplication**
```java
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

**Method 2: Using application.properties**
```properties
spring.autoconfigure.exclude=org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
```

This is useful when you want manual control over certain configurations.

### Viewing Auto Configuration Report

Spring Boot can show you what auto configurations were applied and why:

**In application.properties:**
```properties
debug=true
```

Or run your application with:
```bash
java -jar myapp.jar --debug
```

This prints a report showing:
- Positive matches (configurations that were applied)
- Negative matches (configurations that were not applied and why)
- Exclusions (configurations you explicitly excluded)
- Unconditional classes (always applied)

### Creating Custom Auto Configuration

You can create your own auto configuration for reusable components.

**Step 1: Create a Configuration Class**
```java
@Configuration
@ConditionalOnClass(MyLibrary.class)
public class MyLibraryAutoConfiguration {
    
    @Bean
    @ConditionalOnMissingBean
    public MyLibrary myLibrary() {
        return new MyLibrary();
    }
}
```

**Step 2: Register It**
Create `src/main/resources/META-INF/spring.factories`:
```properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.example.MyLibraryAutoConfiguration
```

Now when someone adds your library to their project, it will be auto-configured.

### Benefits of Auto Configuration

1. **Less boilerplate**: No need to write repetitive configuration code
2. **Convention over configuration**: Sensible defaults that work out of the box
3. **Easy to override**: Can customize anything when needed
4. **Fast development**: Get started quickly without configuration
5. **Best practices**: Spring Boot configures things the recommended way

### Best Practices

1. **Start with defaults**: Let Spring Boot auto-configure everything first
2. **Override only when needed**: Only customize what you actually need to change
3. **Use application.properties**: Prefer properties over code configuration when possible
4. **Check the auto configuration report**: Use `--debug` to understand what's happening
5. **Exclude unused configurations**: If you're not using certain features, exclude them for faster startup

## Component scanning

### What is Component Scanning?

Component scanning is how Spring automatically finds and registers beans in your application. Instead of manually telling Spring about every class, you let Spring search for them.

Think of it like Spring walking through your code looking for classes marked with special annotations (`@Component`, `@Service`, `@Repository`, etc.) and automatically creating beans for them.

### How It Works

When your Spring application starts:
1. Spring starts from a base package (usually where your main class is)
2. Looks through all classes in that package and sub-packages
3. Finds classes marked with stereotype annotations
4. Creates beans for those classes
5. Sets up dependency injection between them

### Enabling Component Scanning

**Spring Boot Way (Automatic)**

If you're using Spring Boot, component scanning is enabled automatically. The `@SpringBootApplication` annotation includes component scanning.

```java
@SpringBootApplication  // This includes @ComponentScan
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

Spring Boot scans:
- The package containing `MyApplication`
- All sub-packages under it

**Example structure:**
```
com.example.myapp/
├── MyApplication.java          (Main class with @SpringBootApplication)
├── controller/
│   └── UserController.java     (Will be scanned)
├── service/
│   └── UserService.java        (Will be scanned)
└── repository/
    └── UserRepository.java     (Will be scanned)
```

All classes under `com.example.myapp` are automatically scanned.

**Traditional Spring Way (Manual)**

Without Spring Boot, you use `@ComponentScan` explicitly.

```java
@Configuration
@ComponentScan("com.example.myapp")  // Scan this package and sub-packages
public class AppConfig {
}
```

### Customizing Component Scanning

**Scanning Multiple Packages**

```java
@Configuration
@ComponentScan(basePackages = {"com.example.services", "com.example.repositories"})
public class AppConfig {
}
```

**Type-Safe Package Scanning**

Instead of using string package names (which can break during refactoring), reference classes:

```java
@Configuration
@ComponentScan(basePackageClasses = {UserService.class, OrderService.class})
public class AppConfig {
}
```

Spring will scan the packages containing these classes.

**Excluding Components**

Tell Spring to skip certain classes:

```java
@Configuration
@ComponentScan(
    basePackages = "com.example",
    excludeFilters = @ComponentScan.Filter(
        type = FilterType.ANNOTATION,
        classes = Deprecated.class
    )
)
public class AppConfig {
}
```

This skips any class marked with `@Deprecated`.

**Including Only Specific Components**

```java
@Configuration
@ComponentScan(
    basePackages = "com.example",
    includeFilters = @ComponentScan.Filter(
        type = FilterType.ANNOTATION,
        classes = Service.class
    ),
    useDefaultFilters = false  // Turn off default scanning
)
public class AppConfig {
}
```

This only scans classes marked with `@Service`.

### Filter Types

You can filter components in different ways:

**1. By Annotation**
```java
@ComponentScan(
    excludeFilters = @ComponentScan.Filter(
        type = FilterType.ANNOTATION,
        classes = Controller.class
    )
)
```
Excludes all classes with `@Controller`.

**2. By Class Type**
```java
@ComponentScan(
    excludeFilters = @ComponentScan.Filter(
        type = FilterType.ASSIGNABLE_TYPE,
        classes = LegacyService.class
    )
)
```
Excludes `LegacyService` and all its subclasses.

**3. By Regular Expression**
```java
@ComponentScan(
    excludeFilters = @ComponentScan.Filter(
        type = FilterType.REGEX,
        pattern = "com\\.example\\..*Test"
    )
)
```
Excludes all classes ending with "Test".

**4. By Custom Filter**
```java
public class CustomFilter implements TypeFilter {
    @Override
    public boolean match(MetadataReader reader, MetadataReaderFactory factory) {
        // Custom logic to decide if class should be included
        return reader.getClassMetadata().getClassName().contains("Special");
    }
}

@ComponentScan(
    includeFilters = @ComponentScan.Filter(
        type = FilterType.CUSTOM,
        classes = CustomFilter.class
    )
)
```

### Common Pitfalls

**1. Component Not Found**

If Spring can't find your component:
- Make sure it's in the scanned package or a sub-package
- Check that it has a stereotype annotation (`@Component`, `@Service`, etc.)
- Verify your main class is in the root package

**Wrong:**
```
com.example.main/
└── Application.java            (@SpringBootApplication)
com.example.services/           (Different root - won't be scanned!)
└── UserService.java
```

**Right:**
```
com.example/
├── Application.java            (@SpringBootApplication)
└── services/
    └── UserService.java        (Will be scanned)
```

**2. Circular Dependencies**

When two components depend on each other:
```java
@Service
public class ServiceA {
    @Autowired
    private ServiceB serviceB;  // ServiceA needs ServiceB
}

@Service
public class ServiceB {
    @Autowired
    private ServiceA serviceA;  // ServiceB needs ServiceA - CIRCULAR!
}
```

Solution: Redesign your code, use setter injection, or `@Lazy` annotation.

**3. Scanning Too Many Packages**

Scanning large packages slows down startup:
```java
// Avoid scanning from the root
@ComponentScan("")  // Scans everything - very slow!
```

Be specific about what packages to scan.

### Component Naming

By default, Spring creates a bean name by uncapitalizing the class name:
- `UserService` becomes `userService`
- `EmailSender` becomes `emailSender`

You can specify a custom name:
```java
@Component("myCustomName")
public class UserService {
}
```

Then reference it by name:
```java
@Autowired
@Qualifier("myCustomName")
private UserService service;
```

### Performance Considerations

Component scanning happens at startup:
- **More classes to scan = slower startup**
- For large applications, consider splitting into modules
- Use specific base packages instead of scanning everything
- In Spring Boot, component scanning is optimized and usually fast enough

### Best Practices

1. **Keep your main class at the root** of your package structure
2. **Use meaningful package names** (`controller`, `service`, `repository`)
3. **Don't scan external libraries** - they usually provide their own configuration
4. **Use stereotype annotations appropriately** (`@Service` for services, `@Repository` for data access)
5. **Avoid circular dependencies** by designing better class relationships
6. **Be specific with @ComponentScan** if you need custom scanning behavior

### Checking What Was Scanned

You can see all beans Spring created:
```java
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        ApplicationContext context = SpringApplication.run(MyApplication.class, args);
        
        String[] beanNames = context.getBeanDefinitionNames();
        for (String beanName : beanNames) {
            System.out.println(beanName);
        }
    }
}
```

This prints all bean names Spring found and created, helping you verify that component scanning worked correctly.


