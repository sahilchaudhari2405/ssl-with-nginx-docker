# Deploying a Secure Docker Setup on AWS EC2 with Route 53

## **Step 1: Launch an EC2 Instance**
1. Log in to your AWS Management Console.
2. Navigate to **EC2 Dashboard** → Click on **Launch Instance**.
3. Choose **Ubuntu 22.04 LTS** (or any preferred Linux distribution).
4. Select an instance type (**t2.micro** for free tier).
5. Configure security group:
   - Open **Port 80 (HTTP)** and **Port 443 (HTTPS)** for public access.
   - Open **Port 22 (SSH)** for connecting to the instance.
6. Click **Launch** and create/select an SSH key pair.

## **Step 2: Connect to the EC2 Instance**
```bash
ssh -i your-key.pem ubuntu@your-ec2-public-ip
```

## **Step 3: Install Docker, Docker Compose, and Git**
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y docker.io git
sudo systemctl enable --now docker
```
Install Docker Compose:
```bash
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

## **Step 4: Clone the Repository**
```bash
git clone https://github.com/YOUR_GITHUB_USERNAME/YOUR_REPO.git
cd YOUR_REPO
```

## **Step 5: Configure Route 53 for Your Domain**
1. Go to **AWS Route 53** → **Create Hosted Zone**.
2. Enter your domain name (e.g., `yourdomain.com`).
3. Copy the **Name Servers (NS)** provided.
4. Go to your domain registrar (e.g., GoDaddy, Namecheap) and update the **NS records**.

### **Set Up an A Record**
1. In Route 53, go to **Hosted Zones** → Select your domain.
2. Click **Create Record** → Choose **A Record**.
3. Set **Value** as your EC2 Public IP → Click **Create Record**.

## **Step 6: Deploy Docker Containers**
1. Inside the cloned repository, create required directories:
```bash
mkdir -p certbot/conf certbot/www nginx
```
2. Copy your `nginx.conf` and `docker-compose.yml` into the project directory.

3. Run Docker Compose:
```bash
docker-compose up -d
```

## **Step 7: Verify and Secure Your Deployment**
- Visit `http://yourdomain.com` to check if Nginx is serving the website.
- Run the following command to check if containers are running:
```bash
docker ps
```
- If SSL is working, you should see a padlock when accessing `https://yourdomain.com`.

## **Step 8: Set Up Automatic SSL Renewal**
Certbot is already configured in the `docker-compose.yml` to renew certificates. However, you can manually test renewal using:
```bash
docker-compose run certbot-renew certbot renew --dry-run
```

## **Step 9: Reboot Policy and Restarting Services**
To ensure your setup restarts on reboot:
```bash
sudo systemctl enable docker
```
To restart the setup manually:
```bash
docker-compose down && docker-compose up -d
```

---
Your secure Docker-based application with SSL is now live on AWS EC2 with Route 53! 🚀

