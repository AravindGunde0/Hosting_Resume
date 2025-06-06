# Host Your Resume on AWS EC2 with a CI/CD Setup Using GitHub Actions


step-by-step explanation of each part of your GitHub Actions workflow named Push-to-EC2, which automatically deploys code to an EC2 server when code is pushed to the main branch:

🧾 Workflow Name
yaml
Copy
Edit
name: Push-to-EC2
This is just a label for the workflow. It appears in GitHub Actions UI.

🚀 Trigger Condition
yaml
Copy
Edit
on:
  push:
    branches:
      - main
This tells GitHub Actions to run the workflow only when code is pushed to the main branch.

🛠️ Job Definition
yaml
Copy
Edit
jobs:
  deploy:
    name: Deploy to EC2 on master branch push
    runs-on: ubuntu-latest
This defines a job named deploy.

runs-on: ubuntu-latest means this job runs on a GitHub-hosted Ubuntu virtual machine.

✅ Step 1: Checkout Code
yaml
Copy
Edit
- name: Checkout the files
  uses: actions/checkout@v2
This downloads your repo's code onto the virtual machine so it can be deployed.

actions/checkout@v2 is the official GitHub action for checking out code.

🚚 Step 2: Deploy Files via SCP
yaml
Copy
Edit
- name: Deploy to Server 1
  uses: easingthemes/ssh-deploy@main
  env:
    SSH_PRIVATE_KEY: ${{ secrets.EC2_SSH_KEY }}
    REMOTE_HOST: ${{ secrets.HOST_DNS }}
    REMOTE_USER: ${{ secrets.USERNAME }}
    TARGET: ${{ secrets.TARGET_DIR }}
This uses the ssh-deploy action to copy your code to an EC2 instance via SSH/SCP.

It authenticates using the private key (EC2_SSH_KEY) and connects to the host (HOST_DNS) as the user (USERNAME).

The code is uploaded to the directory defined by TARGET_DIR.

🔒 All values are stored in GitHub Secrets for security and not hard-coded in the file.

🖥️ Step 3: Run Remote Commands
yaml
Copy
Edit
- name: Executing remote ssh commands using ssh key
  uses: appleboy/ssh-action@master
  with:
    host: ${{ secrets.HOST_DNS }}
    username: ${{ secrets.USERNAME }}
    key: ${{ secrets.EC2_SSH_KEY }}
    script: |
      sudo apt-get -y update
      sudo apt-get install -y apache2
      sudo systemctl start apache2
      sudo systemctl enable apache2
      cd home
      sudo mv * /var/www/html
This step runs remote shell commands over SSH using appleboy/ssh-action.

It:

Updates package lists.

Installs Apache web server.

Starts and enables Apache to run on boot.

Navigates to the uploaded directory (cd home) — this might need to be corrected to an actual path.

Moves all files (*) to Apache's root directory (/var/www/html).

⚠️ Make sure cd home is the right directory — you may need to replace it with something like cd /home/ubuntu/your-project depending on the structure.
