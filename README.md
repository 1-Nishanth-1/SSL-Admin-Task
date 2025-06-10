# SSL Admin Task

## 1. Initial Setup

- As I had burned through my Azure credits, I used the AWS VM.
- updated packages and set up security updates following [this aritcle](https://allthings.how/how-to-enable-automatic-security-updates-with-unattended-upgrades-on-ubuntu-24-04/)

## 2. Enhanced SSH Security

- Modified /etc/ssh/sshd_config and changed the necessary configurations. I also changed the SSH port to 2222
- Implemented fail2ban system for maximum of 5 failed attepts between 5 minutes and an exponential ban time starting with 1 hour. [reference](https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-ubuntu-20-04)

## 3. Firewall and Network Security

- Denied all incoming ports using
  ```bash
    sudo ufw default deny incoming
  ```
  Then allowed specifi ports by
  ```bash
    sudo ufw allow <port>/<protocol>
  ```
- I also had to change the incoming ports in the aws site, for the security group for the instance.
- Enable ufw logging by using the command
  ```bash
    sudo ufw logging on
  ```

## 4. User and Permission Management

- Created users and grouped them into Admins, Audits and users and set their permissions using `chmod `
- To set user quota I follwed [this article](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/6/html/storage_administration_guide/ch-disk-quotas) using the quota package.
- To backup the home directories, I created the following script

  ```bash
    #!/bin/bash
    DIR=/var/backups/exam
    mkdir -p "$DIR"
    tar -czf "$DIR/exam_1_$(date +%F).tar.gz" "/home/exam_1"
    tar -czf "$DIR/exam_2_$(date +%F).tar.gz" "/home/exam_2"
    tar -czf "$DIR/exam_3_$(date +%F).tar.gz" "/home/exam_3"
  ```

  I then used crontab ( `0 2 * * * /usr/local/bin/backup_exam_users.sh` ) to run this script from examadmin on 2 am every day.
  The compressed folders gets backed up to /var/backups/exam.

## 5. Web Server Deployment and Secure Configuration

- Installed nginx
- Watched [this youtube video](https://www.youtube.com/watch?v=9t9Mp0BGnyI) on nginx
- Used `certbot` to certify my domain for secure connection
- Downloaded both apps , ran them and configured them on nginx

## 6. Database Security

- Installed maria db using `sudo apt install mariadb-server`
- Configured the security stuff with command `sudo mysql_secure_installation`
- I Deleted root from mysql.user which are not in localhost to prevent remote root login
- Setup backup using dump command and in a similar way to user sirectory backup using crontab

## 7. Wireguard

- Installed Wireguard and [followed this video](https://www.youtube.com/watch?v=bVKNSf1p1d0) to setup vpn

## 8. Docker Fundamentals and Personal Website Deployment

- Created an image on thr basis of nginx image.
- Created DOCKERFILE.
- Deployed it using nginx in almost the same process as before

## 9. Ansible Automation in Dockerized Lab Environment

- It took a lot of time to learn ansible referencing various youtube videos and articles until i got the hang of it.
- Configured vim by using the basic.vim given in https://github.com/amix/vimrc.
- Added aliases like ll and .. into ~/bashrc
- I didn't get the time to install netdata. :/
- I used `ansible-galaxy init` setup file structure for roles
- To install Vscode I used the following method below, which I found in Microsofts website, by converting it int a script and executing it using ansilbe in remote server

```bash
sudo apt-get install wget gpg
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" |sudo tee /etc/apt/sources.list.d/vscode.list > /dev/null
rm -f packages.microsoft.gpg
sudo apt install apt-transport-https
sudo apt update
sudo apt install code
```
