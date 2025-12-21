```bash
dodinaev@jienshakh:~/netology_ansible/playbook$ ansible-playbook -i inventory/prod.yml site.yml --diff

PLAY [Install Nginx] *************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************************************************************
[WARNING]: Platform linux on host lighthouse-01 is using the discovered Python interpreter at /usr/bin/python3.9, but future installation of another Python interpreter could change the meaning of that
path. See https://docs.ansible.com/ansible-core/2.18/reference_appendices/interpreter_discovery.html for more information.
ok: [lighthouse-01]

TASK [NGINX | install epel-release] **********************************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [NGINX | install NGINX] *****************************************************************************************************************************************************************************
changed: [lighthouse-01]

TASK [NGINX | create general config] *********************************************************************************************************************************************************************
--- before: /etc/nginx/nginx.conf
+++ after: /home/dodinaev/.ansible/tmp/ansible-local-111365dqrs_p72/tmp767b4biw/nginx.conf.j2
@@ -2,7 +2,8 @@
 #   * Official English Documentation: http://nginx.org/en/docs/
 #   * Official Russian Documentation: http://nginx.org/ru/docs/

-user nginx;
+user root;
+#user nginx;
 worker_processes auto;
 error_log /var/log/nginx/error.log;
 pid /run/nginx.pid;
@@ -35,23 +36,6 @@
     # for more information.
     include /etc/nginx/conf.d/*.conf;

-    server {
-        listen       80;
-        listen       [::]:80;
-        server_name  _;
-        root         /usr/share/nginx/html;
-
-        # Load configuration files for the default server block.
-        include /etc/nginx/default.d/*.conf;
-
-        error_page 404 /404.html;
-        location = /404.html {
-        }
-
-        error_page 500 502 503 504 /50x.html;
-        location = /50x.html {
-        }
-    }

 # Settings for a TLS enabled server.
 #

changed: [lighthouse-01]

RUNNING HANDLER [Start-nginx] ****************************************************************************************************************************************************************************
changed: [lighthouse-01]

RUNNING HANDLER [Reload-nginx] ***************************************************************************************************************************************************************************
changed: [lighthouse-01]

PLAY [Install ligthouse] *********************************************************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Lighthouse | Install dependencies] *****************************************************************************************************************************************************************
changed: [lighthouse-01]

TASK [Lighthouse | Copy from git] ************************************************************************************************************************************************************************
>> Newly checked out d701335c25cd1bb9b5155711190bad8ab852c2ce
changed: [lighthouse-01]

TASK [Lighthouse | Create lighthouse config] *************************************************************************************************************************************************************
--- before
+++ after: /home/dodinaev/.ansible/tmp/ansible-local-111365dqrs_p72/tmp5kixf6we/lighthouse.conf.j2
@@ -0,0 +1,13 @@
+server {
+    listen 8888;
+    # server_name localhost;
+    server_name 0.0.0.0;
+
+    access_log /var/log/nginx/lighthouse-access.log;
+
+    location / {
+        root /opt/lighthouse/;
+        index index.html;
+
+    }
+}

changed: [lighthouse-01]

RUNNING HANDLER [Reload-nginx] ***************************************************************************************************************************************************************************
ok: [lighthouse-01]

PLAY [Install Clickhouse] ********************************************************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************************************************************
[WARNING]: Platform linux on host clickhouse-01 is using the discovered Python interpreter at /usr/bin/python3.9, but future installation of another Python interpreter could change the meaning of that
path. See https://docs.ansible.com/ansible-core/2.18/reference_appendices/interpreter_discovery.html for more information.
ok: [clickhouse-01]

TASK [Get clickhouse distrib] ****************************************************************************************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 1000, "group": "dodinaev", "item": "clickhouse-common-static", "mode": "0644", "msg": "Request failed", "owner": "dodinaev", "response": "HTTP Error 404: Not Found", "secontext": "unconfined_u:object_r:user_home_t:s0", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] ****************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Install clickhouse packages] ***********************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Flush handlers] ************************************************************************************************************************************************************************************

RUNNING HANDLER [Start (restart) clickhouse service] *****************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Wait for ClickHouse TCP port] **********************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Create database] ***********************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Create table] **************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Enable ClickHouse external access] *****************************************************************************************************************************************************************
changed: [clickhouse-01]

RUNNING HANDLER [Start (restart) clickhouse service] *****************************************************************************************************************************************************
changed: [clickhouse-01]

PLAY [Install Vector] ************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************************************************************
[WARNING]: Platform linux on host vector-01 is using the discovered Python interpreter at /usr/bin/python3.9, but future installation of another Python interpreter could change the meaning of that
path. See https://docs.ansible.com/ansible-core/2.18/reference_appendices/interpreter_discovery.html for more information.
ok: [vector-01]

TASK [Get vector distrib] ********************************************************************************************************************************************************************************
ok: [vector-01]

TASK [Install vector] ************************************************************************************************************************************************************************************
changed: [vector-01]

TASK [Flush handlers] ************************************************************************************************************************************************************************************

RUNNING HANDLER [Start (restart) vector service] *********************************************************************************************************************************************************
changed: [vector-01]

TASK [Create Vector config directory] ********************************************************************************************************************************************************************
ok: [vector-01]

TASK [Deploy Vector configuration] ***********************************************************************************************************************************************************************
changed: [vector-01]

TASK [Ensure Vector is running] **************************************************************************************************************************************************************************
ok: [vector-01]

RUNNING HANDLER [Start (restart) vector service] *********************************************************************************************************************************************************
changed: [vector-01]

PLAY RECAP ***********************************************************************************************************************************************************************************************
clickhouse-01              : ok=8    changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vector-01                  : ok=8    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
