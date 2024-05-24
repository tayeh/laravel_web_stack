# Ansible Role: `laravel_web_stack`

Description
-----------

The `laravel_web_stack` Ansible role is designed to automate the setup of a web server optimized for hosting Laravel applications. This role performs the following tasks:

1. **System Update and Initial Tools Installation**:
   - Updates the system packages to the latest versions.
   - Installs essential tools such as `vim`, `curl`, `wget`, and `git`.
   - create users for ssh access and secure ssh
   - Give wheel group passwordless sudo powers

2. **PHP Installation with Remi Repository**:
   - Installs and configures the Remi repository for PHP.
   - Installs PHP 8.3 along with necessary extensions (`php-cli`, `php-fpm`, `php-mysqlnd`, `php-opcache`, `php-xml`, `php-gd`, `php-mbstring`, `php-tokenizer`, `php-json`, `php-bcmath`, `php-zip`).

3. **MariaDB Installation**:
   - Installs MariaDB server.
   - Create database, users.

4. **Nginx Installation and Configuration**:
   - Installs Nginx web server.
   - Configures Nginx to serve the Laravel application.

5. **Laravel Application Deployment**:
   - Installs Composer for dependency management.
   - Clones the specified Laravel application repository from Git.
   - Installs Laravel dependencies using Composer.
   - Sets appropriate permissions for storage and cache directories.
   - Configures the application environment and generates the application key.

This role ensures a smooth and automated deployment process for Laravel applications, providing a reliable and consistent environment for development and production.

Requirements
------------

- Ansible 2.9+
- CentOS/RHEL 9

Role Variables
--------------

### Users
Define the users to be created on the server:
```yaml
# List of users to be created
users:
  - { name: tayeh, groups: "wheel" }
  - { name: baraa, groups: "wheel" }
  - { name: "{{ php_user }}" }  # User for php-fpm and Laravel files

php_user: app  # Default user for php-fpm and Laravel files
```

### MariaDB
Define MariaDB root password and database credentials:
```yaml
# MariaDB root password
mariadb_root_password: "secure_root_password"

# List of databases and users to be created
mariadb:
  - db_name: laravel
  - db_user: laravel_user
  - db_pass: ""
```

### Laravel Project
Define the Git repository and project path for the Laravel application:
```yaml
# Git repository URL for the Laravel application
git_repo: https://github.com/laravel/laravel.git  # SSH or HTTPS allowed

# Branch to be checked out
git_branch: main

# Path where the project will be deployed
project_path: "/var/www/code"

# Path to the private key for accessing the Git repository
git_private_key: "~/.ssh/id_bitbucket"
```

### Nginx
Define the Nginx site URL:
```yaml
# URL of the site to be configured in Nginx
site_url: test.tayeh.me
```

### PHP-FPM
Define PHP-FPM and PHP settings:
```yaml
# PHP-FPM socket and settings
php_fpm_listen: /run/php-fpm/laravel.sock
php_memory_limit: "512M"
php_post_max_size: "200M"
php_upload_max_filesize: "200M"
php_max_execution_time: 120
php_max_input_time: 180
php_timezone: Asia/Riyadh
```

Dependencies
------------

None.

Usage
----------------

1. Clone this role into your Ansible roles directory:
```bash
ansible-galaxy install tayeh/laravel_web_stack
```
2. Create a playbook that uses this role and specify the necessary variables:

```yaml
---
- hosts: webservers
  become: yes
  vars:
    users:
      - { name: tayeh, groups: "wheel" }
      - { name: baraa, groups: "wheel" }
      - { name: "{{ php_user }}" }
    php_user: app
    mariadb_root_password: "secure_root_password"
    mariadb:
      - db_name: laravel
      - db_user: laravel_user
      - db_pass: "laravel_password"
    git_repo: https://github.com/laravel/laravel.git
    git_branch: main
    project_path: "/var/www/code"
    git_private_key: "~/.ssh/id_bitbucket"
    site_url: test.tayeh.me
    php_fpm_listen: /run/php-fpm/laravel.sock
    php_memory_limit: "512M"
    php_post_max_size: "200M"
    php_upload_max_filesize: "200M"
    php_max_execution_time: 120
    php_max_input_time: 180
    php_timezone: Asia/Riyadh
  roles:
    - laravel_web_stack
```

3. Run the playbook:
```bash
ansible-playbook -i your_inventory_file site.yml
```

License
-------

GPL-2.0

Author Information
------------------

This role was created by Mohammed Tayeh <info@tayeh.me>
