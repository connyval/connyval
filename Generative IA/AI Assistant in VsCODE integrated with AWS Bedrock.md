### AI Assistant in VsCODE integrated with AWS Bedrock

### Objetive:

Currently, I integrated VsCode framework tool with Amazon Bedrock trought an Artificial Inteligent Model, that it will allow me working and getting technical help by my personal IA assistant in the same workspace in VSCode. Additionaly count with a professional teacher or mentorship to learning more about AWS Cloud.

### Application Architecture:

-   VsCode Framework Tool
-   Amazon Bedrock – Amazon provider – Nova Micro Model
-   Amazon IAM – user, permission, role

### Final Implementation:

-   In the Amazon account, the Bedrock service is enabled using the Nova Micro model, taking both the model name and ID.
-   In the IAM service, a user is created with a profile for CLI (command line) connection, granting permission to invoke the Bedrock AI model. An Access Key is generated for this user.
-   In the VS Code console, the CONTINUE extension is installed and activated, which enables the connection and creation of assistants.
-   The AWS user is configured as a role within VS Code to allow connectivity to AWS.
-   The  _config.yaml_  file is verified, ensuring it contains the model details, parameters, and pre-defined system prompt for the assistant.
-   The  _config.yaml_  file is then copied to the default assistants directory of the CONTINUE module on the local machine—in this case, on Mac.
-   With this setup, a new assistant is enabled and connected to AWS Bedrock within the VS Code interface and workspace, providing support and guidance on AWS Cloud technical information

![Technical Desing](https://ocvpprofessional.cloud/wp-content/uploads/2025/08/vscode-ia2.png)