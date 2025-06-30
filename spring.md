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

| Container Type     | Description         |
|--------------------|---------------------|
| `BeanFactory`      | Basic container     |
| `ApplicationContext` | Advanced, commonly used ✅ |

```java
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
```

---

## 🧰 5. Important Annotations

| Annotation         | Purpose                                |
|--------------------|----------------------------------------|
| `@Component`       | Marks a class as a Spring-managed bean |
| `@Autowired`       | Injects a dependency                   |
| `@Configuration`   | Declares configuration class           |
| `@Bean`            | Declares a bean method                 |
| `@ComponentScan`   | Scans packages for `@Component` classes|
| `@Primary`         | Default when multiple beans of same type |
| `@Qualifier("name")` | Inject a specific bean              |

---

## 🔌 6. Types of Dependency Injection

### ✅ Constructor Injection

```java
@Component
class Car {
    private final Engine engine;
    @Autowired
    Car(Engine engine) {
        this.engine = engine;
    }
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

| Type        | Mutable? | Best For                  |
|-------------|----------|---------------------------|
| Constructor | ❌ No    | Required dependencies ✅   |
| Setter      | ✅ Yes   | Optional/configurable ⚠️   |
| Field       | ✅ Yes   | Quick demos only ❌        |

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

| Type        | Description                 |
|-------------|-----------------------------|
| POJO        | Regular Java class          |
| Java Bean   | POJO with setters/getters   |
| Spring Bean | Managed by Spring           |

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

| Concept            | Explanation                           |
|--------------------|----------------------------------------|
| IoC                | Spring controls object creation        |
| DI                 | Spring gives needed objects            |
| Bean               | Object managed by Spring               |
| Container          | Manages beans                          |
| @Component         | Declares a class as a bean             |
| @Autowired         | Injects a dependency                   |
| Constructor Injection | Required, immutable               |
| Setter Injection   | Optional, changeable                   |
| Field Injection    | Easy but hidden, not testable          |

---

# 🌱 Spring Bean Scopes

In Spring, **scope** defines **how many times** and **how long** a bean is created and used in the application.

---

## ✅ What is Bean Scope?

> A bean scope controls **how many instances** of a bean Spring creates and **how it is shared**.

Examples:
- 🔁 Should Spring reuse the same object (`singleton`)?
- 🆕 Or should it create a new object every time (`prototype`)?

---

## 📦 Types of Bean Scopes

| Scope         | Description                             | Context        |
|---------------|-----------------------------------------|----------------|
| `singleton`   | **(Default)** Only one instance per Spring container | All apps |
| `prototype`   | A **new instance** every time the bean is requested | All apps |
| `request`     | One bean per HTTP request               | Web apps       |
| `session`     | One bean per HTTP session               | Web apps       |
| `application` | One bean per ServletContext             | Web apps       |
| `websocket`   | One bean per WebSocket session          | WebSocket apps |

---

## 🔁 `singleton` (Default)

- Only **one bean object** created for entire Spring container
- All requests get **same instance**
- Memory efficient, stateless

```java
@Component
@Scope("singleton") // or omit this; it's the default
public class Engine {}
```

---

# 📘 Hibernate, JDBC, and JPA – Short Notes Based on Your Two Repository Classes

---

## 🔍 What is Hibernate?

**Hibernate** is a **Java ORM (Object-Relational Mapping)** framework.

> It lets you map Java classes (like `Course`) to database tables without writing SQL manually.

### ✅ Features of Hibernate:

- Converts Java objects into database rows and vice versa
- Automatically generates SQL queries (behind the scenes)
- Supports relationships (OneToMany, ManyToOne)
- Handles transactions, caching, and lazy loading
- Works with JPA (Java Persistence API)

---

## 🧠 Your Two Repository Classes Compared (JDBC vs Hibernate/JPA)

---

### 1️⃣ `CourseJdbcRepository` – Uses **Spring JDBC**

```java
@Repository
public class CourseJdbcRepository {
    @Autowired
    private JdbcTemplate jdbcTemplate;

    public void insert(Course course) {
        jdbcTemplate.update("INSERT INTO course (id, name) VALUES (?, ?)", course.getId(), course.getName());
    }

    public void delete(Course course) {
        jdbcTemplate.update("DELETE FROM course WHERE id = ?", course.getId());
    }

