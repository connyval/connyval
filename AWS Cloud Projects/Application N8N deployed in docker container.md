## Application N8N deployed in docker container

### Objetive:

The N8N application was deployed to create automations and integrations with Artificial Intelligence. It was implemented using a Docker container with the subdomain [https://n8n.ocvpprofessional.cloud/](https://n8n.ocvpprofessional.cloud/), including a secure server certificate. An Application Load Balancer was also configured to orchestrate requests for two applications running under the same balancer.

### Application Architecture:

-   Amazon VPC (SUBNETS, SECURITY GROUPS, INTERNET GATEWAY)
-   Amazon EC2 (linux) deploy DOCKER Container – Ngnix webserver
-   AWS Certificate Manager
-   Amazon Route 53
-   Amazon Application Load Balancer – (2) Target Groups
-   Deployed N8N application

### Final Implementation:

-   In Amazon VPC network to isolate the project’s resources, including both public and private subnets across Availability Zones in the Virginia region.
-   Configured properly NCLs in the public subnet and integrated an Internet Gateway to enable secure internet access for by EC2.
-   Deployed an Amazon EC2 virtual machine the N8N applicattion by Docker and NGNIX as webserver, generating one container for application and database.
-   Configured properly inbound and outbound ports to access through Security Groups, for availble http and https protocols.
-   Registered and configured the DNS zone and basic records in Amazon Route 53 to route traffic for the subdomain: **n8n.ocvpprofessional.cloud**.
-   Set up an SSL certificate through AWS Certificate Manager for subdomain, enabling secure communication over port 443 (HTTPS).
-   Configured (2) Target groups in Application Load Balancer, because there are main proffesional website and N8N aplication in the same hosting.
-   Finally, Application Load Balancer serves traffic to two websites by https protocols without conflict.

### Technical Desing:

![Technical Desing](https://ocvpprofessional.cloud/wp-content/uploads/2025/08/APP_N8N_LBL.png)