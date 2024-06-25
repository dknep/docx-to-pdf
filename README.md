import org.apache.poi.xwpf.usermodel.*;
import org.openqa.selenium.*;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;

import java.io.*;
import java.util.concurrent.TimeUnit;

public class WordToImageConverter {

    public static void main(String[] args) {
        // Path to your input Word document
        String inputFilePath = "path/to/your/document.docx";
        
        // Output image file path
        String outputImagePath = "path/to/save/screenshot.png";

        // Initialize Selenium WebDriver
        WebDriver driver = setupWebDriver();

        try {
            // Load the Word document using Apache POI
            XWPFDocument document = new XWPFDocument(new FileInputStream(inputFilePath));

            // Extract content and convert to HTML
            String htmlContent = convertToHTML(document);

            // Load HTML content in Selenium WebDriver
            driver.get("data:text/html," + htmlContent);

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

    private static String convertToHTML(XWPFDocument document) {
        // Convert XWPFDocument to HTML (simplified example, adjust as needed)
        StringWriter writer = new StringWriter();
        XWPFToHTMLConverter converter = new XWPFToHTMLConverter(document, writer, null);
        converter.processDocument();
        return writer.toString();
    }
}
