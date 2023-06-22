# This document contains bash commands to setup the reverse proxy on nginx server on ubuntu VM.
---
## TLDR; Install nginx server on Ubuntu:
> 
> yes Y | sudo apt install nginx; yes Y | sudo ufw allow 'Nginx HTTP'; yes Y | yes Y | sudo ufw enable; yes Y | sudo ufw status; yes Y | sudo systemctl restart nginx; curl http://$(curl ipinfo.io/ip);
---

### Steps:
- Create a VM on Azure portal
 	- Allow ports -  80, 443, and 22 while creating
 
 - Connect to VM using SSH
 	- "ssh username@vm-ip-address"
 
 - Install nginx server
	- sudo apt update
	- sudo apt install nginx

 - Allow HTTP traffic in ufw firewall:
 	- sudo ufw allow 'Nginx HTTP'

 - Enable ufw firewall
 	- sudo ufw enable	

 - Check ufw status	
 	- sudo ufw status

 - Restart your nginx server
 	- sudo systemctl restart nginx

 - Check VM IP address
 	- curl http://$(curl ipinfo.io/ip 

 - Test if nginx server is working
 	- navigate to http://Your-VM-IP-Address in the browser




# Map Custom domain to the VM and setup Https and SSL
---
 - Map an A record for given custom domain to the VM's IP address

 - nginx configuration- Use nano editor and update "server_name" in the nginx server configuration as custom-domain (www.example.com)
  - sudo nano /etc/nginx/sites-available/default
  	(find "server_name _" and update as "server_name www.example.com")
 
 - Save the configuration (ctrl+O --> Y), and Exit from nano editor (ctrl + X)

 - Verify the syntax of configuration file by running the following command. Observe if there's any error.
 	- sudo nginx -t

 - Restart nginx server
 	- sudo systemctl reload nginx

 - Allow HTTPS in ufw firewall and check the status
 	- sudo ufw allow 'Nginx Full'
	- sudo ufw delete allow 'Nginx HTTP'
	- sudo ufw status
 -- also check of port 443 is allowed on the Azure VM from portal > settings section.

 - Install Certbot and it's Nginx plugin
 	- sudo apt install certbot python3-certbot-nginx

 - Obtain SSL Certificate for the custom domain
 	- sudo certbot --nginx -d "custom-domain"

 - Restart your nginx server
 	- sudo systemctl restart nginx

 - Test if nginx server is working
 	- https://your_server_ip
----------------------------------------------------------------------------------

----------------------------------------------------------------------------------
## Setup reverse-proxy on '/docs' path:
----------------------------------------------------------------------------------
✅💡 sub-folder hosting works for public projects only, not for private projects.
 - Update the active nginx configuration - server block  as follow. update the helloworld.document360.io as the desired target documentation url.

 		location /docs {
                proxy_pass https://<helloworld.document360.io>/docs; 
                proxy_set_header        Host <helloworld.document360.io>;
                proxy_set_header        X-Real-IP $remote_addr;
                proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header        X-Forwarded-Proto $scheme;
                proxy_set_header        "requested-by" "proxy";
		
	    	# rewrite documentation path if using other than '/docs/
                sub_filter 'href="/docs' 'href="/help';

                # rewrite project url if inserted in the articles
                sub_filter 'href="https://freeparking.document360.io/docs' 'href="https://mycustom.domain.com/help';
                sub_filter_once off;
        }




