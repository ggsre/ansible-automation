Task Control 
============
playbook-db.yml imports configure_database.yml  
**=> configure_database.yml**  
**=> playbook-db.yml**  

Handlers
========
In this section, you use a text editor to create the config_db_handler.yml playbook file. This file installs a database server and creates users. When the database server is installed, the playbook restarts the service.  
**=> config_db_handler.yml**  

Handling Errors
===============
Ignore Failed Commands  
In this section, you learn how to ignore failed commands during the execution of playbooks.  
**=> playbook-error.yml**    

Override Task Failures  
In this section, you continue working with the playbook you created in the previous section. You insert a new task at the beginning of the playbook that executes a remote command and captures the output. The output of the command is used by the task that installs the mariadb-server package to override what Ansible considers to be a failure.  
**=> ansible db -a 'yum -y remove mariadb-server' --become**   
**=> playbook-error-1.yml**  

Override changed State for Tasks  
In this section, you update the task that installs the mariadb-server package by overriding the condition that triggers the changed state using the return code saved in the command_result.rc variable.  
**=> Run playbook-error-2.yml twice and notice changed state for mariadb-server install task**  

Implement Blocks/Rescue/Always in Playbooks  
**=> ansible db -a 'yum -y remove mariadb-server' --become**  
**=> playbook-error-3.yml**  

Implement Task Controls
=======================
Define Web Server Tasks  
In this section, you create the install_packages.yml task file and define a task that installs the latest versions of the httpd and mod_ssl packages. To install the two packages, you use the web_package and ssl_package variables. (You define the variables later when you create the main playbook.) You use a loop for installing the packages and set a condition that the packages are installed only if (1) the server belongs to the webservers group and (2) the available memory on the system is greater than the amount of memory defined by the memory variable. This variable is set upon import of the task and uses an Ansible fact to determine the available memory on the managed host. Finally, you add a task that starts the service defined by the web_service variable.  
**=> install_packages.yml**  

Define Tasks to Configure Web Server  
In this section, you create the configure_web.yml task file and add a task to it that checks whether or not the httpd package is installed. The output is registered in a variable. You then update the condition to consider the task as failed based on the return code of the command (the return code is 1 when a package is not installed).  
Next, you create a block that executes only if the httpd package is installed (using the return code that was captured in the first task). The block needs to start with a task that retrieves the file that the https_src variable defines (the variable is set in the main playbook) and copies it to hosts in the webservers hostgroup in the /etc/httpd/conf.d/ directory.  
Finally, you define the following tasks:

- A task that creates the ssl directory, which stores SSL certificates, under /etc/httpd/conf.d/ on the managed host with a mode of 0755.
- A task that creates the logs directory, which stores SSL logs, under /var/www/html/ on the managed host with a mode of 0755.
- A task that uses the stat module to ensure that the /etc/httpd/conf.d/ssl.conf file exists, and captures the output in a variable.
- A task that renames the /etc/httpd/conf.d/ssl.conf file as /etc/httpd/conf.d/ssl.conf.bak only if the file exists (based on the captured output from the previous task).
- A task that retrieves the SSL certificates file that the ssl_src variable defines (the variable is set in the main playbook), extracts it under the /etc/httpd/conf.d/ssl/ directory, and notifies the restart_services handler.
- A task that creates the index.html file under the /var/www/html/ directory that uses Ansible facts and has the customized content  

**=> configure_web.yml**  

Define Firewall Tasks  
In this section, you create the configure_firewall.yml task file, starting with a task that installs the package that the fw_package variable defines (the variable is set in the main playbook). You create a task that starts the service specified by the fw_service variable and a task that adds firewall rules for the http and https services using a loop. The rules need to be applied immediately and persistently. Tag all tasks with the production tag.  
**=> configure_firewall.yml**  

Define Main Playbook  
In this section, you create the playbook-webservers.yml playbook and target the hosts in the webservers group. You define a block that imports the three task files you just created.

For the task that imports the install_packages.yml playbook, you define the following variables:
- memory with a value of 256
- web_package with a value of httpd
- ssl_package with a value of mod_ssl  
For the task that imports the configure_web.yml file, you define the following variables:
- https_src with a value of /home/devops/lab2-files/https.conf
- ssl_src with a value of /home/devops/lab2-files/ssl.tar.gz  
For the task that imports the configure_firewall.yml playbook, you add a condition to import only the tasks tagged with the production tag and define the fw_package and fw_service variables with a value of firewalld.

In the rescue clause for the block, you define a task to install the httpd service and notify the restart_services handler to start the service upon its installation. You add an always statement that uses the shell module to query the status of the httpd service using systemctl and define the restart_services handler to restart both the httpd and firewalld services using a loop.





 





