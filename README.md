

# 💃✨ Runway of Cloud Architecture ✨💃
<img width="1273" height="658" alt="Untitled Diagram drawio (1)" src="https://github.com/user-attachments/assets/1a55bc46-ac4b-4cd3-bdec-fa6ffacd4e11" />

### Terraforming the Perfect 3-Tier Style — Book Project Deployment

---

## 📖 Overview
This project provisions a **3-tier AWS architecture** using Terraform and deploys a **Bookstore application** with backend + frontend servers, RDS database, and secure access via a Bastion host.  
It includes **Load Balancers, Target Groups, Route 53 DNS**, and step-by-step deployment instructions.



---

## 📁 Full Project Structure with README.md

```plaintext
Module_3-tier-Project_Terraform/
├── README.md                        # 📘 Full deployment guide with stylish heading
├── terraform.tfvars                # Global variable values
├── provider.tf                     # AWS provider configuration
├── main.tf                         # Root orchestration (calls child modules)
├── output.tf                       # Global outputs
├── CHILD/                          # Optional nested module or orchestration layer
│   ├── .terraform/                 # Terraform internal state
│   ├── .terraform.lock.hcl
│   └── main.tf
│
├── modules/                        # 📦 Modular infrastructure components
│   ├── VPC/
│   │   ├── main.tf
│   │   ├── output.tf
│   │   ├── provider.tf
│   │   ├── security-group.tf
│   │   ├── terraform.tfvars
│   │   └── variable.tf
│
│   ├── RDS/
│   │   ├── data.tf
│   │   ├── output.tf
│   │   ├── provider.tf
│   │   ├── rds.tf
│   │   ├── terraform.tfvars
│   │   └── variable.tf
│
│   ├── ASG/
│   │   ├── main.tf
│   │   ├── output.tf
│   │   ├── provider.tf
│   │   └── variable.tf
│
│   ├── BASTION/
│   │   └── main.tf
│
│   ├── LAUNCH-TEMPLATE/
│   │   ├── main.tf
│   │   ├── output.tf
│   │   ├── provider.tf
│   │   └── variable.tf
│
│   ├── LB-BACKEND/
│   │   ├── main.tf
│   │   ├── output.tf
│   │   ├── provider.tf
│   │   └── variable.tf
│
│   ├── LB-FRONTEND/
│   │   ├── main.tf
│   │   ├── output.tf
│   │   ├── provider.tf
│   │   └── variable.tf
│
│   └── ROUTE53/
│       ├── main.tf
│       ├── output.tf
│       └── variable.tf
│
├── backend/                        # 🧠 Backend application
│   ├── index.js
│   ├── package.json
│   ├── .env.example
│   └── test.sql
│
├── frontend/                       # 🎨 Frontend application
│   ├── client/
│   │   ├── src/
│   │   │   └── pages/
│   │   │       └── config.js
│   │   ├── package.json
│   │   └── build/                 # Final React build copied to Apache
│   └── deploy.sh                  # Optional deployment script
│
└── scripts/                        # 🔧 Utility scripts
    ├── bastion-connect.sh         # SSH helper
    └── cleanup.sh                 # Terraform destroy + manual cleanup
```

---




## 🛠️ Prerequisites
- AWS account with permissions for VPC, EC2, RDS, ALB, Route 53
- Terraform installed locally
- AWS CLI configured
- SSH key pair created in AWS region (e.g., `us-east-1`)
- Domain name in Route 53 (optional, e.g., `chintu.shop`)

---

## 🚀 Terraform Deployment Steps

```bash
terraform init
terraform plan
terraform apply
```

Terraform will create:

1. VPC  
2. Internet Gateway  
3. Subnets  
   - 2 Public (Bastion, ALB)  
   - 6 Private (Frontend, Backend, RDS)  
4. Route Tables (Public + NAT Gateway)  
5. NAT Gateway + Elastic IP  
6. Security Groups  
   - Public SG  
   - Frontend SG  
   - Backend SG  
   - Load Balancer SG  
   - Database SG  
7. EC2 Instances  
   - Bastion (public)  
   - Frontend (private)  
   - Backend (private)  
8. RDS Subnet Group + Database  
9. Target Groups + Load Balancers (Frontend + Backend)

---

## 🔒 Security Groups Summary
- **Public SG:** SSH (22) + HTTP/HTTPS (80/443)  
- **Frontend SG:** Allow HTTP from ALB SG  
- **Backend SG:** Allow app port (3000) from ALB SG, DB access to RDS SG  
- **Database SG:** Allow 3306 from Backend SG only  
- **Load Balancer SG:** Allow 80/443 inbound, forward traffic to targets  

---

## 🖥️ Backend Setup

### Connect via Bastion
```bash
ssh -i <your-key>.pem ec2-user@<bastion-public-ip>
sudo su -
vi <keypair name>.pem
chmod 400 <keypair name>.pem
ssh -i <keypair name>.pem ec2-user@<backend-private-ip>
```

