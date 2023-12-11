# Playwrightium

## The story
I like Java. I like [Selenium](https://selenium.dev/). I like [Selenide](https://selenide.org/).
But also I like [Playwright Java](https://playwright.dev/java/). But I want to use all (or almost all) features of 
Playwright and continue using Selenide syntax.

What should I do?

I developed Playwrightium.

In general this is implementation of WebDriver interface using Playwright. Sounds crazy but it's true.

## How to install
### Maven
Add to your **pom.xml**

Add repository
```xml
 <repositories>
        <repository>
            <id>maven1</id>
            <url>https://repo1.maven.org/maven2/</url>
        </repository>
    </repositories>

```
Add dependency
```xml
<dependency>
    <groupId>io.github.britka</groupId>
    <artifactId>playwrightium</artifactId>
    <version>LAST_VERSION</version> 
</dependency>
```

### Gradle
```groovy
implementation 'io.github.britka:playwrightium:LAST_VERSION'
```

This dependency encapsulates 
* Selenide
* Playwright Java

## How to use as regular webdriver
You can use it like a regular `Webdriver`
For example:
```java
WebDriver driver = new PlaywrightiumDriver();
driver.get("https://example.com");
driver.findElement(By.name("username")).sendKeys("Some value");
driver.findElement(By.cssSelector("input[value=submit][name=submitbutton]")).click();
```
If you need more example please refer to the [playwrightium tests](src/test/java/org/brit/test/playwrightium/PlaywrightiumBasicTests.java)

> [!NOTE]
> For the first time [Playwright](https://playwright.dev/java/) will install browsers images:
* chromium
* firefox
* webkit (Safari)

After installation your test will run.

## What Playwrightium can do
* Navigate to url
```java
driver.get("https://examle.com");
```
* Search for element and element collections by different locators:
    * By.xpath
    * By.cssSelector
    * By.id
    * By.name
    * By.linkText
    * By.partialLinkText
    * By.className
    * By.tagName
    
For example:
```java
WebElement xpathElement = driver.findElement(By.xpath("//label[text()='Some text']"));
WebElement cssElement = driver.findElement(By.xpath("//label[text()='Some text']"));

List<WebElement> elementList = driver.findElements(By.name("any name"));
```

* Work with WebElements
For example
```java
var name = element.getAttribute("name");
element.click();
element.isDisplayed();
element.getText();
```

* Switch to frame
```java
driver.switchTo().frame("frameName");
```

* Works with Select webelement
```java
ISelect select = new PlaywrightiumSelect(driver.findElement(By.name("dropdown"))); 
select.selectByValue("dd" + faker.number().numberBetween(1, 7));
String selectValue = select.getFirstSelectedOption().getAttribute("value");
```
* Use waiters
```java
 new WebDriverWait(driver, Duration.ofSeconds(10))
                .until(ExpectedConditions.visibilityOfElementLocated(By.xpath("//h1[contains(.,'Processed Form Details')]")));
```

* Use alerts
> [!NOTE] 
> To use alerts you should know [how it works in Playwright](https://playwright.dev/java/docs/dialogs)

So the example will be
```java
// At first describe what we want to do with alert
Alert alert = driver.switchTo().alert();
alert.sendKeys(testString);
alert.accept();

// After we do some actions that will lead to alert appearance.
driver.findElement(By.id("promptexample")).click();

// After we can make some checks
assertThat(alert.getText()).isEqualTo("I prompt you");
```
* Use Actions class
```java
new Actions(driver).moveToElement(driver.findElement(By.id("someId"))).build().perform();
```
> [!IMPORTANT] 
> Some functionality is on WIP. So I cannot guarantee that all will work fine

* Run JavaScript scripts
```java
((JavascriptExecutor)driver).executeScript("return alert();");
```

## How to use it with Selenide
To use it with Selenide you should implement `WebDriverProvider` interface.
For example:
```java
public class PWDriverProvider implements WebDriverProvider {
    @Nonnull
    @Override
    public WebDriver createDriver(@Nonnull Capabilities capabilities) {
        PlaywrightWebdriverOptions chromeOptions = new PlaywrightWebdriverOptions();
        chromeOptions.merge(capabilities);
        return new PlaywrightiumDriver(chromeOptions);
    }
}
```
Then you should use it with `Configuration`
E.g.

```java
Configuration.browser = PWDriverProvider.class.getName();
```
And that's all. You can easily use it with Selenide.
See [test for Selenide](src/test/java/org/brit/test/selenide/SelenideBasicTests.java)

Enjoy!