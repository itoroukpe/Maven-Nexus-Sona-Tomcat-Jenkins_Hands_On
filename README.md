# Maven_hands_on.md

Certainly! Here's a clear, step-by-step practical guide for building a Java application using the Maven build tool, designed for beginners:

---

### Step-by-Step Hands-On Guide: Building a Java Application with Maven

### Prerequisites
Ensure you have:
- [Java JDK (Java 11+)](https://adoptium.net/)
- [Apache Maven](https://maven.apache.org/download.cgi)
- Your favorite IDE (Eclipse, IntelliJ IDEA, VS Code, etc.)

Check installations in your terminal:
```bash
java -version
mvn -version
```

---

## Step 1: Create a New Maven Project from Scratch

### Command-line Method:
Open your terminal and run:

```bash
mvn archetype:generate -DgroupId=com.example -DartifactId=MyApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

- `groupId`: Your package namespace (e.g., com.example).
- `artifactId`: Your application's name (e.g., MyApp).

This creates a Maven-based Java project named **MyApp**.

---

## Step 2: Explore Maven Project Structure
Navigate to your new project directory:
```bash
cd MyApp
```

Your project structure will be:
```
MyApp/
├── pom.xml
└── src
    ├── main
    │   └── java
    │       └── com
    │           └── example
    │               └── App.java
    └── test
        └── java
            └── com
                └── example
                    └── AppTest.java
```

- **`src/main/java`**: Your Java source code.
- **`src/test/java`**: Your test code.
- **`pom.xml`**: Maven project configuration file.

---

## Step 3: Configure Dependencies in `pom.xml`

Open the `pom.xml` file, and add dependencies as needed.

Example: adding JUnit for testing (usually already included):

```xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13.2</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

Save changes.

---

## Step 4: Write Java Code

Open `src/main/java/com/example/App.java`:

```java
package com.example;

public class App {
    public static void main(String[] args) {
        System.out.println("Hello, Maven!");
    }

    public static int add(int a, int b) {
        return a + b;
    }
}
```

---

## Step 5: Write a Unit Test (JUnit)

Navigate to `src/test/java/com/example/AppTest.java` and modify it:

```java
package com.example;

import static org.junit.Assert.assertEquals;
import org.junit.Test;

public class AppTest {

    @Test
    public void testAdd() {
        assertEquals(5, App.add(2, 3));
    }
}
```

---

## Step 6: Build Your Project with Maven

From your project's root directory (`MyApp`), build your project by running:
```bash
mvn compile
```

This compiles your Java source code.

---

## Step 7: Run Unit Tests

Execute your unit tests with:
```bash
mvn test
```

Output should confirm your test passed:
```
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
```

---

## Step 8: Package Your Application as a JAR

To package your Java application into an executable JAR file, run:
```bash
mvn package
```

The resulting JAR file will be created at:
```
target/MyApp-1.0-SNAPSHOT.jar
```

---

## Step 9: Run Your JAR File

Run your application from terminal using:
```bash
java -cp target/MyApp-1.0-SNAPSHOT.jar com.example.App
```

Output should be:
```
Hello, Maven!
```

---

## Step 10: Cleanup (Optional)

To remove compiled files and reset your project workspace:
```bash
mvn clean
```

---

### Summary of Common Maven Commands:
| Command                 | Description                           |
|-------------------------|---------------------------------------|
| `mvn compile`           | Compile Java sources                  |
| `mvn test`              | Run unit tests                        |
| `mvn package`           | Package compiled code into JAR/WAR    |
| `mvn clean`             | Clean up compiled files               |

---

## Additional Learning Points:
- Explore Maven central repository: [https://search.maven.org](https://search.maven.org)
- Understanding Maven Lifecycle: validate → compile → test → package → verify → install → deploy
- IDEs (IntelliJ, Eclipse) provide GUI-based support for Maven commands.

---

