![image](https://github.com/ekomoku/ekom-pbl/assets/66005935/415a6cc1-a74a-48b5-895e-d975d1cd1559)
#### CONFIGURING THE JENKINS SERVER FOR DEPLOYMENT

Provision a RHEL 8 instance on AWS and name it jenkins-ansible


Connect to our vscode through remote SSH:

On the VS code, goto the lower left corner of the window, click 'Open a Remote Window' ( we want to connect to a remote host which is the 'jenkins-ansible' server on AWS) and on the drop down, click "connect to Host ...."

then click " Configure SSH Hosts ...", select /home/ekom/.ssh/config and add the following code to the config file 



~~~
# Read more about SSH config files: https://linux.die.net/man/5/ssh_config
Host Jenkins-Ansible
    HostName 3.85.170.246
    User ubuntu
    IdentityFile /home/ekom/Downloads/devopskeypair.pem
    ForwardAgent yes
    ControlPath /tmp/ansible-ssh-%h-%p-%r
    ControlMaster auto
    ControlPersist 10m
~~~




Edit the "HostName" IP address to the public IP address of your server 

"IdentityFile" to the correct path of your keypair.

"User" = name of the server type ( ubuntu or ec2-user)


Then on VS code, click 'Open a Remote Window' on the lower left corner of the window. select 'connect current window to a host'. At this point, you should see your host listed. Select and connect to it.


To setup our jenkins and clone the ansible-config-mgt repository from our github, I created a file

$ sudo vi jenkins-install.sh



![Screenshot from 2023-12-18 10-22-25](https://github.com/ekomoku/ekom-pbl/assets/66005935/d06ef955-bd94-40a6-afc8-b8f4c4a81bd7)



add the bash script below



~~~
#!/bin/bash

echo "#### Install git ####"
sudo yum install git -y

echo "#### Cloning Git-Repo ####"
git clone https://github.com/ekomoku/ansible-config-mgt-dybran.git

echo "#### Install wget and unzip -y ####"
sudo yum install wget unzip -y

echo "#### Download Repo from URL ####"
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

echo "#### Upgrading Repo ####"
sudo yum upgrade -y

echo "#### Installing Java 11 and Jenkins ####"
sudo yum install fontconfig java-17-openjdk
sudo yum install jenkins -y

echo "#### Reload Deamon ####"
sudo systemctl daemon-reload

echo "#### Start and Enable Jenkins ####"
sudo systemctl start jenkins
sudo systemctl enable jenkins
~~~


Note: Always visit jenkins website for offical documentation on guides to install jenkins e.g https://www.jenkins.io/doc/book/installing/linux/#red-hat-centos


![Screenshot from 2023-12-18 10-20-51](https://github.com/ekomoku/ekom-pbl/assets/66005935/eb75cddc-3e1b-4293-aa7d-5ac20caa1e7a)


Then we make the script executable by running the command

$ sudo chmod +x jenkins-install.sh

and run the script

$ ./jenkins-install.sh





![Screenshot from 2023-12-18 14-27-14](https://github.com/ekomoku/ekom-pbl/assets/66005935/80ef39dd-ec9e-48ff-a16f-33471142dede)




![Screenshot from 2023-12-18 14-32-31](https://github.com/ekomoku/ekom-pbl/assets/66005935/0e836fe6-9ec9-490c-bd26-4d38aeaf431f)




![Screenshot from 2023-12-18 14-35-59](https://github.com/ekomoku/ekom-pbl/assets/66005935/ecf96106-c1de-4979-b710-0999437b5b47)



![Screenshot from 2023-12-18 14-40-08](https://github.com/ekomoku/ekom-pbl/assets/66005935/d273d293-e98a-46f8-bff8-9b3c9ee9efe6)




![Screenshot from 2023-12-18 14-48-56](https://github.com/ekomoku/ekom-pbl/assets/66005935/f295fc58-ed49-40d1-8b3c-8231b18ad39d)



![Screenshot from 2023-12-18 14-49-30](https://github.com/ekomoku/ekom-pbl/assets/66005935/aeae41fe-2336-498d-a398-0bdf3fc4b399)



Blue Ocean now shows up on the Dashboard



![Screenshot from 2023-12-18 14-51-16](https://github.com/ekomoku/ekom-pbl/assets/66005935/a8e15253-0d55-446c-8ef5-ab0b67870bb1)




Click on the Blue Ocean to create a new pipeline



![Screenshot from 2023-12-18 15-02-55](https://github.com/ekomoku/ekom-pbl/assets/66005935/4388a41f-7d77-4e7f-9430-46b63a557a54)



Select Github



![Screenshot from 2023-12-18 15-07-15](https://github.com/ekomoku/ekom-pbl/assets/66005935/690a2206-fc38-43a5-83b1-1957926a47f4)




![Screenshot from 2023-12-18 15-08-10](https://github.com/ekomoku/ekom-pbl/assets/66005935/f1b66297-0860-4eb3-94c7-dd15e2935009)




Connect jenkins with Github and Login to GitHub & Generate an Access token




![Screenshot from 2023-12-18 15-10-12](https://github.com/ekomoku/ekom-pbl/assets/66005935/87705285-dd69-401e-a24c-0fc2ad6a7441)




![Screenshot from 2023-12-18 15-11-25](https://github.com/ekomoku/ekom-pbl/assets/66005935/78d30672-6277-48ea-a4ae-cfe2f055b225)




![Screenshot from 2023-12-18 15-13-46](https://github.com/ekomoku/ekom-pbl/assets/66005935/87d8b816-b444-4c3c-847a-67aeba6b7b70)




Generate access token, Copy Access Token and paste in the space provided to connect to github.

Then select the ansible-config-mgt-dybran repository and click on create pipeline.




![Screenshot from 2023-12-18 15-17-22](https://github.com/ekomoku/ekom-pbl/assets/66005935/e3e24769-990a-4b55-9010-c13d1a1a2987)





![Screenshot from 2023-12-18 15-19-23](https://github.com/ekomoku/ekom-pbl/assets/66005935/a4ca3596-fc45-41b3-bf56-a7243dd8d733)




At this point we do not have a Jenkinsfile in the ansible-config-mgt-dybran repository.

Click on "Administration" to exit the Blue Ocean console.

Here is our newly created pipeline. It takes the name of your GitHub repository





![Screenshot from 2023-12-18 15-21-21](https://github.com/ekomoku/ekom-pbl/assets/66005935/11275e22-7a65-49e5-816c-a14e3e220bf1)




Inside the ansible-config-mgt-dybran, create a new directory deploy and start a new Jenkinsfile inside the directory.





![Screenshot from 2023-12-18 15-45-33](https://github.com/ekomoku/ekom-pbl/assets/66005935/7d1849b3-b53a-4854-9ab8-2d3614fb0bd1)




Add the code snippet below to start building the Jenkinsfile gradually. This pipeline currently has just one stage called Build and the only thing we are doing is using the shell script module to echo Building Stage.




~~~
pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }
    }
}
~~~




![Screenshot from 2023-12-18 17-24-49](https://github.com/ekomoku/ekom-pbl/assets/66005935/04f3e16b-931c-493e-9789-171cc37dc53f)




Go into the ansible-config_mgt in the jenkins UI, and click on "configure" to configure the jenkins.




![Screenshot from 2023-12-18 17-27-40](https://github.com/ekomoku/ekom-pbl/assets/66005935/843774d3-4059-4896-a3bc-d6a5158001bd)




Click on Build Configuration and edit the script path to deploy/Jenkinsfile



Back to the pipeline again and click "Build now"

This will trigger a build and you will be able to see the effect of our basic Jenkinsfile configuration by going through the console output of the build.



![Screenshot from 2023-12-19 15-05-29](https://github.com/ekomoku/ekom-pbl/assets/66005935/ad183ae4-15b7-4258-bc9b-d235769b42e8)





![Screenshot from 2023-12-19 15-06-13](https://github.com/ekomoku/ekom-pbl/assets/66005935/f46c7489-5388-48c6-9517-3ed84c3d0378)




![Screenshot from 2023-12-19 15-09-50](https://github.com/ekomoku/ekom-pbl/assets/66005935/ec01e6bf-3b66-4719-8587-af259fdc5fbb)




Click on Blue Ocean and seen the build



![Screenshot from 2023-12-19 15-10-31](https://github.com/ekomoku/ekom-pbl/assets/66005935/6671546f-593a-404c-82f1-67760650ad5d)



This pipeline has multibranchs. This means, if there were more than one branch in GitHub, Jenkins would have scanned the repository to discover them all and we would have been able to trigger a build for each branch.


To see this in action, we will create a new git branch and name it feature/jenkinspipeline-stages

$ git checkout -b feature/jenkinspipeline-stages


Currently we only have the Build stage. Let us add other stages. Paste the code snippet below.





~~~
   pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }
    }
}
~~~





![Screenshot from 2023-12-19 15-12-31](https://github.com/ekomoku/ekom-pbl/assets/66005935/f8a20e36-222a-45fd-a6f8-4c8562a65caf)




Push the new changes to GitHub.

$ git status

$ git add .

$ git commit -m "<commit-message>"

$  git push origin feature/jenkinspipeline-stages




![Screenshot from 2023-12-19 15-13-27](https://github.com/ekomoku/ekom-pbl/assets/66005935/4eb917f9-7a32-49db-bf9a-7ca6f48ce2e2)



To make your new branch show up in Jenkins, we need to tell Jenkins to scan the repository.




![Screenshot from 2023-12-19 15-14-15](https://github.com/ekomoku/ekom-pbl/assets/66005935/198e47d2-6059-4ff5-94a3-2f4544eb93ee)





![Screenshot from 2023-12-19 15-14-52](https://github.com/ekomoku/ekom-pbl/assets/66005935/e96ad043-0186-463d-baa8-7db3f029c6f8)




![Screenshot from 2023-12-19 15-15-16](https://github.com/ekomoku/ekom-pbl/assets/66005935/19eeedc4-827e-4ad9-93ca-823050882f0a)




Click on the ansible-config-mgt job and then click on "Administration" button to go back to the jenkins UI.



#### RUNNING ANSIBLE PLAYBOOK FROM JENKINS


Now that we have a broad overview of a typical Jenkins pipeline. Let us get the actual Ansible deployment to work.


We will be setting up database and nginx on two different instances using ansible playbook on jenkins UI.





Spin up two AWS instances (Ubuntu database and RHEL Nginx)






To install Ansible on Jenkins, We also need to install python3 as it is a dependency for ansible to be able to successfully run a database role.

$ sudo dnf install -y ansible-core
$ ansible --version




![Screenshot from 2023-12-20 12-04-55](https://github.com/ekomoku/ekom-pbl/assets/66005935/d0414752-e72e-4f69-be05-5e2fe6a7c102)





Search for the Python3 for mysql

$ sudo yum search python3 | grep -i mysql





![Screenshot from 2023-12-20 12-26-47](https://github.com/ekomoku/ekom-pbl/assets/66005935/2d809d8a-18ab-428d-a59b-619d10fe0d60)





then install

$ sudo yum install python3-PyMySQL.noarch






![Screenshot from 2023-12-20 12-32-15](https://github.com/ekomoku/ekom-pbl/assets/66005935/fb8e7e4a-4688-4fe6-8241-b16a23d3afd4)






![Screenshot from 2023-12-20 12-33-30](https://github.com/ekomoku/ekom-pbl/assets/66005935/c29f2db8-e40b-4637-8832-8d6604ffb350)





Installing Ansible plugin in Jenkins UI




![Screenshot from 2023-12-20 12-37-53](https://github.com/ekomoku/ekom-pbl/assets/66005935/15e4961e-743b-4731-957b-2c7ec9911e39)





Then go to "global tool configuration" under "manage jenkins > Tools" to set up ansible plugin to work with jenkins






![Screenshot from 2023-12-20 12-45-38](https://github.com/ekomoku/ekom-pbl/assets/66005935/b3da4fa0-7173-4638-b710-0d5f077c4ec7)




To get the path to ansible

$ which anible




![Screenshot from 2023-12-20 12-48-56](https://github.com/ekomoku/ekom-pbl/assets/66005935/5f1c05c8-29a2-4fde-b48f-dda2942355ac)





![Screenshot from 2023-12-20 12-49-29](https://github.com/ekomoku/ekom-pbl/assets/66005935/a9299601-9868-467f-8cb3-17cb9561ce5e)






Creating Jenkinsfile to run the ansible playbook on jenkins.




Jenkins needs to export the ANSIBLE_CONFIG environment variable. You can put the ansible.cfg file alongside Jenkinsfile in the deploy directory. This way, anyone can easily identify that everything in there relates to deployment.






![Screenshot from 2023-12-20 13-21-17](https://github.com/ekomoku/ekom-pbl/assets/66005935/2a94fa28-036f-48cc-a747-2ce9e62cb060)






We need to set the credentials for ansible to be able to work with jenkins smoothly. To do this we go to manage credentials under manage jenkins > Credentials > global.






![Screenshot from 2023-12-20 13-23-09](https://github.com/ekomoku/ekom-pbl/assets/66005935/f3281473-f593-4ebe-91f3-8ea102e734c9)






![Screenshot from 2023-12-20 13-24-55](https://github.com/ekomoku/ekom-pbl/assets/66005935/0d47439c-6d54-4f72-84cc-f6305e52cb85)




Click on +Add Credentials






![Screenshot from 2023-12-20 13-26-16](https://github.com/ekomoku/ekom-pbl/assets/66005935/cf8ed9ed-5df8-4ba6-986f-9c7fe141e902)






![Screenshot from 2023-12-20 13-29-00](https://github.com/ekomoku/ekom-pbl/assets/66005935/32a751dc-210b-40a3-8850-8da48a4741a9)







We copy the content of our private key into the section "password".






![Screenshot from 2023-12-20 13-41-23](https://github.com/ekomoku/ekom-pbl/assets/66005935/9fe46502-0872-4a85-90dd-288d383a02f6)





Using the Pipeline Syntax tool in Ansible, generate the syntax to create environment variables to set.






![Screenshot from 2023-12-20 13-44-16](https://github.com/ekomoku/ekom-pbl/assets/66005935/8fea6c08-0eb2-45e7-8901-6bdd71eb1a85)





Click on "Pipeline Syntax", then under 'Steps" click the dropdown and select"ansibleplaybook:invoke an ansibleplaybook" and complete the field as shown;





Then click' Generate Pipeline Script






![Screenshot from 2023-12-20 14-19-59](https://github.com/ekomoku/ekom-pbl/assets/66005935/79675ce1-3641-4213-bc12-af30c2f34f71)






![Screenshot from 2023-12-20 14-21-25](https://github.com/ekomoku/ekom-pbl/assets/66005935/34ad5874-bb0a-4f2c-8ea3-b303168e3235)






![Screenshot from 2023-12-20 14-21-45](https://github.com/ekomoku/ekom-pbl/assets/66005935/000552e9-d632-4cb5-8232-6fd94b72f66a)





The generated script is shown below. Copy it and add to the Jenkinsfile in the "Run Ansible Playbook" stage





![Screenshot from 2023-12-20 14-23-20](https://github.com/ekomoku/ekom-pbl/assets/66005935/1667024e-1b12-4f19-aa34-7794ea570f42)



On a new branch, update Jenkinsfile with the following





~~~
  pipeline {

   agent any

   environment {
    ANSIBLE_CONFIG='${WORKSPACE}/deploy/ansible.cfg'
   }

  parameters {
      string(name: 'inventory', defaultValue: 'dev',  description: 'This is the inventory file for the environment to deploy configuration')
  }

   stages {

    stage('Initial Clean Up') {
      steps {
        dir('${WORKSPACE}') {
         deleteDir()
        }

      }
    }


    stage('Checkout SCM') {
      steps {
        git branch: 'main', url: 'https://github.com/ekomoku/ansible-config-mgt-new.git'
      }
    }

    stage('Setting Roles Path in the Ansible.cfg File') {

      steps {
        sh 'echo ${WORKSPACE}'
        sh 'sed -i "3 a roles_path=${WORKSPACE}/roles" ${WORKSPACE}/deploy/ansible.cfg'
      }
    }

    stage('Run Ansible Playbook') {

      steps {
        ansiblePlaybook colorized: true, credentialsId: 'private-key', disableHostKeyChecking: true, installation: 'ansible', inventory: 'inventory/${inventory}', playbook: 'playbooks/site.yml'

      }
    }

    stage('Clean Up') {
      steps {
         cleanWs(cleanWhenAborted: true, cleanWhenNotBuilt: true, cleanWhenUnstable: true, cleanWhenFailure: true)
      }

    }
     
   }
}
~~~





Then in the inventory/dev file, we update the IP address for our database and nginx.




~~~
nginx ansible_host=<Private-IP-address> ansible_ssh_user=<ec2-user>
db ansible_host=<Private-IP-address> ansible_ssh_user=<ubuntu>
~~~




![Screenshot from 2023-12-20 14-49-29](https://github.com/ekomoku/ekom-pbl/assets/66005935/db8e6c35-360b-4765-b10c-df3bdc87bd1d)





Create mysql and nginx roles





![Screenshot from 2023-12-20 15-22-29](https://github.com/ekomoku/ekom-pbl/assets/66005935/4d763041-ba89-4ae8-a841-0984c64359b1)




We set up roles for the database and nginx. The database roles will create a database - tooling and a database user - webaccess. We will go ahead and edit the _defaults/main.yml file in the mysql role




$ cd roles
$ ansible-galaxy init mysql
$ ansible-galaxy init nginx






Paste this snipet in mysql>defaults/main.yml



~~~
---
# Set this to the user ansible is logging in as - should have root
# or sudo access
mysql_user_home: /root
mysql_user_name: root
mysql_user_password: root

# The default root user installed by mysql - almost always root
mysql_root_home: /root
mysql_root_username: root
mysql_root_password: root

# Set this to `true` to forcibly update the root password.
mysql_root_password_update: false
mysql_user_password_update: false

mysql_enabled_on_startup: true

# Whether my.cnf should be updated on every run.
overwrite_global_mycnf: true

# The following variables have a default value depending on operating system.
# mysql_config_file: /etc/my.cnf
# mysql_config_include_dir: /etc/my.cnf.d

# Pass in a comma-separated list of repos to use (e.g. "remi,epel"). Used only
# for RedHat systems (and derivatives).
mysql_enablerepo: ""

# Define a custom list of packages to install; if none provided, the default
# package list from vars/[OS-family].yml will be used.
# mysql_packages:
#   - mysql
#   - mysql-server
#   - MySQL-python

mysql_python_package_debian: python3-mysqldb

# MySQL connection settings.
mysql_port: "3306"
mysql_bind_address: '0.0.0.0'
mysql_skip_name_resolve: false
mysql_datadir: /var/lib/mysql
mysql_sql_mode: ~
# The following variables have a default value depending on operating system.
# mysql_pid_file: /var/run/mysqld/mysqld.pid
# mysql_socket: /var/lib/mysql/mysql.sock

# Log file settings.
mysql_log_file_group: mysql

# Slow query log settings.
mysql_slow_query_log_enabled: false
mysql_slow_query_time: "2"
# The following variable has a default value depending on operating system.
# mysql_slow_query_log_file: /var/log/mysql-slow.log

# Memory settings (default values optimized ~512MB RAM).
mysql_key_buffer_size: "256M"
mysql_max_allowed_packet: "64M"
mysql_table_open_cache: "256"
mysql_sort_buffer_size: "1M"
mysql_read_buffer_size: "1M"
mysql_read_rnd_buffer_size: "4M"
mysql_myisam_sort_buffer_size: "64M"
mysql_thread_cache_size: "8"
mysql_query_cache_type: "0"
mysql_query_cache_size: "16M"
mysql_query_cache_limit: "1M"
mysql_max_connections: "151"
mysql_tmp_table_size: "16M"
mysql_max_heap_table_size: "16M"
mysql_group_concat_max_len: "1024"
mysql_join_buffer_size: "262144"

# Other settings.
mysql_lower_case_table_names: "0"
mysql_wait_timeout: "28800"
mysql_event_scheduler_state: "OFF"

# InnoDB settings.
mysql_innodb_file_per_table: "1"
# Set .._buffer_pool_size up to 80% of RAM but beware of setting too high.
mysql_innodb_buffer_pool_size: "256M"
# Set .._log_file_size to 25% of buffer pool size.
mysql_innodb_log_file_size: "64M"
mysql_innodb_log_buffer_size: "8M"
mysql_innodb_flush_log_at_trx_commit: "1"
mysql_innodb_lock_wait_timeout: "50"

# These settings require MySQL > 5.5.
mysql_innodb_large_prefix: "1"
mysql_innodb_file_format: "barracuda"

# mysqldump settings.
mysql_mysqldump_max_allowed_packet: "64M"

# Logging settings.
mysql_log: ""
# The following variables have a default value depending on operating system.
# mysql_log_error: /var/log/mysql/mysql.err
# mysql_syslog_tag: mysql

mysql_config_include_files: []
#  - src: path/relative/to/playbook/file.cnf
#  - { src: path/relative/to/playbook/anotherfile.cnf, force: yes }

# Databases.
mysql_databases:
   - name: homestead
     collation: utf8_general_ci
     encoding: utf8
     replicate: 1

# Users.
mysql_users:
   - name: homestead
     host: 172.31.83.27
     password: sePret^i
     priv: '*.*:ALL,GRANT'

# # Databases.
# mysql_databases:
#    - name: tooling
#      collation: utf8_general_ci
#      encoding: utf8
#      replicate: 1

# # Users.
# mysql_users:
#    - name: webaccess
#      host: 0.0.0.0
#      password: Sa4la2xa#
#      priv: '*.*:ALL,GRANT'

mysql_disable_log_bin: false

# Replication settings (replication is only enabled if master/user have values).
mysql_server_id: "1"
mysql_max_binlog_size: "100M"
mysql_binlog_format: "ROW"
mysql_expire_logs_days: "10"
mysql_replication_role: ''
mysql_replication_master: ''
mysql_replication_master_inventory_host: "{{ mysql_replication_master }}"

# Same keys as `mysql_users` above.
mysql_replication_user: []
~~~






Edit the highlighted section as show below;




![Screenshot from 2023-12-20 17-39-05](https://github.com/ekomoku/ekom-pbl/assets/66005935/c354aa4a-e77a-4cc3-97a0-96902c616f65)





Update The playbooks/site.yml with the code 



~~~
 - name: installing nginx
   hosts: nginx
 - name: nginx assignment
   import_playbook: ../static-assignments/nginx.yml

 - name: installing database
   hosts: db
 - name: db assignment
   import_playbook: ../static-assignments/db.yml
~~~




![Screenshot from 2023-12-20 17-46-33](https://github.com/ekomoku/ekom-pbl/assets/66005935/51812651-dede-4825-b0b0-3adeb7ca375a)




