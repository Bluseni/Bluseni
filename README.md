# Secure and Automated Server Setup
This project demonstrates how to set up a secure server on an AWS EC2 instance using Ubuntu. The setup includes:
- Installing essential software like Nginx and Python.
- Deploying a Flask web application.
- Configuring a firewall.
- Implementing Git best practices to protect sensitive information.

The goal is to showcase secure and automated server management in a cloud environment.
## Features
- Securely deploy a Flask web application with Nginx.
- Automate server setup using Bash scripting.
- Prevent sensitive files from being exposed in Git repositories.
- Utilize SSL/TLS for secure communication.
## Technologies Used
- AWS EC2
- Ubuntu 20.04 LTS
- Python (Flask)
- Nginx
- Git
- Certbot (Let's Encrypt)
## Handling Sensitive Files
Sensitive files, such as `.ssh` and `.bash_history`, were mistakenly committed to the repository. To address this:
- The files were removed from the repository using `git filter-repo`.
- A `.gitignore` file was added to prevent these files from being re-added.
- Exposed SSH keys were revoked and replaced to maintain security.
## Getting Started
Follow these steps to replicate the project on your own:

### Prerequisites
1. AWS Account
2. SSH Key Pair


### Steps to Deploy
1. **Launch an EC2 Instance**
   - Use the AWS Console to launch an Ubuntu 20.04 instance.
   - Configure the security group to allow SSH (22), HTTP (80), and HTTPS (443).

2. **Connect to the EC2 Instance**
   ```bash
   ssh -i "your-key.pem" ubuntu@<your-ec2-public-ip>
   
 ## Update and upgrade the system to ensure it’s up-to-date:  
sudo apt update && sudo apt upgrade -y
 ## Install Required Software
sudo apt install nginx python3 python3-pip git -y

## Setting Up the Flask Application
mkdir ~/flask_app
cd ~/flask_app

## Create a Python virtual environment
python3 -m venv venv
source venv/bin/activate
## Install Flask and Gunicorn
pip install flask gunicorn
## Create the Flask application
nano app.py
## Add the following code:
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello, World! Flask is running on AWS EC2!, ALso welcome to my 20 days linux challenge please feel free to ask me any question regarding my step"

if __name__ == '__main__':
    app.run(host='0.0.0.0')

## Test the Flask app:
python app.py
http://44.201.132.99:5000

## Run Flask with Gunicorn
gunicorn -w 3 -b 0.0.0.0:5000 app:app

## Create a Gunicorn Service
sudo nano /etc/systemd/system/flask_app.service

## Add the following:

[Unit]
Description=Gunicorn instance to serve Flask app
After=network.target

[Service]
User=ubuntu
Group=www-data
WorkingDirectory=/home/ubuntu/flask_app
Environment="PATH=/home/ubuntu/flask_app/venv/bin"
ExecStart=/home/ubuntu/flask_app/venv/bin/gunicorn -w 3 -b 0.0.0.0:5000 app:app

[Install]
WantedBy=multi-user.target

## Start and enable the service

sudo systemctl start flask_app
sudo systemctl enable flask_app

## Configure Nginx
sudo rm /etc/nginx/sites-enabled/default
## Create a new Nginx configuration
sudo nano /etc/nginx/sites-available/flask_app

## Add code

server {
    listen 80;

    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
## Enable the site and restart Nginx
sudo ln -s /etc/nginx/sites-available/flask_app /etc/nginx/sites-enabled
sudo systemctl restart nginx

## Test the set up

http://44.201.132.99




