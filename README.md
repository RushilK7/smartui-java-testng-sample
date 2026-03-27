# SmartUI SDK Sample for Selenium Java (TestNG)

Welcome to the SmartUI SDK sample for Selenium Java with TestNG. This repository demonstrates how to integrate SmartUI visual regression testing with Selenium Java using TestNG framework.

## Get your SmartUI project and token

If you are new to SmartUI, follow [Running Your First Project on SmartUI](https://www.lambdatest.com/support/docs/smartui-running-your-first-project/) to create a project and obtain your **project token** and `smartui-web.json` configuration.

## Repository Structure

```
smartui-java-testng-sample/
├── src/
│   └── test/
│       └── java/
│           └── com/
│               └── lambdatest/
│                   └── sdk/
│                       ├── SmartUISDKCloud.java    # Cloud test
│                       └── SmartUISDKLocal.java    # Local test
├── sdk-cloud.xml                                    # TestNG suite for Cloud
├── sdk-local.xml                                    # TestNG suite for Local
├── pom.xml                                          # Maven dependencies
└── smartui-web.json                                # SmartUI config (create with npx smartui config:create)
```

## 1. Prerequisites and Environment Setup

### Prerequisites

- Java JDK 1.6 or higher (latest version recommended)
- Maven 3.6 or higher
- Node.js (for SmartUI CLI)
- LambdaTest account credentials (for Cloud tests)
- Chrome browser (for Local tests)

### Environment Setup

**For Cloud:**
```bash
export LT_USERNAME='your_username'
export LT_ACCESS_KEY='your_access_key'
export PROJECT_TOKEN='your_project_token'
```

**For Local:**
```bash
export PROJECT_TOKEN='your_project_token'
```

## 2. Initial Setup and Dependencies

### Clone the Repository

```bash
git clone https://github.com/LambdaTest/smartui-java-testng-sample
cd smartui-java-testng-sample
```

### Install Dependencies

The repository already includes the required dependencies in `pom.xml`. Build the project to download dependencies:

```bash
mvn clean compile
```

**Dependencies included:**
- `io.github.lambdatest:lambdatest-java-sdk` - SmartUI SDK for Selenium Java
- `org.testng:testng` - TestNG testing framework
- `org.seleniumhq.selenium:selenium-java` - Selenium WebDriver

### Install SmartUI CLI

```bash
npm i @lambdatest/smartui-cli
```

### Create SmartUI Configuration

```bash
npx smartui config:create smartui-web.json
```

## 3. Steps to Integrate Screenshot Commands into Codebase

The SmartUI screenshot function is already implemented in the repository.

**Cloud Test** (`src/test/java/com/lambdatest/sdk/SmartUISDKCloud.java`):
```java
driver.get("https://www.lambdatest.com");
SmartUISnapshot.smartuiSnapshot(driver, "screenshot");
```

**Local Test** (`src/test/java/com/lambdatest/sdk/SmartUISDKLocal.java`):
```java
driver.get("https://www.lambdatest.com");
SmartUISnapshot.smartuiSnapshot(driver, "screenshot");
```

**Note**: The code is already configured and ready to use. You can modify the URL and screenshot name if needed.

## 4. Execution and Commands

Pass `--config smartui-web.json` to the SmartUI CLI so it uses this repository’s config (project id, token path, and build naming). Place it **before** `exec` (it is a global CLI option).

### Local Execution

```bash
npx smartui --config smartui-web.json exec -- mvn test -D suite=sdk-local.xml
```

### Cloud Execution

```bash
npx smartui --config smartui-web.json exec -- mvn test -D suite=sdk-cloud.xml
```

## Test Files

### Cloud Test (`SmartUISDKCloud.java`)

- Connects to LambdaTest Cloud using Selenium Remote WebDriver
- Reads credentials from environment variables (`LT_USERNAME`, `LT_ACCESS_KEY`)
- Uses TestNG framework
- Takes screenshot with name: `screenshot`

### Local Test (`SmartUISDKLocal.java`)

- Runs Selenium locally using Chrome
- Requires Chrome browser installed
- Uses TestNG framework
- Takes screenshot with name: `screenshot`

## Configuration

### Maven Config (`pom.xml`)

The Maven configuration file includes all necessary dependencies for SmartUI and TestNG.

### TestNG Suites

- `sdk-cloud.xml` - TestNG suite for Cloud execution
- `sdk-local.xml` - TestNG suite for Local execution

### SmartUI Config (`smartui-web.json`)

Create the SmartUI configuration file using:
```bash
npx smartui config:create smartui-web.json
```

## Parallel Testing

To run parallel tests using TestNG:

```bash
mvn test -D suite=smartui-parallel.xml
```

## Testing Locally Hosted Projects

You can test locally hosted or privately hosted projects with LambdaTest Selenium grid using LambdaTest Tunnel. Refer to the [LambdaTest Tunnel documentation](https://www.lambdatest.com/support/docs/testing-locally-hosted-pages/) for more information.

## Best Practices

### Screenshot Naming

- Use descriptive, unique names for each screenshot
- Include test context and state (e.g., `homepage-logged-in`, `checkout-step-2`)
- Follow Java naming conventions (camelCase)
- Avoid special characters

### When to Take Screenshots

- After critical user interactions
- Before and after form submissions
- At different stages of multi-step processes
- After page state changes

### Java-Specific Tips

- Use explicit waits before taking screenshots
- Handle exceptions properly
- Use TestNG annotations for better test organization
- Leverage TestNG groups for parallel execution

### Example: Screenshot with Waits

```java
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
wait.until(ExpectedConditions.presenceOfElementLocated(By.id("main-content")));
SmartUISnapshot.smartuiSnapshot(driver, "homepage");
```

## Common Use Cases

### Multi-Step Flow Testing

```java
@Test
public void testCheckoutFlow() {
    driver.get("https://example.com/checkout");
    SmartUISnapshot.smartuiSnapshot(driver, "checkout-step-1");
    
    driver.findElement(By.id("next-step")).click();
    WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(5));
    wait.until(ExpectedConditions.elementToBeClickable(By.id("complete")));
    SmartUISnapshot.smartuiSnapshot(driver, "checkout-step-2");
}
```

### Conditional Screenshots

```java
@Test
public void testConditionalScreenshot() {
    driver.get("https://example.com");
    
    List<WebElement> userMenu = driver.findElements(By.className("user-menu"));
    if (!userMenu.isEmpty()) {
        SmartUISnapshot.smartuiSnapshot(driver, "homepage-logged-in");
    } else {
        SmartUISnapshot.smartuiSnapshot(driver, "homepage-guest");
    }
}
```

## CI/CD Integration

### GitHub Actions Example

```yaml
name: Java SmartUI Tests

on: [push, pull_request]

jobs:
  visual-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Java
        uses: actions/setup-java@v3
        with:
          java-version: '17'
          distribution: 'temurin'
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install SmartUI CLI
        run: npm install -g @lambdatest/smartui-cli
      
      - name: Build with Maven
        run: mvn clean compile
      
      - name: Run SmartUI tests
        env:
          PROJECT_TOKEN: ${{ secrets.SMARTUI_PROJECT_TOKEN }}
          LT_USERNAME: ${{ secrets.LT_USERNAME }}
          LT_ACCESS_KEY: ${{ secrets.LT_ACCESS_KEY }}
        run: |
          npx smartui --config smartui-web.json exec -- mvn test -D suite=sdk-cloud.xml
```

### Jenkins Pipeline Example

```groovy
pipeline {
    agent any
    
    tools {
        maven 'Maven-3.8'
        jdk 'JDK-17'
    }
    
    environment {
        PROJECT_TOKEN = credentials('smartui-project-token')
        LT_USERNAME = credentials('lambdatest-username')
        LT_ACCESS_KEY = credentials('lambdatest-access-key')
    }
    
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }
        
        stage('Visual Tests') {
            steps {
                sh 'npm install -g @lambdatest/smartui-cli'
                sh 'npx smartui --config smartui-web.json exec -- mvn test -D suite=sdk-cloud.xml'
            }
        }
    }
}
```

## Troubleshooting

### Issue: `Error: SmartUI Config already exists: smartui-web.json`

**Solution**: This is informational. The config file already exists. You can proceed or delete and recreate it.

### Issue: `PROJECT_TOKEN is required`

**Solution**: Ensure the environment variable is set:
```bash
export PROJECT_TOKEN='your_project_token'
# Verify
echo $PROJECT_TOKEN
```

### Issue: `Could not find or load main class`

**Solution**: Build the project first:
```bash
mvn clean compile
```

### Issue: `ChromeDriver version mismatch` (Local)

**Solution**: 
1. Update ChromeDriver in `pom.xml`
2. Ensure Chrome browser is up to date
3. Use WebDriverManager for automatic driver management

### Issue: `Unauthorized` error (Cloud)

**Solution**:
1. Verify `LT_USERNAME` and `LT_ACCESS_KEY` are set correctly
2. Check credentials in [LambdaTest Profile Settings](https://accounts.lambdatest.com/profile)
3. Ensure no extra spaces in environment variables

### Issue: TestNG suite not found

**Solution**: Verify the suite XML file exists and path is correct:
```bash
ls -la sdk-*.xml
mvn test -D suite=sdk-cloud.xml
```

## Configuration Tips

### TestNG Suite Configuration

**For Cloud** (`sdk-cloud.xml`):
```xml
<suite name="SmartUI Cloud Suite">
    <test name="Cloud Tests">
        <classes>
            <class name="com.lambdatest.sdk.SmartUISDKCloud"/>
        </classes>
    </test>
</suite>
```

**For Local** (`sdk-local.xml`):
```xml
<suite name="SmartUI Local Suite">
    <test name="Local Tests">
        <classes>
            <class name="com.lambdatest.sdk.SmartUISDKLocal"/>
        </classes>
    </test>
</suite>
```

### Maven Configuration Tips

Add to `pom.xml` for better dependency management:
```xml
<properties>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>
```

## Parallel Testing

### Running Parallel Tests

```bash
mvn test -D suite=smartui-parallel.xml
```

Configure parallel execution in TestNG suite XML:
```xml
<suite name="Parallel Suite" parallel="tests" thread-count="3">
    <test name="Test 1">
        <!-- Test configuration -->
    </test>
    <test name="Test 2">
        <!-- Test configuration -->
    </test>
</suite>
```

## View Results

After running the tests, visit your SmartUI project dashboard to view the captured screenshots and compare them with baseline builds.

## Additional Resources

- [SmartUI Selenium Java Onboarding Guide](https://www.lambdatest.com/support/docs/smartui-onboarding-selenium-java/)
- [LambdaTest Selenium Java Documentation](https://www.lambdatest.com/support/docs/selenium-java/)
- [TestNG Documentation](https://testng.org/doc/documentation-main.html)
- [Maven Documentation](https://maven.apache.org/guides/)
- [SmartUI Dashboard](https://smartui.lambdatest.com/)
- [LambdaTest Automation Dashboard](https://automation.lambdatest.com/)
- [LambdaTest Community](https://community.lambdatest.com/)
- [LambdaTest Blog](https://www.lambdatest.com/blog/)
- [LambdaTest Learning Hub](https://www.lambdatest.com/learning-hub/)