### Configure Backend
```bash
               sudo su -
               yum install git -y
               git clone https://github.com/chintu-cloud/Module_3-tier-Project_Terraform.git
               ls
 	           cd 2nd10WeeksofCloudOps-main/
 	           ls
```
# then remove the unnecessary file
# go to backend
```
 	           cd backend/
               vi .env
```
# go to .env file to set the correct configuration



Set environment variables:
```
DB_HOST=<rds-endpoint>
DB_USERNAME=admin
DB_PASSWORD="chandan#1234"
PORT=3306
```
# after changes goto   esc --> :wq! --> enter

Install dependencies:
```bash
yum install mariadb105-server -y
mysql -h <rds-endpoint> -u admin -p<password> < test.sql
sudo dnf install -y nodejs
npm install
npm install -g pm2
pm2 start index.js --name node-app
curl http://localhost
```
# // hello response form server
---

## 🎯 Backend Target Group & Load Balancer
- Create **Backend TG** → Register backend instance
- Register Target
 ```
             select - backend server
             (Click) - Including as pending below     --> then save
```
 
- Create **Backend ALB** → Attach TG, map to public subnets
```
          vpc -  project-vpc
          network maping - public-subnet-1a
                           public-subnet-1b
          SG - ALB-sg & backend-sg
          TG - bastion-tg
          AZ - 1a, 1b

```  
- Check target health → must be **Healthy**

---

## 🎨 Frontend Setup

### Connect via Bastion
```bash
ssh -i <your-key>.pem ec2-user@<bastion-public-ip>
sudo su -
vi <keypair name>.pem
chmod 400 <keypair name>.pem
ssh -i <keypair name>.pem ec2-user@<backend-private-ip>
sudo su -
```

### Configure Frontend
```bash
                   sudo su -
                   vi <keypair name>.pem
                   Chmod 400 <keypair name>
    //  goto Frontend server & connect  then go SSH client copy & paste
                   SSH client (copy code & paste)
 
        connect Frontend
                   sudo su -

    //  install dependencies

                   yum install httpd -y
                   systemctl start httpd
                   systemctl enable httpd
 
   //  install nodejs

                   sudo dnf install -y nodejs

   //   Frontend deploy process
                   yum install git -y
                   git clone https://github.com/CloudTechDevOps/2nd10WeeksofCloudOps-main.git
                   ls
 	               cd 2nd10WeeksofCloudOps-main/
 	               ls

 
   //  edit the config.js

                   vi client/src/pages/config.js

```

Update API URL:
```js
const API_BASE_URL = "http://<backend-loadbalancer-dns>";
```

