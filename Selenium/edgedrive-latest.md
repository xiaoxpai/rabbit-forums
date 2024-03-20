

- edgedriver-latest
 - https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver



- java-example
 ```java
public class EdgeDriverExampleTest {

    @BeforeAll
    public static void setUp() {
        System.setProperty("webdriver.edge.driver", "src/test/resources/driver/msedgedriver.exe");
    }

    @Test
    public void test(){
        WebDriver driver = new EdgeDriver();
        driver.get("https://www.baidu.com");
        driver.getTitle();
        driver.manage().timeouts().implicitlyWait(Duration.ofMillis(500));
//        driver.quit();
    }
}
 ```