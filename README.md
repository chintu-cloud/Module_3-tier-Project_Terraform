

# 💃✨ Runway of Cloud Architecture ✨💃
### Terraforming the Perfect 3-Tier Style — Book Project Deployment

---

## 📖 Overview
This project provisions a **3-tier AWS architecture** using Terraform and deploys a **Bookstore application** with backend + frontend servers, RDS database, and secure access via a Bastion host.  
It includes **Load Balancers, Target Groups, Route 53 DNS**, and step-by-step deployment instructions.

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
yum install git -y
git clone https://github.com/chintu-cloud/Module_3-tier-Project_Terraform.git
cd 2nd10WeeksofCloudOps-main/backend/
vi .env
```

Set environment variables:
```
DB_HOST=<rds-endpoint>
DB_USERNAME=admin
DB_PASSWORD="chandan#1234"
PORT=3306
```

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

---

## 🎯 Backend Target Group & Load Balancer
- Create **Backend TG** → Register backend instance  
- Create **Backend ALB** → Attach TG, map to public subnets  
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
yum install httpd -y
systemctl start httpd
systemctl enable httpd
sudo dnf install -y nodejs
yum install git -y
git clone https://github.com/CloudTechDevOps/2nd10WeeksofCloudOps-main.git
cd 2nd10WeeksofCloudOps-main/client/
vi src/pages/config.js
```

Update API URL:
```js
const API_BASE_URL = "http://<backend-loadbalancer-dns>";
```

Build & deploy:
```bash
npm install
npm run build
sudo cp -r build/* /var/www/html
```

---

## 🎯 Frontend Target Group & Load Balancer
- Create **Frontend TG** → Register frontend instance  
- Create **Frontend ALB** → Attach TG, map to public subnets  
- Check target health → must be **Healthy**  
- Copy **Frontend ALB DNS** → Open in browser to see Bookstore app  

---

## 🗄️ RDS Setup
- Create **Subnet Group** → Private subnets across AZs  
- Create **Database** → MySQL/MariaDB with DB SG  
- Use endpoint in backend `.env`

---

## 🌐 Route 53 DNS
- Create alias record for **Frontend ALB** → `chintu.shop`  
- Create alias record for **Backend ALB** (optional)  
- Test domain in browser → should load frontend and connect to backend

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
