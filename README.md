#WordPress Playbook (swrve-cms)

This playbook is used to automate, version and deploy WordPress running on multiple instances backed by the same MySQL database.


##Requirements

* Ansible 2.10.3
* Valid public SSH key installed on the remote hosts (.ssh/authorized_keys file).

##Directory structure

```
swrve-cms
│   ├── README.md
│   ├── ansible.cfg
│   ├── group_vars
│   │   └── credentials
│   ├── hosts
│   ├── playbook.yml
│   └── roles
│       ├── cms
│       │   ├── README.md
│       │   ├── defaults
│       │   │   └── main.yml
│       │   ├── files
│       │   ├── handlers
│       │   │   └── main.yml
│       │   ├── meta
│       │   │   └── main.yml
│       │   ├── tasks
│       │   │   └── main.yml
│       │   ├── templates
│       │   │   ├── htaccess.j2
│       │   │   ├── vhosts
│       │   │   │   ├── default-ssl.conf.j2
│       │   │   │   └── default.conf.j2
│       │   │   └── wp-config.php.j2
│       │   ├── tests
│       │   │   ├── inventory
│       │   │   └── test.yml
│       │   └── vars
│       │       └── main.yml
│       ├── database
│       │   ├── README.md
│       │   ├── defaults
│       │   │   └── main.yml
│       │   ├── files
│       │   ├── handlers
│       │   │   └── main.yml
│       │   ├── meta
│       │   │   └── main.yml
│       │   ├── tasks
│       │   │   └── main.yml
│       │   ├── templates
│       │   ├── tests
│       │   │   ├── inventory
│       │   │   └── test.yml
│       │   └── vars
│       │       └── main.yml
│       ├── php
│       │   ├── README.md
│       │   ├── defaults
│       │   │   └── main.yml
│       │   ├── files
│       │   ├── handlers
│       │   │   └── main.yml
│       │   ├── meta
│       │   │   └── main.yml
│       │   ├── tasks
│       │   │   └── main.yml
│       │   ├── templates
│       │   ├── tests
│       │   │   ├── inventory
│       │   │   └── test.yml
│       │   └── vars
│       │       └── main.yml
│       ├── ssl
│       │   ├── README.md
│       │   ├── defaults
│       │   │   └── main.yml
│       │   ├── files
│       │   ├── handlers
│       │   │   └── main.yml
│       │   ├── meta
│       │   │   └── main.yml
│       │   ├── tasks
│       │   │   └── main.yml
│       │   ├── templates
│       │   ├── tests
│       │   │   ├── inventory
│       │   │   └── test.yml
│       │   └── vars
│       │       └── main.yml
│       └── webserver
│           ├── README.md
│           ├── defaults
│           │   └── main.yml
│           ├── files
│           ├── handlers
│           │   └── main.yml
│           ├── meta
│           │   └── main.yml
│           ├── tasks
│           │   └── main.yml
│           ├── templates
│           ├── tests
│           │   ├── inventory
│           │   └── test.yml
│           └── vars
│               └── main.yml
```

##Architecture

- This playbook deploys WordPress on multiple EC2 instances and connects them to MYSQL Database deployed on a different EC2 instance.
- For this task, WordPress is deployed on 2 instances while MYSQL is deployed on the third instance.
- This architecture allows scalability and enables the WordPress instances talk to the same database.

##Execution

- Clone the repo **swrve-cms**:
https://github.com/jiwuh/swrve-cms
  
- Edit **ansible.config** file with the remote port, user and path to your private SSH Key file

- Update the **hosts** file with the correct Target IPs for both DB and CMS
  - DB: 
      - 54.147.177.124
  - CMS: 
      - 3.94.81.108
      - 3.93.0.2 (No SSH Access)
  
- Decrypt the credentials file (group_vars/credentials) and specify the database host and other credentials 

- Deploy swrve-cms from the root directory by running the playbook as below:
    ```
    $> ansible-playbook playbook.yml -i hosts --ask-vault-pass
    ```
  
- To view the deployed WordPress application, Enter the IP of either of the WordPress servers on a web browser:

    http://3.94.81.108
  
    https://3.94.81.108 (uses self-signed certificate)
  
    http://3.93.0.2 ====> I could not deploy to this server due to SSH Access

### The decisions made here were purely based on the resources provided for this test
### My Ideal solution would be to:
- Use AWS Managed RDS instead of installing the database on an EC2 instance.
- Have an Application Load Balancer in front of the WordPress instances.   
- Provision the Infrastructure (RDS, EC2, Security Groups, Load Balancers and AutoScaling Group) as code using Terraform.
- Create an AMI for WordPress using Packer (Terraform would provision the instances using the generated image)

    
