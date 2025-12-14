```bash
dodinaev@jienshakh:~/netology_ansible/playbook$ ansible-playbook -i inventory/prod.yml site.yml --diff

PLAY [Install Clickhouse] ********************************************************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************************************************************
[WARNING]: Platform linux on host clickhouse-01 is using the discovered Python interpreter at /usr/bin/python3.9, but future installation of another Python interpreter could change the meaning of that
path. See https://docs.ansible.com/ansible-core/2.18/reference_appendices/interpreter_discovery.html for more information.
ok: [clickhouse-01]

TASK [Get clickhouse distrib] ****************************************************************************************************************************************************************************
changed: [clickhouse-01] => (item=clickhouse-client)
changed: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "item": "clickhouse-common-static", "msg": "Request failed", "response": "HTTP Error 404: Not Found", "status_code": 404, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] ****************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Install clickhouse packages] ***********************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Flush handlers] ************************************************************************************************************************************************************************************

RUNNING HANDLER [Start clickhouse service] ***************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Wait for ClickHouse TCP port] **********************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Create database] ***********************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Enable ClickHouse external access] *****************************************************************************************************************************************************************
--- before: /etc/clickhouse-server/config.xml (content)
+++ after: /etc/clickhouse-server/config.xml (content)
@@ -93,6 +93,10 @@
          and by most of web interfaces (embedded UI, Grafana, Redash, ...).
       -->
     <http_port>8123</http_port>
+# BEGIN ANSIBLE MANAGED BLOCK
+<listen_host>::1</listen_host>
+<listen_host>0.0.0.0</listen_host>
+# END ANSIBLE MANAGED BLOCK

     <!-- Port for interaction by native protocol with:
          - clickhouse-client and other native ClickHouse tools (clickhouse-benchmark, clickhouse-copier);

changed: [clickhouse-01]

RUNNING HANDLER [Start clickhouse service] ***************************************************************************************************************************************************************
changed: [clickhouse-01]

PLAY [Install Vector] ************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************************************************************
[WARNING]: Platform linux on host vector-01 is using the discovered Python interpreter at /usr/bin/python3.9, but future installation of another Python interpreter could change the meaning of that
path. See https://docs.ansible.com/ansible-core/2.18/reference_appendices/interpreter_discovery.html for more information.
ok: [vector-01]

TASK [Get vector distrib] ********************************************************************************************************************************************************************************
changed: [vector-01]

TASK [Install vector] ************************************************************************************************************************************************************************************
changed: [vector-01]

TASK [Flush handlers] ************************************************************************************************************************************************************************************

RUNNING HANDLER [Start vector service] *******************************************************************************************************************************************************************
changed: [vector-01]

PLAY [Configure Vector] **********************************************************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************************************************************
ok: [vector-01]

TASK [Create Vector config directory] ********************************************************************************************************************************************************************
ok: [vector-01]

TASK [Deploy Vector configuration] ***********************************************************************************************************************************************************************
--- before: /etc/vector/vector.yaml
+++ after: /home/dodinaev/.ansible/tmp/ansible-local-108246nycvikv/tmppyqzs4c2/vector.yaml.j2
@@ -1,49 +1,38 @@
-#                                    __   __  __
-#                                    \ \ / / / /
-#                                     \ V / / /
-#                                      \_/  \/
-#
-#                                    V E C T O R
-#                                   Configuration
-#
-# ------------------------------------------------------------------------------
-# Website: https://vector.dev
-# Docs: https://vector.dev/docs
-# Chat: https://chat.vector.dev
-# ------------------------------------------------------------------------------
+# Ansible managed

-# Change this to use a non-default directory for Vector data storage:
-# data_dir: "/var/lib/vector"
+data_dir: "/var/lib/vector"

-# Random Syslog-formatted logs
+api:
+  enabled: true
+  address: "0.0.0.0:8686"
+  playground: false
+
 sources:
-  dummy_logs:
-    type: "demo_logs"
-    format: "syslog"
+  generated_logs:
+    type: demo_logs
+    format: json
     interval: 1
+
+  internal_metrics:
+    type: internal_metrics
+    scrape_interval_secs: 15

-# Parse Syslog logs
-# See the Vector Remap Language reference for more info: https://vrl.dev
-transforms:
-  parse_logs:
-    type: "remap"
-    inputs: ["dummy_logs"]
-    source: |
-      . = parse_syslog!(string!(.message))
-
-# Print parsed logs to stdout
 sinks:
-  print:
-    type: "console"
-    inputs: ["parse_logs"]
-    encoding:
-      codec: "json"
-      json:
-        pretty: true
-
-# Vector's GraphQL API (disabled by default)
-# Uncomment to try it out with the `vector top` command or
-# in your browser at http://localhost:8686
-# api:
-#   enabled: true
-#   address: "127.0.0.1:8686"
+  to_clickhouse:
+    type: clickhouse
+    inputs:
+      - generated_logs
+    endpoint: "http://130.193.35.42:8123"
+    database: "logs"
+    table: "logs"
+    compression: gzip
+    skip_unknown_fields: true
+    batch:
+      max_events: 10000
+      timeout_secs: 5
+
+  prometheus_exporter:
+    type: prometheus_exporter
+    inputs:
+      - internal_metrics
+    address: "0.0.0.0:9598"
\ No newline at end of file

changed: [vector-01]

TASK [Ensure Vector is running] **************************************************************************************************************************************************************************
changed: [vector-01]

RUNNING HANDLER [Restart vector] *************************************************************************************************************************************************************************
changed: [vector-01]

PLAY RECAP ***********************************************************************************************************************************************************************************************
clickhouse-01              : ok=8    changed=6    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0
vector-01                  : ok=9    changed=6    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
