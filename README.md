# 🚀 React Frontend Deployment on AWS EC2 using Nginx

This README explains how to deploy a **React/Vite frontend application** on an **AWS EC2 Ubuntu server** using **Nginx**.

The main idea is:

```text
Local React Project
       ↓
     GitHub
       ↓
     EC2
       ↓
   Node.js + NVM
       ↓
   npm install
       ↓
   npm run build
       ↓
      dist/
       ↓
     Nginx
       ↓
 /var/www/html/
       ↓
   Internet 🌍
```

---

# 📌 Prerequisites

Before starting, make sure you have:

* AWS account
* EC2 Ubuntu instance
* `.pem` private key
* GitHub repository containing your project
* Git installed on your local machine
* Node.js installed locally
* Your React project should build successfully on your local machine

Check your local Node version:

```bash
node -v
```

Example:

```text
v22.13.1
```

It is recommended to use the same Node.js version on EC2.

---

# 1️⃣ Launch an EC2 Instance

Go to:

```text
AWS Console
   ↓
EC2
   ↓
Launch Instance
```

Choose Ubuntu as the operating system.

After launching the instance, AWS provides:

* Public IPv4 address
* Public DNS
* `.pem` key pair

Example:

```text
Public DNS:
ec2-43-204-96-49.ap-south-1.compute.amazonaws.com
```

### ⚠️ Important

Keep your `.pem` file safe.

Never upload your `.pem` file to GitHub.

Add it to `.gitignore` if necessary:

```text
*.pem
```

---

# 2️⃣ Connect to EC2 using SSH

From your local terminal, go to the directory containing your `.pem` file.

On Linux/macOS:

```bash
chmod 400 devTinder-secret.pem
```

### What does `chmod 400` mean?

It gives the owner read permission and removes permissions for everyone else.

```text
Owner → Read
Group → No permission
Others → No permission
```

AWS requires the private key to have restricted permissions.

Then connect:

```bash
ssh -i "devTinder-secret.pem" ubuntu@<EC2-PUBLIC-DNS>
```

Example:

```bash
ssh -i "devTinder-secret.pem" ubuntu@ec2-43-204-96-49.ap-south-1.compute.amazonaws.com
```

After successful login:

```text
Your Laptop
     │
     │ SSH
     ↓
AWS EC2 Ubuntu Server
```

You are now working inside the EC2 machine.

---

# 3️⃣ Check the Ubuntu User

Run:

```bash
whoami
```

Expected:

```text
ubuntu
```

This tells you which Linux user you are currently using.

You can also check the current directory:

```bash
pwd
```

Usually:

```text
/home/ubuntu
```

---

# 4️⃣ Install NVM

## What is NVM?

NVM means:

**Node Version Manager**

It allows you to install and switch between different Node.js versions.

For example:

```text
NVM
 ├── Node 18
 ├── Node 20
 └── Node 22
```

This is useful when different projects require different Node versions.

Install NVM:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
```

### What is happening here?

```text
curl
 ↓
Downloads the NVM installation script
 ↓
|
 ↓
bash
 ↓
Executes the script
```

`|` is called a **pipe**.

It sends the output of one command to another command.

---

# 5️⃣ Reload Bash

After installing NVM:

```bash
source ~/.bashrc
```

### Why?

NVM adds its configuration to:

```text
~/.bashrc
```

`source` reloads that configuration into the current terminal session.

Without this, your current terminal may not recognize the `nvm` command immediately.

---

# 6️⃣ Verify NVM

Run:

```bash
nvm --version
```

Example:

```text
0.40.3
```

If you see a version number, NVM is installed successfully.

---

# 7️⃣ Install the Required Node.js Version

First check your local Node version:

```bash
node -v
```

Example:

```text
v22.13.1
```

Now install the same version on EC2:

```bash
nvm install 22.13.1
```

This means:

```text
NVM
 ↓
Download and install
 ↓
