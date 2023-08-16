
Ansible playbook for deploying a sample application in a Docker container with the use of variables and Jinja2 templates. 
In this example, Deploying a simple "Hello World" web application using Nginx in a Docker container.

Create the Directory Structure:

my_app_deployment/
├── deploy_app.yml
├── templates/
│   └── nginx_site.conf.j2
├── files/
│   └── index.html
└── vars.yml


 Run the Playbook:

ansible-playbook deploy_app.yml
