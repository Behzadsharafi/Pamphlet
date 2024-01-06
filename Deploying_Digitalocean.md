- You can use https://www.youtube.com/watch?v=iGUNEnFZOgE&t=419s
  and https://studygyaan.com/spring-boot/deploy-spring-boot-app-on-vm-using-nginx-https-domain

- Create a database and add the configurations to the spring app.
- Follow postcodeAPI config
- Create a droplet (I used Ubuntu 20.04 (LTS) x64)
- Use SSH Key
- Open a terminal and enter ssh root@SERVER_IP_ADDRESS (The IP address for the droplet)
- Install Java Development Kit

run these lines one by one

```bash
sudo add-apt-repository ppa:linuxuprising/java -y
sudo apt update
sudo apt-get install oracle-java17-installer oracle-java17-set-default
```

- Install Maven

```bash
sudo apt install maven
```

- Install Nginx Web server

```bash
sudo apt update
sudo apt install nginx
sudo ufw allow 'Nginx Full'
systemctl status nginx
```

- Install git

```bash
sudo apt install git
```

- Clone the repo. Use https to get the link in the github if ssh doesn't work.

- Navigate to the folder and try to run the project (This might not work because of permissions)

```bash
./mvnw spring-boot:run
```

-If doesn't work run

```bash
chmod +x mvnw
```

- Now the project is running on "http://dropletaddress:8080"
  - for example "http://170.64.163.57:8080"
  - Use postman to make sure
- Buy a domain and change the Nameservers and add the domain to Digital Ocean
  - Click on My Account (top left)
  - Click on Domains
  - Click on the checkbox next to the domain
  - Click on Nameservers
- Rename the Nameservers as below (In GoDaddy)
  - ns1.digitalocean.com
  - ns2.digitalocean.com
  - ns3.digitalocean.com
- In Digital Ocean click on Create and then click on Domains/DNS
- Enter the domain name (example.com) and click on Add
- In the HOSTNAME type @ and in "WILL DIRECT TO" select the ubuntu server.
- Click on Create Record
- If you remove :8080 from the ip address in the browser you should see Welcome to nginx!

- Setup reverse proxy
  - Run this command

```bash
sudo vi /etc/nginx/sites-available/zademployee.store
```

- Modify and add the below code

```bash

server {
    server_name zademployee.store;
    index index.html index.htm;
    access_log /var/log/nginx/zademployee.log;
    error_log  /var/log/nginx/zademployee-error.log error;

    location / {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:8080;
        proxy_redirect off;
    }
}
```

- To exit press Esc and the type :wq

- Now enable the website.

```bash
sudo ln -s /etc/nginx/sites-available/zademployee.store /etc/nginx/sites-enabled/zademployee.store
```

- Let’s verify if there is no Nginx error, then reload it to take the changes into account:

```bash
sudo nginx -t
sudo nginx -s reload
```

- Lets re-run the project – ./mvnw spring-boot:run
- For easy spring boot project running i have written shell script, you can find in project: startup.sh, restart.sh and shutdown.sh (Get these from postcodeAPI )
- To run the project

```bash
 sh startup.sh
```

- To see what's happening in the background use

```bash
tail -f log.txt
```

- To stop the project

```bash
sh shutdown.sh
```

- Install Certbot, which is the tool responsible for certificate generation

```bash
sudo apt install snapd
sudo snap install --classic certbot
```

- Generate and install an SSL certificate for our domain. Please replace example.com with your domain name.

```bash
sudo certbot --nginx -d example.com
```

- Now to access the data we don't need 8080 anymore. Just type the custom address you bought with https.
- Update the allowed origins in web.config and add the frontend address
