# Ansible role: Filebeat
[![CI Molecule](https://github.com/darexsu/ansible-role-filebeat/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-filebeat/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/58490?color=blue&label=downloads)

  - Role:
      - [platforms](#platforms)
      - [install](#install)
      - [merge behaviour](#merge-behaviour)
  - Playbooks (merge version):
      - [install and configure: Filebeat](#install-and-configure-filebeat-merge-version)
          - [install: Filebeat](#install-filebeat-merge-version)
          - [configure: Filebeat](#configure-filebeat-merge-version)
  - Playbooks (full version):
      - [install and configure: Filebeat](#install-and-configure-filebeat-full-version)
          - [install: Filebeat](#install-filebeat-full-version)
          - [configure: Filebeat](#configure-filebeat-full-version)

### Platforms

|  Testing         | repo: elastic      |
| :--------------: | :----------------: |
| Debian 11        |  elastic.co        |
| Debian 10        |  elastic.co        |
| Ubuntu 20.04     |  elastic.co        |
| Ubuntu 18.04     |  elastic.co        |
| Oracle Linux 8   |  elastic.co        |
| Rocky Linux 8    |  elastic.co        |

### Install

```
ansible-galaxy install darexsu.filebeat --force
```

### Merge behaviour

Replace or Merge dictionaries (with "hash_behaviour=replace" in ansible.cfg):
```
# Replace             # Merge
---                   ---
  vars:                 vars:
    dict:                 merge:
      a: "value"            dict: 
      b: "value"              a: "value" 
                              b: "value"

# How does merge work?:
Your vars [host_vars]  -->  default vars [current role] --> default vars [include role]
  
  dict:          dict:              dict:
    a: "1" -->     a: "1"    -->      a: "1"
                   b: "2"    -->      b: "2"
                                      c: "3"
    
```

##### Install and configure: Filebeat (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Filebeat
      filebeat:
        enabled: true
      # Filebeat -> install
      filebeat_install:
        enabled: true
      # Filebeat -> config -> filebeat.yml
      filebeat_yml:
        enabled: true
        data: |
          # ============================== Filebeat inputs ===============================
          filebeat.inputs:
            - type: filestream
              enabled: true
              paths:
                - /var/log/*.log
          # ============================== Filebeat modules ==============================
          filebeat.config.modules:
            path: ${path.config}/modules.d/*.yml
            reload.enabled: false
          # ======================= Elasticsearch template setting =======================
          setup.template.settings:
            index.number_of_shards: 1
          # ================================== General ===================================
          setup.kibana:
          # ---------------------------- Elasticsearch Output ----------------------------
          # output.elasticsearch:
          #   hosts: ["localhost:9200"]
          # ------------------------------ Logstash Output -------------------------------
          output.logstash:
            hosts: ["localhost:5044"]
          # ================================= Processors =================================
          processors:
            - add_host_metadata:
                when.not.contains.tags: forwarded
            - add_cloud_metadata: ~
            - add_docker_metadata: ~
            - add_kubernetes_metadata: ~
          # ================================== Logging ===================================
          # logging.level: debug
          # logging.selectors: ["*"]
          # ============================= X-Pack Monitoring ==============================
          # monitoring.enabled: false
          # monitoring.cluster_uuid:
          # monitoring.elasticsearch:
          # ============================== Instrumentation ===============================
          # instrumentation:
          # enabled: false
          # environment: ""
          # hosts:
          #   - http://localhost:8200
          # api_key:
          # secret_token:
          # ================================= Migration ==================================
          # migration.6_to_7.enabled: true

  tasks:
    - name: role darexsu filebeat
      include_role:
        name: darexsu.filebeat

```
##### Install: Filebeat (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Filebeat
      filebeat:
        enabled: true
      # Filebeat -> install
      filebeat_install:
        enabled: true

  tasks:
    - name: role darexsu filebeat
      include_role:
        name: darexsu.filebeat

```
##### Configure: Filebeat (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Filebeat
      filebeat:
        enabled: true
      # Filebeat -> config -> filebeat.yml
      filebeat_yml:
        enabled: true
        data: |
          # ============================== Filebeat inputs ===============================
          filebeat.inputs:
            - type: filestream
              enabled: true
              paths:
                - /var/log/*.log
          # ============================== Filebeat modules ==============================
          filebeat.config.modules:
            path: ${path.config}/modules.d/*.yml
            reload.enabled: false
          # ======================= Elasticsearch template setting =======================
          setup.template.settings:
            index.number_of_shards: 1
          # ================================== General ===================================
          setup.kibana:
          # ---------------------------- Elasticsearch Output ----------------------------
          # output.elasticsearch:
          #   hosts: ["localhost:9200"]
          # ------------------------------ Logstash Output -------------------------------
          output.logstash:
            hosts: ["localhost:5044"]
          # ================================= Processors =================================
          processors:
            - add_host_metadata:
                when.not.contains.tags: forwarded
            - add_cloud_metadata: ~
            - add_docker_metadata: ~
            - add_kubernetes_metadata: ~
          # ================================== Logging ===================================
          # logging.level: debug
          # logging.selectors: ["*"]
          # ============================= X-Pack Monitoring ==============================
          # monitoring.enabled: false
          # monitoring.cluster_uuid:
          # monitoring.elasticsearch:
          # ============================== Instrumentation ===============================
          # instrumentation:
          # enabled: false
          # environment: ""
          # hosts:
          #   - http://localhost:8200
          # api_key:
          # secret_token:
          # ================================= Migration ==================================
          # migration.6_to_7.enabled: true

  tasks:
    - name: role darexsu filebeat
      include_role:
        name: darexsu.filebeat


```
##### Install and configure: Filebeat (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    # Filebeat
    filebeat:
      enabled: true
      version: "8.x"
      repo: "elastic"
      service:
        enabled: true
        state: "started"
    # Filebeat -> install
    filebeat_install:
      enabled: true
      
    # Filebeat -> config -> filebeat.yml
    filebeat_yml:
      enabled: true
      file: "filebeat.yml"
      src: "filebeat_yml.j2"
      backup: false
      data: |
        # ============================== Filebeat inputs ===============================
        filebeat.inputs:
          - type: filestream
            enabled: true
            paths:
              - /var/log/*.log
        # ============================== Filebeat modules ==============================
        filebeat.config.modules:
          path: ${path.config}/modules.d/*.yml
          reload.enabled: false
        # ======================= Elasticsearch template setting =======================
        setup.template.settings:
          index.number_of_shards: 1
        # ================================== General ===================================
        setup.kibana:
        # ---------------------------- Elasticsearch Output ----------------------------
        # output.elasticsearch:
        #   hosts: ["localhost:9200"]
        # ------------------------------ Logstash Output -------------------------------
        output.logstash:
          hosts: ["localhost:5044"]
        # ================================= Processors =================================
        processors:
          - add_host_metadata:
              when.not.contains.tags: forwarded
          - add_cloud_metadata: ~
          - add_docker_metadata: ~
          - add_kubernetes_metadata: ~
        # ================================== Logging ===================================
        # logging.level: debug
        # logging.selectors: ["*"]
        # ============================= X-Pack Monitoring ==============================
        # monitoring.enabled: false
        # monitoring.cluster_uuid:
        # monitoring.elasticsearch:
        # ============================== Instrumentation ===============================
        # instrumentation:
        # enabled: false
        # environment: ""
        # hosts:
        #   - http://localhost:8200
        # api_key:
        # secret_token:
        # ================================= Migration ==================================
        # migration.6_to_7.enabled: true

  tasks:
    - name: role darexsu filebeat
      include_role:
        name: darexsu.filebeat

```
##### Install: Filebeat (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    # Filebeat
    filebeat:
      enabled: true
      version: "8.x"
      repo: "elastic"
      service:
        enabled: true
        state: "started"
    # Filebeat -> install
    filebeat_install:
      enabled: true

  tasks:
    - name: role darexsu filebeat
      include_role:
        name: darexsu.filebeat

```
##### Configure: Filebeat (full version)
```yaml
---
- hosts: all
  become: true

  vars:    
    # Filebeat
    filebeat:
      enabled: true
      version: "8.x"
      repo: "elastic"
      service:
        enabled: true
        state: "started"
    # Filebeat -> config -> filebeat.yml
    filebeat_yml:
      enabled: true
      file: "filebeat.yml"
      src: "filebeat_yml.j2"
      backup: false
      data: |
        # ============================== Filebeat inputs ===============================
        filebeat.inputs:
          - type: filestream
            enabled: true
            paths:
              - /var/log/*.log
        # ============================== Filebeat modules ==============================
        filebeat.config.modules:
          path: ${path.config}/modules.d/*.yml
          reload.enabled: false
        # ======================= Elasticsearch template setting =======================
        setup.template.settings:
          index.number_of_shards: 1
        # ================================== General ===================================
        setup.kibana:
        # ---------------------------- Elasticsearch Output ----------------------------
        # output.elasticsearch:
        #   hosts: ["localhost:9200"]
        # ------------------------------ Logstash Output -------------------------------
        output.logstash:
          hosts: ["localhost:5044"]
        # ================================= Processors =================================
        processors:
          - add_host_metadata:
              when.not.contains.tags: forwarded
          - add_cloud_metadata: ~
          - add_docker_metadata: ~
          - add_kubernetes_metadata: ~
        # ================================== Logging ===================================
        # logging.level: debug
        # logging.selectors: ["*"]
        # ============================= X-Pack Monitoring ==============================
        # monitoring.enabled: false
        # monitoring.cluster_uuid:
        # monitoring.elasticsearch:
        # ============================== Instrumentation ===============================
        # instrumentation:
        # enabled: false
        # environment: ""
        # hosts:
        #   - http://localhost:8200
        # api_key:
        # secret_token:
        # ================================= Migration ==================================
        # migration.6_to_7.enabled: true

  tasks:
    - name: role darexsu filebeat
      include_role:
        name: darexsu.filebeat
```
