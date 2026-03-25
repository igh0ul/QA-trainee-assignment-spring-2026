

## Тест-кейсы



---

###  Десктопная версия: Главная страница

| ID | Проверяемый функционал | Шаги воспроизведения | Ожидаемый результат |
| :--- | :--- | :--- | :--- |
| TC-01 | Фильтр «Диапазон цен» | 1. Ввести в поле «От» валидное число (напр., 1000).<br>2. Ввести в поле «До» валидное число (напр., 50000).<br>3. Применить фильтр. | В выдаче отображаются только те объявления, цена которых больше или равна 1000 ₽ и меньше или равна 50000 ₽. |
| TC-02 | Сортировка «По цене» | 1. В поле «Сортировать по» выбрать «Цене».<br>2. В поле «Порядок» выбрать «По возрастанию».<br>3. Изменить порядок на «По убыванию». | 1. Карточки отсортированы от самой дешевой к самой дорогой.<br>2. Карточки отсортированы от самой дорогой к самой дешевой. |
| TC-03 | Фильтр «Категория» | 1. Открыть дропдаун «Категория».<br>2. Выбрать любую категорию (например, «Электроника»). | В выдаче отображаются только карточки, у которых указана категория «Электроника». |
| TC-04 | Тогл «Только срочные» | 1. Активировать чекбокс/тогл « Только срочные». | В списке остаются исключительно те карточки, которые имеют бейдж «Срочно». Обычные объявления скрыты. |

---

###  Десктопная версия: Страница Статистики

| ID | Проверяемый функционал | Шаги воспроизведения | Ожидаемый результат |
| :--- | :--- | :--- | :--- |
| TC-05 | Управление таймером обновления | 1. Перейти на страницу /stats.<br>2. Нажать кнопку остановки таймера.<br>3. Нажать кнопку «Обновить».<br>4. Нажать кнопку запуска таймера. | 1. Обратный отсчет таймера останавливается на текущем значении.<br>2. Данные статистики визуально обновляются, таймер сбрасывается на начальное значение.<br>3. Отсчет времени возобновляется. |

---

###  Мобильная версия

| ID | Проверяемый функционал | Шаги воспроизведения | Ожидаемый результат |
| :--- | :--- | :--- | :--- |
| TC-06 | Переключение тем | 1. Открыть сайт с шириной экрана менее 768px (мобильный вид).<br>2. Нажать на кнопку переключения темы в навигационном меню. | 1. Цветовая схема сайта меняется на противоположную (светлая на темную или наоборот).<br>2. Текст и иконка на самой кнопке меняются в соответствии с текущей или доступной темой. |


## Автотетсты

### TC-06

#### ThemeToggleTest.java

import io.github.bonigarcia.wdm.WebDriverManager;
import org.openqa.selenium.By;
import org.openqa.selenium.Dimension;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
import org.testng.Assert;
import org.testng.annotations.*;

import java.time.Duration;

public class ThemeToggleTest {

    private WebDriver driver;
    private WebDriverWait wait;
    private final String URL = "https://cerulean-praline-8e5aa6.netlify.app/";

    @BeforeMethod
    public void setUp() {
        WebDriverManager.chromedriver().setup();

        ChromeOptions options = new ChromeOptions();
        options.addArguments("--window-size=375,812");
        options.addArguments("--user-agent=Mozilla/5.0 (iPhone; CPU iPhone OS 16_0 like Mac OS X)");

        driver = new ChromeDriver(options);
        wait = new WebDriverWait(driver, Duration.ofSeconds(10));

        driver.get(URL);
    }

    @Test
    public void testThemeToggle() throws InterruptedException {
        System.out.println(" Проверяем переключение темы...");

        Thread.sleep(5000);

        WebElement themeButton = wait.until(ExpectedConditions.elementToBeClickable(
                By.cssSelector("button._themeToggle_127us_1")
        ));

        String initialAriaLabel = themeButton.getAttribute("aria-label");
        System.out.println(" Исходный aria-label: " + initialAriaLabel);

        Thread.sleep(5000);

        themeButton.click();
        System.out.println(" Кликнули по кнопке (1 раз)...");

        Thread.sleep(5000);

        wait.until(ExpectedConditions.not(
                ExpectedConditions.attributeToBe(themeButton, "aria-label", initialAriaLabel)
        ));

        String newAriaLabel = themeButton.getAttribute("aria-label");
        System.out.println(" Новый aria-label: " + newAriaLabel);

        Assert.assertTrue(
                newAriaLabel.contains("dark") || newAriaLabel.contains("тем"),
                " Тема не переключилась! Ожидали 'dark', получили: " + newAriaLabel
        );

        Thread.sleep(5000);

        themeButton.click();
        System.out.println("🖱️ Кликнули по кнопке (2 раз)...");

        Thread.sleep(5000);

        wait.until(ExpectedConditions.not(
                ExpectedConditions.attributeToBe(themeButton, "aria-label", newAriaLabel)
        ));

        String finalAriaLabel = themeButton.getAttribute("aria-label");
        Assert.assertEquals(
                finalAriaLabel,
                initialAriaLabel,

        );

        System.out.println(" Тест пройден! Кнопка переключения темы работает.");

        Thread.sleep(5000);
    }

    @AfterMethod
    public void tearDown() {
        if (driver != null) {
            driver.quit();
        }
    }
}


#### testing.xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="ThemeToggleSuite">
    <test name="ThemeToggleTest">
        <classes>
            <class name="ThemeToggleTest"/>
        </classes>
    </test>
</suite>






#### pom.xml 

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>theme-test</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <!-- Selenium -->
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-java</artifactId>
            <version>4.18.1</version>
        </dependency>

        <!-- WebDriverManager -->
        <dependency>
            <groupId>io.github.bonigarcia</groupId>
            <artifactId>webdrivermanager</artifactId>
            <version>5.6.3</version>
        </dependency>

        <!-- TestNG -->
        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>7.8.0</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <!-- Плагин для запуска тестов -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.2.5</version>
                <configuration>
                    <suiteXmlFiles>
                        <suiteXmlFile>testng.xml</suiteXmlFile>
                    </suiteXmlFiles>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>






