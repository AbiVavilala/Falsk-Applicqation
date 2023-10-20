#   This repository contains instructions and resources to deploy a Flask application on an Amazon EC2 instance, configure an Application Load Balancer, create Route 53 records for routing traffic through the load balancer, set up a security group for the load balancer, deploy an AWS RDS MySQL database in a private subnet, and add an SSL certificate from AWS ACM.

 ## Application I am deploying
I am deploying a Flask Web application. Source code is in the repo. This application is Library Catalogue Management. I will add books with author name and price of the book, Update book info and delete the book from catalogue. data is stored in AWS RDS MySQL database. 
![](https://github.com/AbiVavilala/Flask-Application-on-AWS/blob/main/picsforreadme/%20FlaskApplication.png)




## Deployment Steps

Follow these steps to deploy your Flask application on AWS:

### 1. Deploy VPC with Public Subnets, Private Subnets, Internet Gateway, Custom Route table and Security group. All the resources have been created using Terraform. modules for deploying can be found below in my other repo
<https://github.com/AbiVavilala/Terraform-AWS-Modules-Dev-Environment>

### 2. Launch EC2 Instance

- Create a new EC2 instance, choose an appropriate Amazon Machine Image (AMI), and configure the instance with the necessary resources.

### 3. Install Dependencies and deploy code from gitHub

- SSH into your EC2 instance and install the required dependencies for your Flask application. the source code is provided below. 

-Install Python Virtualenv
```bash
  sudo apt-get update
  sudo apt-get install python3-venv
```

-  I need to clone my source code into my ec2 instance. I created a folder Flask and cloned my repo into that folder. I installed libraries required to host my application.
```bash
// Create directory
  mkdir  Flask
  cd Flask
// Clone Git repo
Git clone https://github.com/AbiVavilala/Flask-Application-on-AWS.git
// Create the virtual environment
  python3 -m venv venv
// Activate the virtual environment
  source venv/bin/activate
// Install dependencies for mysqlclient Library
sudo apt install python3-dev default-libmysqlclient-dev build-essential -y
// Install requirements
  pip install -r requirements.txt
// Install Gunicorn as WSGI to handle request from users
 pip install Gunicorn
```
 Once all the dependencies are installed please run  gunicorn -b 0.0.0.0:8000 app:app.
![](https://github.com/AbiVavilala/Flask-Application-on-AWS/blob/main/picsforreadme/%20creatingserviceflask.png)


-  Systemd is a boot manager for Linux. We are using it to restart gunicorn if the EC2 restarts or reboots for some reason.

We create a ```bash <projectname>.service file in the /etc/systemd/system folder ```, and specify what would happen to gunicorn when the system reboots.

We will be adding 3 parts to systemd Unit file — Unit, Service, Install

Unit — This section is for description about the project and some dependencies
Service — To specify user/group we want to run this service after. Also some information about the executables and the commands.
Install — tells ```bash systemd ``` at which moment during boot process this service should start.
With that said, create an unit file in the ```bash /etc/systemd/system ``` directory

```bash
  sudo nano /etc/systemd/system/helloworld.service

- then add this into the file 
```
 [Unit]
Description=Gunicorn instance for a  flask app
After=network.target
[Service]
User=ubuntu
Group=www-data
WorkingDirectory=/home/ubuntu/Flask/Flask-Applicqation
ExecStart=/home/ubuntu/Flask/Flask-Applicqationvenv/bin/gunicorn -b localhost:8000 app:app
Restart=always
[Install]
WantedBy=multi-user.target
```

### 5. Create an RDS MySQL Database

- Create an AWS RDS MySQL instance in a private subnet. Ensure it's configured with the appropriate security groups and VPC settings.

### 6. Configure Security Groups

- Set up security groups for your EC2 instance, RDS instance, and Application Load Balancer to allow the necessary traffic.

### 7. Create an Application Load Balancer

- Configure an Application Load Balancer (ALB) to distribute incoming traffic across your EC2 instances.

### 8. Add EC2 Instances to Target Group

- Add your EC2 instances to a target group associated with the ALB.

### 9. Create Load Balancer Listeners

- Create two listeners: one for HTTP routing to HTTPS and another for HTTPS traffic.

### 10. ACM Certificate

- Request an SSL certificate from AWS Certificate Manager (ACM) and associate it with your Application Load Balancer.

### 11. Route 53 Setup

- Update Route 53 records to point to your Application Load Balancer, and associate your ACM certificate with your domain name. Ensure DNS propagation has occurred.

## Additional Resources

- [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [AWS RDS Documentation](https://docs.aws.amazon.com/rds/)
- [AWS Elastic Load Balancing Documentation](https://docs.aws.amazon.com/elasticloadbalancing/)
- [AWS Route 53 Documentation](https://docs.aws.amazon.com/Route53/)
- [AWS Certificate Manager Documentation](https://docs.aws.amazon.com/acm/)

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

In this README file, I've included the additional step for creating an SSL certificate from ACM and associated it with your Application Load Balancer. Make sure to replace placeholders with actual instructions, AWS resource names, and other details relevant to your project.







