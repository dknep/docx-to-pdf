# docx-to-pdf

import com.lowagie.text.Document;
import com.lowagie.text.Paragraph;
import com.lowagie.text.pdf.PdfWriter;
import org.apache.poi.hwpf.HWPFDocument;
import org.apache.poi.hwpf.extractor.WordExtractor;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class DocToPdfConverter {

    public static void main(String[] args) throws IOException {
        convertDocToPdf("input.doc", "output.pdf");
    }

    public static void convertDocToPdf(String docFilePath, String pdfFilePath) throws IOException {
        // Read the DOC file using Apache POI
        FileInputStream fis = new FileInputStream(docFilePath);
        HWPFDocument doc = new HWPFDocument(fis);
        WordExtractor extractor = new WordExtractor(doc);

        // Create a PDF document
        Document pdfDoc = new Document();
        PdfWriter.getInstance(pdfDoc, new FileOutputStream(pdfFilePath));
        pdfDoc.open();

        // Extract text from DOC and add it to PDF
        String[] paragraphs = extractor.getParagraphText();
        for (String paragraph : paragraphs) {
            pdfDoc.add(new Paragraph(paragraph));
        }

        // Close the document
        pdfDoc.close();
        extractor.close();
        fis.close();

        System.out.println("DOC to PDF conversion successful.");
    }
}
