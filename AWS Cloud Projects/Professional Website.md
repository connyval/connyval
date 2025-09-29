## Professional Website

### Objetive:

This website is based on my professional experience and was implemented using AWS 2-tiers cloud architecture, enabling me to showcase my knowledge and skills while continuing to grow in my career as a cloud professional.

### Application Architecture:

-   Amazon VPC (SUBNETS, SECURITY GROUPS, INTERNET GATEWAY)
-   Amazon EC2 (linux) deploy DOCKER Containers – Webserver Apache
-   AWS Certificate Manager
-   Amazon Route 53
-   Amazon Application Load Balancer
-   CMS WordPresss – Profile Website Template

### Final Implementation:

-   Created an Amazon VPC network to isolate the project’s resources, including public subnets across Availability Zones in the Virginia region.
-   Configured properly NCLs in the public subnet and integrated an Internet Gateway to enable secure internet access for by EC2.
-   Deployed an Amazon EC2 virtual machine using a lightweight Linux AMI with minimal CPU, memory, and storage resources, tailored for hosting a personal portfolio website. Docker containers were launched using Docker Compose (Apache as webserver), including two containers for the WordPress application and a MySQL database.
-   Registered and configured the DNS zone and basic records in Amazon Route 53 to route traffic for the primary domain: **ocvpprofessional.cloud**.
-   Set up an SSL certificate through AWS Certificate Manager for the primary domain, enabling secure communication over port 443 (HTTPS).
-   Configured secure routing via Amazon Application Load Balancer, using the primary domain and HTTPS protocol. This setup also masked the public IP address, enhancing the security and privacy of the website.
-   Finally, launched the professional website in production using the latest version of WordPress CMS, applying a high-quality theme to reflect a strong professional image and brand online.

![Professional Website ](https://ocvpprofessional.cloud/wp-content/uploads/2025/07/Mysite.png)
