This repository contains audit logs captured from an **OpenShift 4.17.11** cluster, with logging infrastructure provided by **Red Hat OpenShift Logging 6.11**. The logs were ingested and processed using **Graylog**, leveraging pipelines to extract and structure JSON data. These logs serve as examples for auditing and troubleshooting cluster activity.

---

### Note on Logs

The logs included in this repository may differ from your environment based on the following factors:
- The **authentication providers** used in your cluster (e.g., OAuth, LDAP, or custom identity providers).
- **Log ingestion methods** and transformations applied (e.g., additional parsing or enrichment steps in Graylog pipelines).

This variability emphasizes the importance of tailoring your log processing and analysis pipelines to your specific cluster setup.


---

### Key Details

1. **OpenShift Version**:
   - Logs were collected from an OpenShift 4.17.11 environment.

2. **Logging Stack**:
   - Logging infrastructure was provided by **Red Hat OpenShift Logging 6.11**.

3. **Log Processing**:
   - Logs were ingested into Graylog and processed using a pipeline to extract and format JSON data.

4. **Variations in Logs**:
   - The structure and content of your logs may vary depending on:
     - Authentication providers configured in your OpenShift cluster.
     - Log ingestion methods and transformations applied during processing.

---

### Example ClusterLogForwarder Configuration

Below is an example **ClusterLogForwarder** configuration used to forward logs to Graylog in this setup:

```yaml
apiVersion: observability.openshift.io/v1
kind: ClusterLogForwarder
metadata:
  name: logging
  namespace: openshift-logging
spec:
  managementState: Managed
  outputs:
  - name: graylog
    syslog:
      appName: ocp
      rfc: RFC5424
      url: udp://172.99.0.105:5140
    type: syslog
  pipelines:
  - inputRefs:
    - application
    - infrastructure
    - audit
    name: syslog-pipeline
    outputRefs:
    - graylog
  serviceAccount:
    name: cluster-logging-operator
```

### Configuration Highlights

- **Inputs**:
  - Logs from `application`, `infrastructure`, and `audit` sources are forwarded.
  
- **Output**:
  - Logs are sent to Graylog using **Syslog** with RFC5424 formatting via `udp://172.99.0.105:5140`.

- **Pipeline**:
  - The `syslog-pipeline` is configured to route logs to the specified `graylog` output.

- **ServiceAccount**:
  - The `cluster-logging-operator` ServiceAccount is used to manage the log forwarding configuration.

---

For further details or guidance on configuring OpenShift logging or Graylog pipelines, please consult the official Red Hat documentation or your system administrator.