    public Course getData(int id) {
        return jdbcTemplate.queryForObject("SELECT * FROM course WHERE id = ?", 
                new BeanPropertyRowMapper<>(Course.class), id);
    }
}
```

#### 🔑 Key Points:

- You **write SQL manually**
- `JdbcTemplate` simplifies query execution
- You control exactly how data is fetched
- You need to manually map results (`BeanPropertyRowMapper`)

---

### 2️⃣ `CourseJpaRepository` – Uses **JPA with Hibernate**

```java
@Repository
public class CourseJpaRepository {

    @PersistenceContext
    private EntityManager entityManager;

    @Transactional
    public void insert(Course course) {
        entityManager.merge(course); // insert or update
    }

    @Transactional
    public void delete(Course course) {
        if (entityManager.contains(course)) {
            entityManager.remove(course);
        } else {
            System.out.println("Course not found");
        }
    }

    @Transactional
    public void deletById(int id) {
        Course course = entityManager.find(Course.class, id);
        entityManager.remove(course);
    }

    public void findById(int id) {
        Course course = entityManager.find(Course.class, id);
        System.out.println(course.getId() + " " + course.getName());
    }
}
```

#### 🔑 Key Points:

- Hibernate uses **Java objects** to perform DB operations
- `EntityManager` handles data operations (CRUD)
- `@Transactional` ensures DB operations are safely committed
- Uses **no SQL**; works with entity classes and annotations

---

## ⚖️ JDBC vs Hibernate/JPA – Quick Comparison

| Feature             | JDBC (`CourseJdbcRepository`)       | Hibernate/JPA (`CourseJpaRepository`)   |
|---------------------|-------------------------------------|-----------------------------------------|
| SQL Required         | ✅ Yes                              | ❌ No (uses Java entities)               |
| Framework Used       | Spring JDBC                        | JPA + Hibernate                          |
| Mapping              | Manual (`BeanPropertyRowMapper`)   | Auto via annotations (`@Entity`)        |
| Insert/Update        | `jdbcTemplate.update()`            | `entityManager.merge()`                 |
| Delete               | SQL delete query                   | `entityManager.remove()`                |
| Read                 | SQL select query                   | `entityManager.find()`                  |
| Flexibility          | High (you control SQL)             | High (you control objects/relations)    |
| Boilerplate Code     | More                               | Less                                    |

---

# 📚 What is Spring Data JPA?

**Spring Data JPA** is a part of Spring that makes database work easier using **JPA**.

You just write a simple interface, and Spring auto-generates the code to talk to the database.

---

## ✅ Example Repository

```java
@Repository
public interface CourseSpringDataEntityRepository extends JpaRepository<Course, Integer> {
    List<Course> findByName(String name);
}
```

### 🔍 How does `findByName` work?

- Spring looks at `findByName(String name)`
- It reads: "find all Course records where `name = ?`"
- It creates the SQL behind the scenes:
  
```sql
SELECT * FROM course WHERE name = ?
```

---

## ✅ Why Use Spring Data JPA?

| Feature               | Benefit                            |
|------------------------|------------------------------------|
| No boilerplate code    | No need to write SQL manually      |
| Auto CRUD methods      | `save()`, `findAll()`, `delete()`  |
| Custom query support   | With method names or `@Query`      |
| Works with Hibernate   | Hibernate runs behind the scenes   |

---

## ✅ Entity Example

```java
@Entity
public class Course {
    @Id
    @GeneratedValue
    private Integer id;
    private String name;
}
```

---

## ✅ Custom Query Method

```java
List<Course> findByName(String name);
```

or

```java
@Query("SELECT c FROM Course c WHERE c.name = :name")
List<Course> searchByName(@Param("name") String name);
```

---

## ✅ Common Built-in Methods

```java
findAll();
findById(id);
save(course);
deleteById(id);
```

---

# 🔍 JPA vs Hibernate (Simple)

### ✅ What is JPA?

- JPA is just **rules** (an interface)
- It says how to map Java classes to DB tables
- JPA **can’t do anything by itself**

🧠 Think: **Remote control**

---

### ✅ What is Hibernate?

- Hibernate is a **tool** that follows JPA rules
- It actually **connects and runs SQL**
- Hibernate adds **extra features** (caching, etc.)

🧠 Think: **TV**

---

## ✅ Summary

| JPA           | Hibernate             |
|---------------|-----------------------|
| Interface     | Implementation        |
| Only rules    | Full working tool     |
| Needs provider| Is the provider       |
| Portable code | Extra features        |

---

✅ In Spring Boot, when you use Spring Data JPA — Hibernate is used **by default** to do the work.

