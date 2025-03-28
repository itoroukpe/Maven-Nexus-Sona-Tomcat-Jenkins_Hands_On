Here's how you can quickly create and start a Spring Boot application step-by-step:

### 1. **Prerequisites**

Ensure you have the following installed:

- **JDK** (Java 8 or higher)
- **Maven or Gradle** (build tool)
- **IDE** (IntelliJ IDEA, Eclipse, or VSCode recommended)

---

### 2. **Creating a New Spring Boot Project**

#### Option A: **Using Spring Initializr (recommended)**

- Visit [start.spring.io](https://start.spring.io/).
- Select:
  - **Project**: Maven or Gradle
  - **Language**: Java
  - **Spring Boot Version**: latest stable
  - **Dependencies**: Select dependencies as needed (e.g., Spring Web for REST APIs).
- Click on **Generate** to download a zip file containing the project structure.

Unzip the downloaded file and open it in your IDE.

#### Option B: **Using IDE directly**

Most IDEs (IntelliJ IDEA, Eclipse) have built-in support for Spring Boot project creation:

- **IntelliJ IDEA**:
  - File → New → Project → Spring Initializr → Follow instructions to set up.
  
- **Eclipse (with STS)**:
  - File → New → Spring Starter Project → Follow instructions to set up.

---

### 3. **Understand the Basic Structure**

Once generated, your project structure will typically look like this:

```
src
 ├── main
 │    ├── java
 │    │    └── com/example/demo
 │    │        └── DemoApplication.java
 │    └── resources
 │         └── application.properties
 └── test
      └── java
           └── com/example/demo
                └── DemoApplicationTests.java
```

- **DemoApplication.java** (contains `main` method, entry point)
- **application.properties** (configuration file)

---

### 4. **Sample Spring Boot Application**

Your main class should look similar to this:

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}

}
```

---

### 5. **Run Your Application**

- **IDE**: Right-click your main class (`DemoApplication.java`) → Run as Java Application or Spring Boot App.
- **Command Line**: Navigate to your project directory and run:

  - **Maven**:
    ```bash
    ./mvnw spring-boot:run
    ```

  - **Gradle**:
    ```bash
    ./gradlew bootRun
    ```

---

### 6. **Verify Application Startup**

By default, your Spring Boot application starts on port **8080**.

Visit:
```
http://localhost:8080
```

You may get a **"Whitelabel Error Page"** unless you define a controller.

---

### 7. **Add a Simple Controller (Optional Example)**

To verify your setup quickly, add a simple REST controller:

```java
package com.example.demo;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

	@GetMapping("/")
	public String hello() {
		return "Hello, Spring Boot!";
	}
}
```

Now, restart your application and visit:
```
http://localhost:8080
```

You should see:
```
Hello, Spring Boot!
```

---

### 8. **Next Steps (Optional)**

- Add dependencies for databases, security, etc.
- Configure logging and properties in `application.properties`.

---

🎯 **You’ve now successfully started a basic Spring Boot application!**
