### MCP Server for IA Chat with Amazon Q Cli / VsCODE improving my DEV journey

### Objetive:
First, what is  **MCP Server?**  
“MCP, or Model Context Protocol, is an open-source protocol developed by Anthropic and supported by AWS. It acts as a ‘universal translator’ for artificial intelligence. AWS has integrated MCP to enable AI applications to access and use AWS tools, databases, and services. It operates with a client-server architecture, where clients (such as AI assistants) connect to MCP servers (which expose AWS data and services), allowing language models to interact securely and efficiently with the AWS environment”.

An amazing tool! I implemented MCP to interact with an AI Chat using Sonnet models. Specifically, I used MCP for cost analysis and optimization, enabling me to investigate my resources and workloads, understand and analyze costs in detail, identify potential technical optimizations, and determine key aspects to monitor. This has become my powerful AI assistant for managing and controlling AWS cloud expenses.

### Application Architecture:

-   AWS MCP Server – Sonnet Model
-   AWS Q Cli and VsCode Framework Tool
-   Amazon IAM: user, role, permissions to Cost Explorer API

### Final Implementation:

-   Initially, based on the AWS MCP Servers guide, the MCP for Cost Explorer analysis was selected.
-   Amazon Q CLI was installed to enable functionalities, dependencies, and components on the local Mac machine.
-   In the AWS account, a user and role were created, granting permissions for the Cost Explorer API to allow the user to connect to account resources.
-   A profile was then created with the user’s credentials, including the access key and secret key, for use in the Terminal and VsCode.
-   The JSON file located at /.aws/amazonq/mcp.json on the local machine was configured with the selected MCP server code, including the assigned user for connection to the AWS account.
-   The MCP server was successfully integrated for use via Terminal and VSCode.
-   Finally, it was activated through AWS Q CLI in Chat, enabling natural language queries to the AI for AWS cost analysis, management, and optimization.

[Technical Desing](https://ocvpprofessional.cloud/wp-content/uploads/2025/08/aws_q_cli.png)
