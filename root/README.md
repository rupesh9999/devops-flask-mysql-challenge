# Flask-MySQL VPC Deployment Project

A comprehensive DevOps project demonstrating the deployment of a Flask application and MySQL database on AWS using VPC networking, NAT Gateway configuration, and SSL security.

## 🚀 Project Overview

This project implements a secure, scalable architecture where:
- **Flask Application** runs on a public subnet with HTTPS access
- **MySQL Database** runs on a private subnet for security
- **NAT Gateway** enables secure outbound internet access for private resources
- **SSL/TLS** encryption secures all communications

## 📋 Prerequisites

- AWS Account with appropriate permissions
- AWS CLI configured
- Python 3.8+
- Git
- SSH Key Pair for EC2 access

## 🏗️ Architecture

```
Internet
    ↓ (HTTPS)
Internet Gateway
    ↓
Public Subnet (Flask App)
    ↓ (Private IP)
Private Subnet (MySQL DB)
    ↓ (NAT Gateway)
Internet (for updates)
```

## 🛠️ Quick Start

### 1. Clone the Repository
```bash
git clone <repository-url>
cd flask-mysql-vpc-project
```

### 2. Set Up Environment Variables
```bash
cp .env.example .env
# Edit .env with your configuration
```

### 3. Deploy Infrastructure
```bash
# Deploy VPC and networking
aws cloudformation deploy \
  --template-file infrastructure/cloudformation/vpc-infrastructure.yaml \
  --stack-name flask-mysql-vpc \
  --capabilities CAPABILITY_IAM

# Deploy security groups
aws cloudformation deploy \
  --template-file infrastructure/cloudformation/security-groups.yaml \
  --stack-name flask-mysql-security-groups \
  --capabilities CAPABILITY_IAM

# Deploy EC2 instances
aws cloudformation deploy \
  --template-file infrastructure/cloudformation/ec2-instances.yaml \
  --stack-name flask-mysql-instances \
  --capabilities CAPABILITY_IAM \
  --parameter-overrides KeyName=your-key-pair-name
```

### 4. Configure Services
```bash
# SSH into Flask instance and run setup
ssh -i your-key.pem ubuntu@<flask-public-ip>
sudo bash /home/ubuntu/setup-flask.sh

# SSH into MySQL instance via bastion/Flask instance
ssh -i your-key.pem ubuntu@<mysql-private-ip>
sudo bash /home/ubuntu/setup-mysql.sh
```

### 5. Configure SSL
```bash
# On Flask instance
sudo bash /home/ubuntu/configure-ssl.sh your-domain.com
```

## 📁 Project Structure

```
flask-mysql-vpc-project/
├── app/                    # Flask application code
│   ├── routes/            # URL routing
│   ├── models/            # Database models
│   ├── config/            # Configuration
│   ├── static/            # CSS, JS, images
│   └── templates/         # HTML templates
├── infrastructure/        # AWS infrastructure code
│   ├── cloudformation/   # CloudFormation templates
│   └── scripts/          # Setup scripts
├── tests/                # Test files
├── docs/                 # Documentation
└── requirements.txt      # Python dependencies
```

## 🔧 Configuration

### Environment Variables (.env)
```
# Database Configuration
DB_HOST=<mysql-private-ip>
DB_NAME=flask_app_db
DB_USER=flask_user
DB_PASSWORD=secure_password

# Flask Configuration
FLASK_ENV=production
SECRET_KEY=your-secret-key
SSL_CERT_PATH=/etc/ssl/certs/cert.pem
SSL_KEY_PATH=/etc/ssl/private/key.pem
```

### Security Groups
- **Flask Security Group**: Allows HTTPS (443), HTTP (80), SSH (22)
- **MySQL Security Group**: Allows MySQL (3306) from Flask Security Group only

## 🧪 Testing

### Connectivity Tests
```bash
# Test Flask app connectivity
curl -k https://<flask-public-ip>

# Test MySQL connectivity from Flask instance
python -c "import pymysql; print('MySQL connection test')"

# Run automated tests
python -m pytest tests/
```

### Failure Simulation
```bash
# Stop MySQL service for testing
sudo systemctl stop mysql

# Check Flask app response
curl -k https://<flask-public-ip>/health

# Troubleshoot connectivity
ping <mysql-private-ip>
telnet <mysql-private-ip> 3306
```

## 🔍 Troubleshooting

### Common Issues

1. **Flask app can't connect to MySQL**
   - Check security group rules
   - Verify MySQL is running: `sudo systemctl status mysql`
   - Test network connectivity: `ping <mysql-private-ip>`

2. **SSL certificate issues**
   - Verify certificate files exist and have correct permissions
   - Check domain name matches certificate
   - Test with curl: `curl -v https://your-domain.com`

3. **NAT Gateway connectivity issues**
   - Check route table configuration
   - Verify NAT Gateway is in public subnet
   - Test outbound connectivity from private subnet

### Monitoring Commands
```bash
# Check Flask application logs
sudo journalctl -u flask-app -f

# Check MySQL logs
sudo tail -f /var/log/mysql/error.log

# Check network connections
netstat -tlnp | grep :3306
ss -tlnp | grep :443
```

## 📊 Monitoring & Logging

- **CloudWatch Logs**: Application and system logs
- **VPC Flow Logs**: Network traffic monitoring
- **CloudTrail**: API call auditing
- **Custom Metrics**: Application performance metrics

## 🔐 Security Best Practices

- Database in private subnet with no internet access
- Security groups with least privilege access
- SSL/TLS encryption for all communications
- Regular security updates via NAT Gateway
- IAM roles with minimal required permissions

## 🎯 Bonus Features

### IP-based Access Restriction
```bash
# Update security group to allow specific IP range
aws ec2 authorize-security-group-ingress \
  --group-id sg-xxxxxxxxx \
  --protocol tcp \
  --port 443 \
  --cidr 192.168.1.0/24
```

### Post-Mortem Analysis
See `docs/post-mortem-template.md` for incident analysis framework.

## 📚 Additional Resources

- [AWS VPC Documentation](https://docs.aws.amazon.com/vpc/)
- [Flask Deployment Guide](https://flask.palletsprojects.com/en/stable/deploying/)
- [MySQL Security Best Practices](https://dev.mysql.com/doc/refman/8.0/en/security.html)

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests for new functionality
5. Submit a pull request

## 📝 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🏷️ Hashtags

#getfitwithsagar #SRELife #DevOpsForAll #AWS #Flask #MySQL #VPC #NAT #SSL #CloudFormation

---

**Author**: DevOps Engineering Team  
**Version**: 1.0.0  
**Last Updated**: $(date +%Y-%m-%d)
