![alt text](https://github.com/Daffalbar/remote-3server-using-ansible/blob/main/Gambar.png?raw=true)

### **Job Sheet: Manage Server using Ansible**

---

#### **Objective:**
This job sheet will guide you through the process of:
1. Setting up server Ubuntu, Debian, and CentOS
2. Setting up Hosts and Playbook
3. Create Apache2 and Httpd using Ansible
4. Create MySQL using Ansible
5. Create FTP using Ansible

---

### **Step 1: Preparing Your Environment**

#### **Exercise 1.1: Job Explanation**

There are explanation walktrough of job we will do:
1. Create 3 server using OS Debian, Ubuntu, and CentOS
2. Debian ansible will manage to install MySQL on Ubuntu and CentOS Server
3. Ubuntu ansible will manage to install Apache2 and HTTPD on Debian and CentOS server
4. CentOS ansible will manage to install FTP on Debian and Ubuntu server

#### **Exercise 1.2: Set Up Server**

Run the command below to set up our server
```ini
update
install ansible
install python3 python3-pip
install openssh-server
```

### **Step 2: Set Up SSH-Key**

#### **Exercise 2.1: Create Key**
```ini
ssh-keygen
```
Create key useful so that other servers can get access to connect to your server

#### **Exercise 2.2: Copy key**
```ini
ssh-copy-id user-target@ip-target
```
Copy other server key you can replace user-target with name user and ip-target with ip user from other server

### **Step 3: Set Up Hosts**

#### **Exercise 3.1: Make Hosts Directory**
Making ansible directory to holds the hosts file
```
mkdir /etc/ansible
```
You can skip this command if ansible directory is already created cause in some cases it is often found that the ansible directory is not created automatically

#### **Exercise 3.2: Create Hosts file**
```
nano /etc/ansible/hosts
```
Creating Hosts file to get connection from target server

#### **Exercise 3.3: Create Hosts Connection**
```
[target]
ip-target ansible_user=user-target ansible_become_pass=password-target
```
Creating connection to target server, replace connection name with name like debian, ubuntu, or centos, replace ip-target with the ip of target server, replace user-target with name of user of target server, and replace password-target with the password of super-user password of target server

#### **Exercise 3.4: Ping Connection**
```
ansible all -m ping -i /etc/ansible/hosts
```
If the result is PONG and the color is green your connection is work and if the result is UNREACHABLE and the color is red your connection is doesn't work. Try to check again in the hosts file the ip, name user, or password user

### **Step 4: Make Playbook**
#### **Exercise 4.1: Create Playbook.yml file**
```
nano playbook.yml
```
#### **Exercise 4.2: Make Playbook Script**
Ubuntu
```
---
- name: Install Apache2 on Debian and HTTPD on CentOS
  hosts: debian, centos
  become: yes
  tasks:
    - name: Install Apache2 on Debian
      apt:
        name: apache2
        state: present
      when: ansible_distribution == "Debian"

    - name: Install HTTPD on CentOS
      yum:
        name: httpd
        state: present
      when: ansible_distribution == "CentOS"
    
    - name: Start and Enable HTTPD
      systemd:
        name: httpd
        state: started
        enabled: yes
      when: ansible_distribution == "CentOS"

    - name: Ensure the firewall allows HTTP
      firewalld:
        service: http
        permanent: yes
        state: enabled
      when: ansible_distribution == "CentOS"

    - name: Reload the firewall
      command: firewall-cmd --reload
      when: ansible_distribution == "CentOS"
```
Debian
```
---
- name: Install MySQL on Ubuntu and CentOS
  hosts: ubuntu, centos
  become: yes
  tasks:
    - name: Install MySQL on Ubuntu
      apt:
        name: mysql-server
        state: present
      when: ansible_distribution == "Ubuntu"

    - name: Install MySQL on CentOS
      yum:
        name: mysql-server
        state: present
      when: ansible_distribution == "CentOS"

    - name: Active mysqld
      systemd:
        name: mysqld
        enabled: yes
      when: ansible_distribution == "CentOS"

    - name: Running mysqld
      systemd:
        name: mysqld
        state: started
      when: ansible_distribution == "CentOS"
```
CentOS
```
---
- name: Install FTP on Ubuntu and Debian
  hosts: ubuntu, debian
  become: yes
  tasks:
    - name: Install vsftpd on Ubuntu
      apt:
        name: vsftpd
        state: present
      when: ansible_distribution == "Ubuntu"

    - name: Install vsftpd on Debian
      apt:
        name: vsftpd
        state: present
      when: ansible_distribution == "Debian"
```
Copy the script depend on our server that you use

#### **Step 5: Add Super User Group**
### **Exercise 5.1: Entering SU Direktory**
```
su -
```
### **Exercise 5.2: Add user to SU"
```
usermod -aG sudo user
```
Replace user with our user name
### **Exercise 5.3: Exit**
```
exit
```

---

## **Start Ansible**
```
ansible-playbook playbook.yml
```
Start It.

---
