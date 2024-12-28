# MarketPeak Ecommerce - Deployment Guide

This guide outlines the steps taken to set up the environment, deploy the MarketPeak Ecommerce application, and manage the entire flow from development to production.

## Prerequisites

- **EC2 Instance** on AWS
- **SSH Key** for secure access to the EC2 instance
- **Git** installed on both local machine and EC2 instance
- **Apache Web Server** installed on EC2 instance
- **GitHub Repository** for source code

---

## 1. **Setting up the Development Environment**

### Step 1: Clone the Repository

1. **Clone the repository** to your local machine:
    ```bash
    git clone https://github.com/lamideh1/MarketPeak_Ecommerce1.git
    ```
2. Navigate into the cloned directory:
    ```bash
    cd MarketPeak_Ecommerce1
    ```

### Step 2: Create and Manage Git Branches

1. Check for existing branches and switch to the default branch (usually `master` or `main`):
    ```bash
    git branch -a
    git checkout master  # or `git checkout main` depending on your repo setup
    ```

2. **Create a development branch**:
    ```bash
    git checkout -b development
    ```

3. **Commit and Push Changes**:
    - After making changes, commit the changes to the development branch:
    ```bash
    git commit -m "Add new features or fix bugs"
    ```
    - Push the development branch to the remote repository:
    ```bash
    git push -u origin development
    ```

---

## 2. **Setting up the EC2 Instance (Production Server)**

### Step 1: Launch an EC2 Instance

1. Launch an EC2 instance on AWS with a suitable Linux AMI (Amazon Machine Image), such as Amazon Linux 2 or Ubuntu.
2. Select security groups and ensure **SSH** access is enabled.
3. Download and store the **SSH private key** (`marketpeak1.pem`).

### Step 2: SSH into EC2 Instance

1. Change the permissions of your SSH key file:
    ```bash
    chmod 400 path/to/marketpeak1.pem
    ```
2. SSH into your EC2 instance:
    ```bash
    ssh -i path/to/marketpeak1.pem ec2-user@<ec2-public-ip>
    ```

### Step 3: Install Apache Web Server

1. Install Apache on the EC2 instance:
    - For Amazon Linux or RHEL-based instances:
    ```bash
    sudo yum install httpd -y
    ```

2. **Start and enable Apache** to run on boot:
    ```bash
    sudo systemctl start httpd
    sudo systemctl enable httpd
    ```

### Step 4: Install Git (If Needed)

1. Install Git if it’s not already installed:
    ```bash
    sudo yum install git -y
    ```

---

## 3. **Clone Repository and Deploy on the EC2 Instance**

### Step 1: Clone the Repository on the EC2 Instance

1. SSH into the EC2 instance and clone the repository:
    ```bash
    git clone https://github.com/lamideh1/MarketPeak_Ecommerce1.git
    ```

2. Navigate to the project directory:
    ```bash
    cd MarketPeak_Ecommerce1
    ```

### Step 2: Copy the Application Files to Apache's Web Root

1. Clean the default web root directory (if necessary):
    ```bash
    sudo rm -rf /var/www/html/*
    ```

2. Copy the application files to Apache's web root directory:
    ```bash
    sudo cp -r ~/MarketPeak_Ecommerce1/* /var/www/html/
    ```

3. Change ownership of the files to the `apache` user (if necessary):
    ```bash
    sudo chown -R apache:apache /var/www/html/
    ```

---

## 4. **Configure and Test the Apache Web Server**

### Step 1: Reload or Restart Apache

1. **Reload Apache** to apply changes:
    ```bash
    sudo systemctl reload httpd
    ```
   
2. **Check the status of Apache** to ensure it is running:
    ```bash
    sudo systemctl status httpd
    ```

### Step 2: Verify the Deployment

1. Open the EC2 instance's public IP in a browser. If the Apache server is running and files are correctly copied, you should see the deployed website.

    Example: `http://<ec2-public-ip>`

---

## 5. **Final Steps**

### Step 1: Push Updates from Local Development to Remote Repository

1. After testing and making further changes locally, push the updates back to the GitHub repository:
    ```bash
    git add .
    git commit -m "Updated features"
    git push origin development
    ```

### Step 2: Pull Updates on the EC2 Instance

1. On the EC2 instance, navigate to the project directory:
    ```bash
    cd ~/MarketPeak_Ecommerce1
    ```

2. **Pull the latest changes** from the repository:
    ```bash
    git pull origin development
    ```

3. **Reload Apache** to reflect the changes on the live server:
    ```bash
    sudo systemctl reload httpd
    ```

---

## 6. **Troubleshooting and Challenges**

During the deployment process, the following challenges were encountered and resolved:

### **1. SSH Access Issues (Permission Denied)**

**Problem:**
- When attempting to SSH into the EC2 instance, the error message `Permission denied (publickey)` was encountered.

**Solution:**
- The issue was due to missing or incorrectly configured SSH keys. I generated a new SSH key on the EC2 instance and ensured the public key was added to the EC2 instance's `authorized_keys`.
- Verified that the SSH private key (`marketpeak1.pem`) was correctly configured with proper permissions (`chmod 400`), and used the correct key to connect.

---

### **2. Git Issues (Cloning the Repository)**

**Problem:**
- Encountered errors like `fatal: destination path 'MarketPeak_Ecommerce1' already exists and is not an empty directory`.

**Solution:**
- This error was resolved by either removing the existing directory or renaming it. After that, I successfully cloned the repository again on the EC2 instance:
    ```bash
    rm -rf MarketPeak_Ecommerce1
    git clone https://github.com/lamideh1/MarketPeak_Ecommerce1.git
    ```

---

### **3. Copying Files to Apache's Web Directory**

**Problem:**
- Received the error `cp: cannot stat '/home/ec2-user/MarketPeak_Ecommerce1/*': No such file or directory`.

**Solution:**
- The error occurred because the directory path was incorrect, possibly due to an issue with how the files were cloned or the current directory was not set properly.
- After ensuring the repository was cloned correctly into the home directory (`/home/ec2-user/MarketPeak_Ecommerce1`), I used the correct path to copy files to Apache's web directory:
    ```bash
    sudo cp -r /home/ec2-user/MarketPeak_Ecommerce1/* /var/www/html/
    ```

---

### **4. Apache Not Serving the Site**

**Problem:**
- Apache was not displaying the website after deployment, indicating an issue with the web server configuration.

**Solution:**
- I checked the Apache status and error logs:
    ```bash
    sudo systemctl status httpd
    sudo tail -f /var/log/httpd/error_log
    ```
- It turned out that the `httpd` service was not running correctly. I restarted and reloaded the Apache service:
    ```bash
    sudo systemctl restart httpd
    sudo systemctl reload httpd
    ```

---

## Conclusion

By following these steps, you've successfully deployed the MarketPeak Ecommerce website from your local development environment to a production server hosted on an AWS EC2 instance. The workflow included cloning the repository, managing branches, setting up the EC2 instance, and deploying the website via Apache.

---

