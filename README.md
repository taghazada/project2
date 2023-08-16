
Ansible playbook for deploying a sample application in a Docker container with the use of variables and Jinja2 templates. In this example, Deploying a simple "Hello World" web application using Nginx in a Docker container.

Create the Directory Structure:

my_app_deployment/
├── deploy_app.yml
├── templates/
│   └── nginx_site.conf.j2
├── files/
│   └── index.html
└── vars.yml

1) Create vars.yml to Define Variables:

---
app_name: my_app
container_port: 80
host_port: 8080
domain_name: site.demo.oldbaku.net

2) Create 'nginx_site.conf.j2' Template:

server {
    listen {{ host_port }};
    server_name {{ domain_name }};

    location / {
        root /usr/share/nginx/html;
        index index.html index.htm;
    }
}

3) Create index.html File in the 'files' directory:


<!DOCTYPE html>
<html>
<head>
    <title>{{ app_name }} - Hello World</title>
</head>
<body>
    <h1>Hello, world! This is {{ app_name }}.</h1>
</body>
</html>

4) Create deploy_app.yml Playbook:

---
- name: Deploy Web Application in Docker
  hosts: dev.demo.oldbaku.net
  become: true

  vars_files:
    - vars.yml

  tasks:
    - name: Install Docker and Docker Compose
      apt:
        name:
          - docker.io
          - docker-compose
        state: present
      become: true

    - name: Start Docker service
      service:
        name: docker
        state: started
        enabled: true

    - name: Create Docker network
      docker_network:
        name: {{ app_name }}_network

    - name: Copy index.html to host
      copy:
        src: files/index.html
        dest: /var/www/{{ app_name }}/index.html
      become: true

    - name: Configure Nginx for the application
      template:
        src: templates/nginx_site.conf.j2
        dest: /etc/nginx/sites-available/{{ app_name }}
      become: true

    - name: Enable Nginx site
      file:
        src: /etc/nginx/sites-available/{{ app_name }}
        dest: /etc/nginx/sites-enabled/{{ app_name }}
        state: link
      become: true

    - name: Reload Nginx
      service:
        name: nginx
        state: reloaded
      become: true

    - name: Run Docker container
      docker_container:
        name: {{ app_name }}_container
        image: nginx:latest
        ports:
          - "{{ host_port }}:{{ container_port }}"
        networks:
          - name: {{ app_name }}_network
        volumes:
          - "/var/www/{{ app_name }}:/usr/share/nginx/html:ro"
      become: true

      
5) Run the Playbook:

ansible-playbook deploy_app.yml
#   p r o j e c t 2 
 
 