Build & deploy:
****(Use npm run build: When preparing the app for deployment (e.g., to a server or hosting service like AWS, Netlify, or Vercel).
    ( Use npm start: During development or to start the app in production (for backend apps).*****

```bash
npm install
npm run build
sudo cp -r build/* /var/www/html
     //  your frontend part is completed
```

---

## 🎯 Frontend Target Group & Load Balancer
- Create **Frontend TG** → Register frontend instance
- Goto Target Group    // for backend
 ```
             Register Target
             select - frontend server
             (Click) - Including as pending below     // then save
 ```
- Create **Frontend ALB** → Attach TG, map to public subnets
```
Goto Load balancer
 
          vpc -  project-vpc
          network maping - public-subnet-1a
                           public-subnet-1b
          SG - ALB-sg & backend-sg
          TG - backend-tg
          AZ - 1a, 1b

``` 
- Check target health → must be **Healthy**  
- Copy **Frontend ALB DNS** → Open in browser to see Bookstore app  

---



# 🗄️ Amazon RDS Setup Guide

This guide explains how to set up an **Amazon RDS instance** for your application.  
We’ll walk through creating the database, configuring connectivity, and verifying access.

---

## 📖 Prerequisites
- AWS account with appropriate IAM permissions
- VPC and subnets configured (preferably private subnets for RDS)
- Security groups created for database access
- Application requirements (e.g., MySQL, PostgreSQL, or other supported engines)

---

## ⚙️ Steps to Create RDS Instance

### 1. Navigate to RDS
- Go to the **AWS Management Console**
- Open **RDS** service

---

### 2. Create Database
1. Click **Create database**
2. Select:
   - **Engine type** → Choose (e.g., MySQL, PostgreSQL, MariaDB, Oracle, SQL Server)
   - **Version** → Latest stable version recommended
   - **Template** → `Production` or `Dev/Test` depending on use case

---

### 3. Configure Settings
- **DB instance identifier** → e.g., `chintu-db`
- **Master username** → e.g., `admin`
- **Master password** → Set a strong password

---

### 4. Instance Configuration
- **DB instance class** → Choose based on workload (e.g., `db.t3.micro` for testing, `db.m5.large` for production)
- **Storage type** → General Purpose (SSD) or Provisioned IOPS
- **Allocated storage** → e.g., 20 GB (scalable)

---

### 5. Connectivity
- **VPC** → Select your application VPC
- **Subnets** → Choose private subnets (recommended)
- **Security group** → Allow inbound traffic from application servers
- **Public access** → `No` (recommended for production)

---

### 6. Additional Configurations
- **Database authentication** → Password or IAM-based
- **Backup** → Enable automated backups
- **Monitoring** → Enable CloudWatch monitoring
- **Maintenance** → Configure maintenance window

---

### 7. Create Database
- Review all settings
- Click **Create database**
- Wait for the instance status to become **Available**

---

## ✅ Verification
- Copy the **endpoint** from the RDS console
- Test connectivity using a client:
  ```bash
  mysql -h <rds-endpoint> -u admin -p


---

## 🌐 Route 53 DNS
- Create alias record for **Frontend ALB** → `chintu.shop`  
- Create alias record for **Backend ALB** (optional)
  Here’s a polished **README.md** file that documents the Route53 setup you described. I’ve structured it with clear headings, step-by-step instructions, and stylish formatting so it’s beginner-friendly yet professional:  

# 🌐 Route53 Setup for chintu.shop

This guide explains how to configure **Amazon Route53** to point your domain `chintu.shop` to Application Load Balancers (ALBs) in **us-east-1**.  
We will create **alias records** for both the **frontend** and **backend** load balancers.

---

## 📖 Prerequisites
- Domain name registered in Route53 (`chintu.shop`)
- Application Load Balancers created in **us-east-1** region:
  - **Frontend LB**
  - **Backend LB**
- Proper IAM permissions to manage Route53 and ALB

---

## ⚙️ Steps

### 1. Navigate to Route53
- Go to the **AWS Management Console**
- Open **Route53**
- Select your hosted zone for `chintu.shop`

---

### 2. Create Alias Record for Frontend LB
1. Click **Create record**
2. Choose:
   - **Record type** → `A – Routes traffic to an IPv4 address and some AWS resources`
   - **Alias** → `Yes`
3. Configure alias:
   - **Route traffic to** → `Application Load Balancer`
   - **Region** → `us-east-1`
   - **Load balancer** → Select **Frontend LB**
4. Save the record.

---

### 3. Create Alias Record for Backend LB
1. Click **Create record**
2. Choose:
   - **Record type** → `A – Routes traffic to an IPv4 address and some AWS resources`
   - **Alias** → `Yes`
3. Configure alias:
   - **Route traffic to** → `Application Load Balancer`
   - **Region** → `us-east-1`
   - **Load balancer** → Select **Backend LB**
4. Save the record.

---

## ✅ Verification
- Run `nslookup chintu.shop` or `dig chintu.shop` to confirm DNS resolution.
- Test application endpoints to ensure traffic is routed correctly to the ALBs.

---

## 📌 Notes
- Alias records are free of charge in Route53.
- Alias records automatically update if the ALB’s IPs change.
- You can add additional records (e.g., `CNAME`, `MX`) depending on your application needs.

---

## 🗂 Example Record Structure
| Domain        | Type | Alias Target            | Region     | Load Balancer |
|---------------|------|-------------------------|------------|---------------|
| chintu.shop   | A    | Application Load Balancer | us-east-1 | Frontend LB   |
| chintu.shop   | A    | Application Load Balancer | us-east-1 | Backend LB    |

---

### 🚀 Done!
Your domain `chintu.shop` is now configured to route traffic to both **Frontend** and **Backend** ALBs using Route53 alias records.
```
  
- Test domain in browser → should load frontend and connect to backend
<img width="1920" height="1080" alt="Screenshot (251)" src="https://github.com/user-attachments/assets/4ae3cef0-6ebf-486f-9200-34bfbf509399" />

---

## ✅ Verification Checklist
- Bastion SSH works  
- Backend responds with `curl http://localhost`  
- RDS schema created via `test.sql`  
- Frontend served via Apache (`/var/www/html`)  
- ALBs healthy and serving traffic  
- Route 53 domain resolves correctly  

---

## 🧹 Cleanup
```bash
terraform destroy
```
- Remove Route 53 records if not needed  
- Verify EIP/NAT/ALBs are deleted  

---

## 🔐 Production Notes
- Use **Secrets Manager** for DB credentials  
- Enable **Auto Scaling** for frontend/backend  
- Add **CloudWatch monitoring**  
- Use **ACM certificates** for HTTPS  
- Enable **RDS backups**  

---

## 🎉 Stylish Closing
This project is your **runway-ready cloud architecture** — blending infrastructure elegance with application performance.  
Deploy, scale, and strut your Bookstore app with confidence! 💃📚✨
```

---

This is the **full README.md file** with a stylish heading, all steps included, and no missing lines.  

👉 Do you want me to also create a **matching ASCII-art banner** at the very top so the README looks even more eye-catching when opened in GitHub?
