Task Control 
============
playbook-db.yml imports configure_database.yml

Handlers
========
In this section, you use a text editor to create the config_db_handler.yml playbook file. This file installs a database server and creates users. When the database server is installed, the playbook restarts the service.  
config_db_handler.yml 

Handling Errors
===============
Ignore Failed Commands  
In this section, you learn how to ignore failed commands during the execution of playbooks.  
playbook-error.yml  

Override Task Failures  
In this section, you continue working with the playbook you created in the previous section. You insert a new task at the beginning of the playbook that executes a remote command and captures the output. The output of the command is used by the task that installs the mariadb-server package to override what Ansible considers to be a failure.  
ansible db -a 'yum -y remove mariadb-server' --become 
playbook-error-1.yml  

Override changed State for Tasks  
In this section, you update the task that installs the mariadb-server package by overriding the condition that triggers the changed state using the return code saved in the command_result.rc variable.  
Run playbook-error-2.yml twice and notice changed state for mariadb-server install task  

Implement Blocks/Rescue/Always in Playbooks  
ansible db -a 'yum -y remove mariadb-server' --become  
playbook-error-3.yml  

Implement Task Controls
=======================
Define Web Server Tasks  
In this section, you create the install_packages.yml task file and define a task that installs the latest versions of the httpd and mod_ssl packages. To install the two packages, you use the web_package and ssl_package variables. (You define the variables later when you create the main playbook.) You use a loop for installing the packages and set a condition that the packages are installed only if (1) the server belongs to the webservers group and (2) the available memory on the system is greater than the amount of memory defined by the memory variable. This variable is set upon import of the task and uses an Ansible fact to determine the available memory on the managed host. Finally, you add a task that starts the service defined by the web_service variable.  




 