Node.js 22.13.1
```

### Why use the same Node version?

Using the same version reduces the possibility of:

* Build differences
* Dependency compatibility problems
* Unexpected runtime errors
* Different npm behavior

---

# 8️⃣ Select the Node.js Version

Run:

```bash
nvm use 22.13.1
```

This activates Node.js 22.13.1 for the current shell.

Verify:

```bash
node -v
```

Expected:

```text
v22.13.1
```

---

# 9️⃣ Make Node.js Version the Default

Run:

```bash
nvm alias default 22.13.1
```

### Difference between `use` and `alias default`

```text
nvm use 22.13.1
```

Activates the version for the current shell.

```text
nvm alias default 22.13.1
```

Makes it the default version for future shell sessions.

---

# 🔟 Verify npm

Run:

```bash
npm -v
```

NVM's Node.js installation normally includes npm.

You should see an npm version.

---

# 1️⃣1️⃣ Install Git if Required

Check Git:

```bash
git --version
```

If Git is not installed:

```bash
sudo apt update
sudo apt install git
```

Then verify:

```bash
git --version
```

---

# 1️⃣2️⃣ Clone the GitHub Repository

Go to your home directory:

```bash
cd ~
```

Then clone your repository:

```bash
git clone <YOUR-GITHUB-REPOSITORY>
```

Example:

```bash
git clone https://github.com/username/my-react-project.git
```

This downloads the repository from GitHub to your EC2 machine.

Check the files:

```bash
ls
```

You should see your project directory.

---

# 1️⃣3️⃣ Enter the Project

Example:

```bash
cd my-react-project
```

Check the current location:

```bash
pwd
```

You can also see files using:

```bash
ls
```

---

# 1️⃣4️⃣ Enter the Frontend Directory

If your repository structure is:

```text
my-react-project/
├── backend/
└── frontend/
```

Run:

```bash
cd frontend
```

Check:

```bash
ls
```

You should see files such as:

```text
package.json
src/
public/
vite.config.js
```

The exact files depend on your project.

---

# 1️⃣5️⃣ Install Dependencies

Run:

```bash
npm install
```

### What does this do?

`npm install` reads:

```text
package.json
```

and installs the required dependencies.

For example:

```text
package.json
      ↓
 npm install
      ↓
node_modules/
```

It may also create/update:

```text
package-lock.json
```

### Important

Do not upload `node_modules` to GitHub.

It is normally included in `.gitignore`:

```text
node_modules/
```

---

# 1️⃣6️⃣ Build the React Application

Run:

```bash
npm run build
```

For a Vite project, this usually executes:

```text
vite build
```

The source code is converted into optimized production files.

Example:

```text
React Source Code
       ↓
npm run build
       ↓
     dist/
```

Typical Vite output:

```text
dist/
├── index.html
└── assets/
    ├── index-xxxxx.js
    └── index-xxxxx.css
```

### Important

The `dist` directory is the production version of your frontend.

Nginx will serve these files.

---

# 1️⃣7️⃣ Install Nginx

Update Ubuntu's package information:

```bash
sudo apt update
```

### What does `apt update` do?

It refreshes Ubuntu's list of available packages.

It does **not** install or upgrade everything.

Then install Nginx:

```bash
sudo apt install nginx
```

Nginx is a web server.

Its job in this deployment is:

```text
Browser
   ↓
Nginx
   ↓
React production files
```

---

# 1️⃣8️⃣ Start Nginx

Run:

```bash
sudo systemctl start nginx
```

### What is `systemctl`?

`systemctl` is used to manage Linux services.

Here:

```text
systemctl
   ↓
start
   ↓
nginx
```

means:

> Start the Nginx service.

Check its status:

```bash
sudo systemctl status nginx
```

You should see:

```text
active (running)
```

Press:

```text
q
```

to exit the status screen.

---

# 1️⃣9️⃣ Enable Nginx After Reboot

Run:

```bash
sudo systemctl enable nginx
```

This tells Ubuntu:

> Automatically start Nginx whenever the server starts.

Difference:

```text
start
 ↓
Start Nginx now

enable
 ↓
Start Nginx automatically after reboot
```

Usually use both:

```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

---

# 2️⃣0️⃣ Test Nginx Before Deploying React

Open your browser and enter:

```text
http://<EC2-PUBLIC-IP>
```

For example:

```text
http://43.xxx.xxx.xxx
```

If everything is working, you should see the default Nginx page.

If you don't see it, check:

```bash
sudo systemctl status nginx
```

Also check the AWS Security Group rules described below.

---

# 2️⃣1️⃣ Copy React Build to Nginx

Nginx's default website directory on Ubuntu is:

