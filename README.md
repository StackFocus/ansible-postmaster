# PostMaster Ansible Role
This is an Ansible role that deploys the web application [PostMaster](https://github.com/StackFocus/PostMaster) written by [StackFocus](https://github.com/StackFocus).

## Requirements
This role requires version 2.4.2 of the [geerlingguy.mysql](https://github.com/geerlingguy/ansible-role-mysql) Ansible role.
If you are installing this role through Ansible Galaxy, geerlingguy.mysql should be installed automatically for you.
Note that the geerlingguy.mysql role is only used when the variable `postmaster_install_mysql` is set to `True`.

This role must be run with sudo or as root, otherwise the role will fail.

## Role Variables:
* **postmaster_db_user** - the MySQL user for PostMaster to connect as.
* **postmaster_db_password** - the MySQL user's password.
* **postmaster_db_host** - the hostname or IP address of the MySQL server. This defaults to `localhost`.
* **postmaster_db_port** - the port that the MySQL server listens on. This defaults to `3306`.
* **postmaster_db_name** - the name of the MySQL database. This defaults to `servermail`.
* **postmaster_secret_key** - the Flask secret key that PostMaster will use (this should be a random string of characters)
* **postmaster_migrate_db** - determines whether or not to migrate/upgrade the PostMaster database. This defaults to `True`.
* **postmaster_install_mysql** - determines whether or not to install MySQL using the [geerlingguy.mysql](https://github.com/geerlingguy/ansible-role-mysql) role.
Read the documentation of that role to find out the configuration options needed for MySQL. This defaults to `False`.
* **postmaster_apache_port** - the port that the PostMaster virtual host listens on.
* **postmaster_apache_ssl_cert** - the path on the target node to the SSL certificate to use on the PostMaster virtual host.
If this is not set, the virtual host will not use SSL.
* **postmaster_apache_ssl_key** - the path on the target node to the SSL key to use on the PostMaster virtual host.
If this is not set, the virtual host will not use SSL.
* **postmaster_apache_ssl_chain** - the path on the target node to the SSL certificate chain to use on the PostMaster virtual host.
This is not required when configuring SSL, but it is required if `postmaster_apache_ssl_local_chain` is defined.
* **postmaster_apache_ssl_local_cert** - the local path to the SSL certificate to copy over to the path defined in `postmaster_apache_ssl_cert`.
This is not required.
* **postmaster_apache_ssl_local_key** - the local path to the SSL key to copy over to the path defined in `postmaster_apache_ssl_key`.
This is not required.
* **postmaster_apache_ssl_local_chain** - the local path to the SSL certificate chain to copy over to the path defined in `postmaster_apache_ssl_chain`.
This is not required.
* **postmaster_apache_ssl_cipher_suite** - the SSL cipher suite that the PostMaster virtual host will allow. This defaults to `AES256+EECDH:AES256+EDH`.
* **postmaster_apache_ssl_protocol** - the SSL protocols that the PostMaster virtual host will allow. This defaults to `All -SSLv2 -SSLv3`.
* **postmaster_clean_virtualenv** - delete and recreate the PostMaster virtualenv. This is useful between upgrades to delete old and no longer used Python dependencies.
This defaults to `False`.

## Playbook Example:
```yaml
- name: Deploy PostMaster
  hosts: mailserver
  become: yes

  vars:
  - postmaster_db_user: postmaster_user
  - postmaster_db_password: DbUserPassword
  - postmaster_db_host: localhost
  - postmaster_db_name: servermail
  - postmaster_migrate_db: True
  - postmaster_secret_key: SuperSecretKey
  - postmaster_apache_port: 8443
  - postmaster_apache_ssl_cert: /local/path/to/postmaster.crt
  - postmaster_apache_ssl_key: /local/path/to/postmaster.key
  - postmaster_install_mysql: True
  - mysql_root_password: MySqlRootPassword
  - mysql_enabled_on_startup: yes
  - mysql_databases:
    - name: "{{ postmaster_db_name }}"
  - mysql_users:
    - name: "{{ postmaster_db_user }}"
      password: "{{ postmaster_db_password }}"
      host: localhost
      priv: "{{ postmaster_db_name }}.*:ALL"

  roles:
   - postmaster
```
