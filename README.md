# Ansible-ELK

# Ansible-ELK

######################################################################################
######################################################################################

There are 2 modes to this Ansible playbook, Production (p) and Development (d).

Ensure that /etc/hosts file is properly populated with IP and DNS name.

## Note:
Rename the inventory file to your preferred ES cluster name. 
In this case, inventory name is cluster1, Elasticsearch's cluster name is cluster1.

###################################################################################### 
# PRODUCTION  

## generic
The group_names within the production style inventory file.
- es
- master
- hot
- warm
- coordinating
- kb
- ls

## group_vars (./inventory/group_vars/all.yml)
Edit the following file: ./inventory/group_vars/all.yml as required.
- Definitions:
   
   seed_hosts: ansible inventory 'master' group hosts are used for ES master nodes

   initial_master_nodes: #ansible inventory 'master' group hosts are used for ES master nodes

   rack_id: overwrite if necessary
   
   es_password: password for Elasticsearch cluster
   
   es_version: Elasticsearch version to install
   
   kb_version: Kibana version to install
   
   ls_version: Logstash version to install
   
   mb_version: Metricbeat version to install

   es_cert_dir: Elasticsearch certificate directory
   
   kb_cert_dir: Kibana certificate directory

   es_data_path: Elasticsearch data directory
   
   es_logs_path: Elasticsearch logs directory

   es_tmp_path: Elasticsearch tmp directory for jvm.options

   kb_data_path: Kibana data directory
  
   kb_logs_path: Kibana logs directory

   mb_logs_path: Metricbeat logs directory

   ls_logs_path: Logstash logs directory


## gen_ca_ssl_csr 

- Refer to instance_prod.yml.j2 in ./roles/gen_ca_ssl_csr/template/instance_prod.yml.j2

- instance_prod.yml.j2 will be used to generate SSL/CSR and will generate only 
   hot,warm,coordinating,master,kb SSL and CSR 

Note: Follow the instance group names specified, i.e. hot, warm, coordinating, master, kb

- Edit instance_prod.yml file in ./roles/gen_ca_ssl_csr/template/instance_prod.yml.j2 
   and fill in the necessary dns and ip. 
   
Note: Rename your own inventory file to instance_prod.yml if you have one.

- CA/SSL will be copied to /etc/elasticsearch/certs/ca or 
   /etc/elasticsearch/certs/[hot,warm,coordinating,master,kb] respectively
   
Note: Avoid changing, the CA/SSL path will not be in-synced with the elasticsearch.yml.j2 
      in ./roles/push_configs/templates/

## push_installers

- To overwrite Elasticsearch/Kibana/Metricbeat/Logstash version,
  edit /roles/push_installers/vars/main.yml

Note: Place installers in ./roles/push_installers/files/

## create_dir

- To overwrite Elasticsearch/Kibana/Metricbeat/Logstash data path or log path in group_vars,
   edit ./roles/create_dir/vars/main.yml

## push_configs 

- Change metricbeat output elasticsearch location in ./inventory/group_vars/all.yml

E.g.: mb_output_elasticsearch: "10.0.20.20", "10.0.20.30" 
      No need square brackets

- Change the list of seed_hosts, initial_master_nodes, rack_id in ./inventory/group_vars/all.yml

Note: seed_hosts and initial_master_nodes retrieve list of master nodes from inventory file

## setup_password_interactive 

- Password is retrieved and used from group_vars: es_password 

##################################################################################################

#  DEVELOPMENT  

## generic
The group_names within the production style inventory file.
- es
- master
- data
- coordinating
- kb
- ls

Note: no hot/warm

## group_vars (./inventory/group_vars/all.yml)
Edit the following file: ./inventory/group_vars/all.yml as required.
- Definitions:

   seed_hosts: ansible inventory 'master' group hosts are used for ES master nodes

   initial_master_nodes: #ansible inventory 'master' group hosts are used for ES master nodes

   rack_id: overwrite if necessary
   
   es_password: password for Elasticsearch cluster

   es_cert_dir: Elasticsearch certificate directory
   
   kb_cert_dir: Kibana certificate directory

   es_data_path: Elasticsearch data directory
   
   es_logs_path: Elasticsearch logs directory

   es_tmp_path: Elasticsearch tmp directory for jvm.options

   kb_data_path: Kibana data directory
   
   kb_logs_path: Kibana logs directory

   mb_logs_path: Metricbeat logs directory

   ls_logs_path: Logstash logs directory


## gen_ca_ssl_csr 

- Refer to instance_dev.yml.j2 in ./roles/gen_ca_ssl_csr/template/instance_dev.yml.j2

- instance_dev.yml.j2 will be used to generate SSL/CSR and will generate only 
   1 set of SSL and CSR. The name would be the same as the cluster's name

Note: Follow the instance group names specified, i.e. master, data, kb
      NO CSR generated for dev

- Edit instance_dev.yml file in ./roles/gen_ca_ssl_csr/template/instance_dev.yml.j2 
   and fill in the necessary dns and ip. 
   
Note: Rename your own inventory file to instance_dev.yml if you have one.

- CA/SSL will be copied to /etc/elasticsearch/certs/ca or 
   /etc/elasticsearch/certs/{{ cluster_name }} respectively
   
Note: Avoid changing, the CA/SSL path will not be in-synced with the elasticsearch.yml.j2 
      in ./roles/push_configs/templates/

## push_installers

- To overwrite Elasticsearch/Kibana/Metricbeat/Logstash version,
  edit ./roles/push_installers/vars/main.yml

Note: Place installers in ./roles/push_installers/files/

## create_dir

- To overwrite Elasticsearch/Kibana/Metricbeat/Logstash data path or log path in group_vars,
   edit ./roles/create_dir/vars/main.yml

## push_configs 

- Change metricbeat output elasticsearch location in ./roles/push_configs/vars/main_dev.yml

E.g.: mb_output_elasticsearch: "10.0.20.20", "10.0.20.30" 
      No need square brackets

- Change the list of seed_hosts, initial_master_nodes, rack_id in ./inventory/group_vars/all.yml

Note: seed_hosts and initial_master_nodes will retrieve list of master nodes from inventory file

## setup_password_interactive 

- Password is retrieved and used from group_vars: es_password 