```text
/var/www/html/
```

Your React production build is:

```text
dist/
```

Copy the contents of `dist`:

```bash
sudo cp -r dist/* /var/www/html/
```

### Understanding the command

```text
sudo
 ↓
Administrator permission

cp
 ↓
Copy

-r
 ↓
Recursive copy

dist/*
 ↓
Everything inside dist

/var/www/html/
 ↓
Destination
```

So:

```bash
sudo cp -r dist/* /var/www/html/
```

means:

> Copy all files and folders inside `dist` into Nginx's default web directory.

---

# 2️⃣2️⃣ Verify the Nginx Directory

Run:

```bash
ls /var/www/html/
```

You should see something similar to:

```text
index.html
assets/
```

Your React build is now inside Nginx's web directory.

---

# 2️⃣3️⃣ Configure AWS Security Group

This is done in the **AWS Console**, not inside Linux.

Go to:

```text
AWS Console
   ↓
EC2
   ↓
Instances
   ↓
Select your instance
   ↓
Security
   ↓
Security Groups
   ↓
Inbound Rules
```

Add:

```text
Type: HTTP
Protocol: TCP
Port: 80
Source: 0.0.0.0/0
```

Port 80 is the standard port for HTTP.

```text
HTTP  → 80
HTTPS → 443
```

---

# 2️⃣4️⃣ Open Your Application

Now open:

```text
http://<EC2-PUBLIC-IP>
```

Example:

```text
http://43.xxx.xxx.xxx
```

The request flow is:

```text
User Browser
     ↓
Internet
     ↓
AWS Security Group
     ↓
Port 80
     ↓
EC2
     ↓
Nginx
     ↓
/var/www/html/
     ↓
index.html
     ↓
React Application
```

🎉 Your frontend is now deployed.

---

# 🔄 Updating the Application

Suppose you modify your React application locally.

The normal workflow is:

```text
Local Code
    ↓
git add .
    ↓
git commit
    ↓
git push
    ↓
GitHub
```

Then SSH into EC2:

```bash
ssh -i "devTinder-secret.pem" ubuntu@<EC2-PUBLIC-DNS>
```

Go to the project:

```bash
cd ~/my-react-project/frontend
```

Pull the latest code:

```bash
git pull
```

Install dependencies if `package.json` changed:

```bash
npm install
```

Build again:

```bash
npm run build
```

Copy the new build:

```bash
sudo cp -r dist/* /var/www/html/
```

Then refresh your browser.

---

# ⚠️ Important: Environment Variables

If your React application uses environment variables, make sure the required production values are available **before running the build**.

For Vite, frontend environment variables usually look like:

```text
VITE_API_URL=...
```

Remember:

> Vite environment variables are embedded into the frontend during `npm run build`.

Therefore:

```text
Environment Variables
        ↓
npm run build
        ↓
dist/
```

Changing an environment variable after building will not automatically change the already-generated JavaScript files.

You normally need to rebuild.

---

# ⚠️ Important: React Router

If your React application uses:

```text
react-router-dom
```

and you directly open a route such as:

```text
http://your-ip/data
```

Nginx may return a `404`.

Why?

Because Nginx looks for:

```text
/var/www/html/data
```

but React Router expects the request to eventually reach:

```text
index.html
```

For a single-page React application, you may need an Nginx configuration such as:

```nginx
location / {
    try_files $uri $uri/ /index.html;
}
```

After changing Nginx configuration, test it:

```bash
sudo nginx -t
```

If the test succeeds:

```bash
sudo systemctl reload nginx
```

This is especially important for applications using React Router.

---

# 🔐 Important Security Notes

## Never upload your `.pem` file

Never do:

```bash
git add devTinder-secret.pem
```

Never push it to GitHub.

Add:

```text
*.pem
```

to `.gitignore`.

---

## Never expose database passwords

Do not hardcode:

```text
DB_PASSWORD=MySecretPassword
```

inside your GitHub repository.

Use environment variables or a proper secret-management solution.

---

## Don't use `chmod 777`

Avoid commands like:

```bash
chmod 777
```

unless you completely understand why you need them.

Giving everyone full permissions can create security problems.

---

# 🛠️ Useful Troubleshooting Commands

### Check Node

```bash
node -v
```

### Check npm

