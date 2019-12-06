# repmgr-ansible
Automatisation repmgr

#repmgr
This role installs and configures repmgr for Postgresql replication

#Requirements
Assumes PostgreSQL is already installed and running.
You should have the include_dir directive set in your postgresql.conf. See the repmgr_postgresql_conf default.
Sample
include_dir = '/etc/postgresql/conf.d'

#Role Variables
See defaults/main.yml

#Usage
In the playbook for the master (Change the IP of master and slave) :
- hosts: postgresql_master
  roles: 
  - role: repmgr
    repmgr_is_master: yes
    repmgr_node_id: 1
    repmg_ip_conn: ip_of_master
    node_name: node1
- hosts: postgresql_replicas
  roles:
  - role: repmgr
    repmgr_node_id: 2
    repmg_ip_conn: ip_of_standby
    node_name: node2

#Host/Inventary file
Your host file should be like (Change the IP of master and slave) :
all:
  children:
    postgresql:
      children:
        postgresql_master:
          hosts:
            db-01:
              ansible_host: ip_of_master
        postgresql_replicas:
          hosts:
            db-02:
              ansible_host: ip_of_standby

#Tricks and Tips
You will need to create a repmgr user on your master database with appropriate permissions. This two things.
1.	Create a database use repmgr with the permissions SUPERUSER,REPLICATION,LOGIN
2.	Add an entry to the pg_hba.conf file giving explicit access to the replication database to both the repmgr and the postgres users

#pg_hba.conf
…
#replication
local   replication   repmgr                                   trust
host    replication   repmgr      127.0.0.1/32                 trust
host   replication        repmgr       ip_of_master/24       trust
local  repmgr        repmgr                                    trust
host   repmgr        repmgr       127.0.0.1/32                 trust
host   repmgr        repmgr       ip_of_slave/24            trust
