## Extraction data from PDF with AI-powered automated process

### Objetive:

Read (OCR scan) a PDF document containing shipping information, where an automated AI process using OpenAI LLM model, structures the required data into JSON format, which is then exported to Google Sheets for analysis and cross-referencing with the business’s bank transactions. This enables detailed control over shipments delivered to customers

### Application Architecture:

-   **GOOGLE DRIVE:** stores PDF documents and allow it to download documents to carry out the process
-   **PDF.CO:** platform that provides API for scan and extract data
-   **OPENAI:** Model LLM (GPT-4.o Mini 2024) and structure them in JSON format
-   **GOOGLE SHEET:** to export final the scanned data from PDF

### Final Implementation:

-   First, PDF documents containing shipping information are uploaded to a GOOGLE DRIVE folder, which stores the files and allows them to be downloaded for processing.

-   Next, the required data is scanned from the PDF using the PDF.co platform API and extracted as plain text.

-   Then, the OpenAI LLM model (GPT-4.o Mini 2024) is used to precisely extract the necessary data and structure it in JSON format, properly labeling each value.

-   Finally, the structured JSON data is mapped and exported to GOOGLE SHEETS in order to cross-reference it with the business’s bank transactions and maintain control over shipments delivered to customers.”
- 
![Technical Desing](https://ocvpprofessional.cloud/wp-content/uploads/2025/07/1_Escenario-MAKE-2048x712.png)