```bash
npm -v
```

### Check NVM

```bash
nvm --version
```

### Check current directory

```bash
pwd
```

### List files

```bash
ls
```

### List hidden files

```bash
ls -la
```

### Check Nginx status

```bash
sudo systemctl status nginx
```

### Test Nginx configuration

```bash
sudo nginx -t
```

### Restart Nginx

```bash
sudo systemctl restart nginx
```

### Reload Nginx without a full restart

```bash
sudo systemctl reload nginx
```

### View Nginx error logs

```bash
sudo tail -f /var/log/nginx/error.log
```

### View Nginx access logs

```bash
sudo tail -f /var/log/nginx/access.log
```

### Check what's listening on port 80

```bash
sudo ss -tulpn | grep :80
```

---

# 🧠 Important Commands to Remember

| Command         | Meaning                           |
| --------------- | --------------------------------- |
| `ssh`           | Connect to remote server          |
| `chmod`         | Change file permissions           |
| `curl`          | Transfer/download data            |
| `source`        | Load shell configuration          |
| `nvm`           | Manage Node.js versions           |
| `node -v`       | Check Node version                |
| `npm -v`        | Check npm version                 |
| `git clone`     | Download repository               |
| `cd`            | Change directory                  |
| `pwd`           | Show current directory            |
| `ls`            | List files                        |
| `npm install`   | Install dependencies              |
| `npm run build` | Create production build           |
| `sudo`          | Run with administrator privileges |
| `apt`           | Ubuntu package manager            |
| `systemctl`     | Manage services                   |
| `cp`            | Copy files                        |
| `nginx`         | Web server                        |

---

# 🎯 The Complete Deployment Cheat Sheet

Once you understand everything above, this is the short version you can keep for quick reference:

```bash
# Connect to EC2
ssh -i "devTinder-secret.pem" ubuntu@<EC2-PUBLIC-DNS>

# Install NVM
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash

# Reload shell
source ~/.bashrc

# Check NVM
nvm --version

# Install required Node version
nvm install 22.13.1

# Use Node version
nvm use 22.13.1

# Make it default
nvm alias default 22.13.1

# Verify
node -v
npm -v

# Clone project
git clone <YOUR-GITHUB-REPOSITORY>

# Enter project
cd <PROJECT>

# Enter frontend
cd frontend

# Install dependencies
npm install

# Build production files
npm run build

# Install Nginx
sudo apt update
sudo apt install nginx

# Start Nginx
sudo systemctl start nginx

# Enable Nginx after reboot
sudo systemctl enable nginx

# Copy React build
sudo cp -r dist/* /var/www/html/

# Check Nginx
sudo systemctl status nginx

# Test Nginx configuration if you modified it
sudo nginx -t

# Reload Nginx after configuration changes
sudo systemctl reload nginx
```

Then configure:

```text
AWS EC2
   ↓
Security Group
   ↓
Inbound Rules
   ↓
HTTP
TCP
Port 80
0.0.0.0/0
```

Finally:

```text
http://<EC2-PUBLIC-IP>
```

---

# 🧠 Remember This One Picture

```text
                👨‍💻 YOUR LAPTOP
                     │
                     │ git push
                     ↓
                  🐙 GitHub
                     │
                     │ git clone / git pull
                     ↓
              ☁️ AWS EC2
              Ubuntu Server
                     │
                     ↓
                    NVM
                     │
                     ↓
               Node.js 22.13.1
                     │
                     ↓
               npm install
                     │
                     ↓
              npm run build
                     │
                     ↓
                   dist/
                     │
                     ↓
                  🌐 Nginx
                     │
                     ↓
              /var/www/html/
                     │
                     ↓
                  Port 80
                     │
                     ↓
               🌍 Internet
                     │
                     ↓
                👤 Browser
```

## ⭐ Core Concept

You are **not deploying the React source code directly to the browser**.

You are doing:

```text
React Source Code
       ↓
npm run build
       ↓
Production Files
       ↓
dist/
       ↓
Nginx
       ↓
Browser
```

**EC2 = Computer**

**NVM = Node Version Manager**

**Node.js = JavaScript runtime**

**npm = Package manager**

**Git = Source-code/version control**

**Nginx = Web server**

**dist = Production frontend**

**Port 80 = HTTP entry point**
