import org.docx4j.Docx4J;
import org.docx4j.convert.out.html.HTMLSettings;
import org.docx4j.openpackaging.packages.WordprocessingMLPackage;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;

import java.io.File;

public class WordToImageConverter {

    public static void main(String[] args) {
        // Path to your input Word document
        String inputFilePath = "path/to/your/document.docx";
        
        // Output HTML file path
        String outputHtmlPath = "path/to/save/document.html";

        // Output image file path
        String outputImagePath = "path/to/save/screenshot.png";

        // Initialize Selenium WebDriver
        WebDriver driver = setupWebDriver();

        try {
            // Load the Word document using docx4j
            WordprocessingMLPackage wordMLPackage = Docx4J.load(new File(inputFilePath));

            // Convert docx to HTML
            HTMLSettings htmlSettings = Docx4J.createHTMLSettings();
            htmlSettings.setWmlPackage(wordMLPackage);
            Docx4J.toHTML(htmlSettings, outputHtmlPath);

            // Load HTML content in Selenium WebDriver
            driver.get("file://" + new File(outputHtmlPath).getAbsolutePath());

            // Wait for the page to render (adjust wait time based on document complexity)
            Thread.sleep(5000); // Example: wait for 5 seconds (adjust as needed)

            // Capture screenshot of the rendered document
            File screenshot = ((TakesScreenshot) driver).getScreenshotAs(OutputType.FILE);
            FileUtils.copyFile(screenshot, new File(outputImagePath));

            System.out.println("Document screenshot saved: " + outputImagePath);

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Quit WebDriver instance
            driver.quit();
        }
    }

    private static WebDriver setupWebDriver() {
        // Setup Chrome WebDriver (adjust path to your ChromeDriver executable)
        System.setProperty("webdriver.chrome.driver", "path/to/chromedriver");

        // ChromeOptions to set headless mode (optional, remove if you want visible browser)
        ChromeOptions options = new ChromeOptions();
        options.addArguments("--headless"); // Comment this line to show the browser window

        // Initialize WebDriver
        WebDriver driver = new ChromeDriver(options);

        // Set implicit wait time
        driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);

        return driver;
    }
}
