#Selenium Java & Docker
This is a Dockerfile with the latest version of Google Chrome and the Google Chrome Web Driver along with the Java 16 
JDK. 

## Why
A project built in Java which requires Selenium, and a webdriver to be deployed, this is a simple way to get it done.

## Example use
If you wanted to use this as a multi-stage build file you'd just need to add two small sections to a Dockerfile. 

```dockerfile
# Build the project using Gradle
FROM gradle:jdk16 AS build
ADD . /home/gradle/src
WORKDIR /home/gradle/src
RUN gradle build

# Run the Jar 
FROM peavers/selenium-java:latest
RUN mkdir /app
COPY --from=build /home/gradle/src/build/libs/spring-boot-application.jar /app/spring-boot-application.jar
ENTRYPOINT ["java", "-jar", "/app/spring-boot-application.jar"]
```

## Configure your application
The web driver is ready and accessible at `/usr/local/bin/chromedriver` so however you configure the driver path in 
your application, point it there. 

If you're running the Java Selenium library through Springboot, here is an example configuration class:
```java
@Configuration
public class WebDriverConfig {

    @Bean
    public WebDriver webDriver() {

        final var options = new ChromeOptions();
        
        // Overwrite the values set in the Dockerfile.
        System.setProperty("webdriver.chrome.whitelistedIps", "");
        System.setProperty("webdriver.chrome.driver", "/usr/local/bin/chromedriver");
        
        // Set custom options for the webdriver.
        options.addArguments("--user-agent=Mozilla/5.0 (Macintosh; Intel Mac OS X 11_4) AppleWebKit/537.36 (KHTML,like Gecko) Chrome/91.0.4472.124 Safari/537.36");
        options.addArguments("--lang=en_US");
        options.addArguments("--window-size=3840,2160");
        options.addArguments("--disable-extensions");
        options.addArguments("--disable-dev-shm-usage");
        options.addArguments("--no-sandbox");
        options.addArguments("--disable-gpu");
        options.addArguments("--headless");

        return new ChromeDriver(options);
    }
}
```
