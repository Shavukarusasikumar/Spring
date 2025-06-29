
# 🌱 Spring Framework

This document gives you a quick, simple, and in-depth understanding of the core Spring concepts with examples.

---

## 🔁 1. What is Inversion of Control (IoC)?

Instead of creating objects manually, Spring creates and manages them.

```java
// Without IoC
class Car {
    Engine engine = new Engine(); // Manual creation
}

// With IoC
class Car {
    Engine engine; // Spring provides it
}
```

---

## 💉 2. What is Dependency Injection (DI)?

Providing required objects (dependencies) from outside.

```java
@Component
class Engine {}

@Component
class Car {
    @Autowired
    private Engine engine;
}
```

---

## 🔧 3. What is a Bean?

A **bean** is a Java object managed by Spring.

```java
@Component
class Engine {}
```

```java
@Configuration
class AppConfig {
    @Bean
    public Engine engine() {
        return new Engine();
    }
}
```

---

## 🧠 4. What is the Spring Container?

A factory that manages your beans.

| Container Type     | Description |
|--------------------|-------------|
| `BeanFactory`      | Basic container |
| `ApplicationContext` | Advanced, commonly used ✅ |

```java
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
```

---

## 🧰 5. Important Annotations

| Annotation | Purpose |
|-----------|---------|
| `@Component` | Marks a class as a Spring-managed bean |
| `@Autowired` | Injects a dependency |
| `@Configuration` | Declares configuration class |
| `@Bean` | Declares a bean method |
| `@ComponentScan` | Scans packages for `@Component` classes |
| `@Primary` | Default when multiple beans of same type |
| `@Qualifier("name")` | Inject a specific bean |

---

## 🔌 6. Types of Dependency Injection

### ✅ Constructor Injection
```java
@Component
class Car {
    private final Engine engine;
    @Autowired
    Car(Engine engine) { this.engine = engine; }
}
```

### ✅ Setter Injection
```java
@Component
class Car {
    private Engine engine;
    @Autowired
    public void setEngine(Engine engine) {
        this.engine = engine;
    }
}
```

### ✅ Field Injection
```java
@Component
class Car {
    @Autowired
    private Engine engine;
}
```

---

## 💡 7. When to Use Which Injection

| Type        | Mutable? | Best For                |
|-------------|----------|-------------------------|
| Constructor | ❌ No    | Required dependencies ✅ |
| Setter      | ✅ Yes   | Optional/configurable ⚠️ |
| Field       | ✅ Yes   | Quick demos only ❌     |

---

## 🎯 8. Component Scan Example

```java
@Component
class Engine {}

@Component
class Car {
    @Autowired
    Engine engine;
}

@Configuration
@ComponentScan("com.example")
class AppConfig {}
```

---

## 🔀 9. Primary vs Qualifier

```java
@Component
@Primary
class DieselEngine {}

@Component
class PetrolEngine {}

@Autowired
@Qualifier("petrolEngine")
Engine engine;
```

---

## 📦 10. Java Bean vs Spring Bean vs POJO

| Type        | Description |
|-------------|-------------|
| POJO        | Regular Java class |
| Java Bean   | POJO with setters/getters |
| Spring Bean | Managed by Spring |

---

## 🔁 11. When Beans are Created

> Beans are created when `ApplicationContext` is initialized.

```java
var context = new AnnotationConfigApplicationContext(AppConfig.class);
```

---

## 🧪 12. Testability

Constructor injection allows easy testing:

```java
Car car = new Car(mockEngine); // Easy to test ✅
```

---

## 🧠 Summary Table

| Concept       | Explanation |
|---------------|-------------|
| IoC           | Spring controls object creation |
| DI            | Spring gives needed objects |
| Bean          | Object managed by Spring |
| Container     | Manages beans |
| @Component    | Declares a class as a bean |
| @Autowired    | Injects a dependency |
| Constructor Injection | Required, immutable |
| Setter Injection | Optional, changeable |
| Field Injection | Easy but hidden, not testable |

# 🌱 Spring Bean Scopes

In Spring, **scope** defines **how many times** and **how long** a bean is created and used in the application.

---

## ✅ What is Bean Scope?

> A bean scope controls **how many instances** of a bean Spring creates and **how it is shared**.

Example:
- 🔁 Should Spring reuse the same object (`singleton`)?
- 🆕 Or should it create a new object every time (`prototype`)?

---

## 📦 Types of Bean Scopes

| Scope        | Description | Context |
|--------------|-------------|---------|
| `singleton`  | **(Default)** Only one instance per Spring container | All apps |
| `prototype`  | A **new instance** every time the bean is requested | All apps |
| `request`    | One bean per HTTP request | Web apps |
| `session`    | One bean per HTTP session | Web apps |
| `application`| One bean per ServletContext (web application) | Web apps |
| `websocket`  | One bean per WebSocket session | Web apps with WebSocket |

---

## 🔁 `singleton` (Default)

- Only **one bean object** created for entire Spring container
- All requests get **same instance**
- Memory efficient, stateless

```java
@Component
@Scope("singleton") // or omit this; it's the default
public class Engine {}

