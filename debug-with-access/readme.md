---
### **Critical Fields to Alert On**
1. **`annotations.authorization.k8s.io/decision`**:
   - Value: `allow`
   - This indicates the access was granted. Alert when sensitive operations like `attach` or `exec` are successfully authorized.

2. **`verb`**:
   - Value: `create`
   - Indicates that a new resource or operation (e.g., a debug session) was initiated. This is critical for monitoring privilege escalation attempts.

3. **`objectRef.resource`** and **`objectRef.subresource`**:
   - Values: `pods` and `attach`
   - Tracks which resource and subresource were accessed. Alert when subresources like `attach` or `exec` are involved, especially in the context of debug or direct access to containers.

4. **`objectRef.namespace`**:
   - Value: `openshift-debug-h8jfq`
   - Indicates the namespace where the action occurred. Alert on debug or privileged namespaces like `openshift-debug-*`.

5. **`requestURI`**:
   - Value: `/api/v1/namespaces/openshift-debug-h8jfq/pods/ba-c2-aa-ef-1b-82-debug-psfrp/attach?...`
   - The exact API request path. Alert on operations targeting `pods/<name>/attach` or similar paths that enable interaction with containers.

6. **`responseStatus.code`**:
   - Value: `101`
   - An HTTP WebSocket Switching Protocol code indicates an active session. This should trigger an alert for debug or exec sessions.

7. **`user.username`**:
   - Value: `system:admin`
   - The user who initiated the session. Alert on administrative or highly privileged accounts performing debug actions.

8. **`user.groups`**:
   - Values: `system:masters`, `system:authenticated`
   - These groups indicate high privilege. Alert when debug or exec access is performed by users in elevated groups.

9. **`userAgent`**:
   - Value: `oc/4.14.0 (darwin/arm64)`
   - Monitors the client initiating the request. Alert if non-standard or suspicious clients are used.

10. **`sourceIPs`**:
    - Value: `172.99.0.1`
    - The IP address from which the request originated. Alert on non-whitelisted or suspicious IPs.

---

### **Additional Contextual Fields**
1. **`stage`**:
   - Value: `ResponseStarted`
   - Indicates the session has started but not yet completed. Track for prolonged or repeated stages.

2. **`auditID`**:
   - Unique identifier for this specific audit event. Use it to correlate logs if needed.

3. **`@timestamp`** and **`stageTimestamp`**:
   - Useful for analyzing timing and identifying potential anomalous activity patterns.

4. **`objectRef.name`**:
   - Value: `ba-c2-aa-ef-1b-82-debug-psfrp`
   - Indicates the specific debug pod created. Monitor debug pod creation, especially in critical namespaces or for sensitive nodes.

---

### **Alerting Recommendations**
1. **Debug/Exec Mode Alerts**:
   - Trigger an alert whenever `objectRef.subresource = attach` or `exec` and `annotations.authorization.k8s.io/decision = allow`.

2. **Privileged User Access**:
   - Alert when users like `system:admin` or groups like `system:masters` perform debug operations, especially if they deviate from typical activity patterns.

3. **Namespace Monitoring**:
   - Monitor debug or admin namespaces (`openshift-debug-*`) for any suspicious activity.

4. **Source IP Monitoring**:
   - Alert on unauthorized or unusual IP addresses in `sourceIPs`.

5. **Session Start Detection**:
   - Detect and alert on debug or exec sessions when `responseStatus.code = 101`.

---
