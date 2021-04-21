# Provisioning Flow with Orchestrator


- Service Management Orchestration (SMO)
- Red Hat Advanced Cluster Management for Kubernetes (RHACM)
- OpenShift Assisted Service (OAS) Operator or Assisted Installer (AI)


```mermaid
sequenceDiagram
    participant SMO
    participant RHACM
    participant OAS
    participant GIT
    participant NODE
    SMO->>GIT: Commit cluster directory structre
    GIT->>RHACM: Webhook notification
    loop Cluster CR
        RHACM->>RHACM: Create Cluster CR definition
	RHACM-->>OAS: OAS receives event notification on creation of CR
    end
    Note right of OAS: OAS generate cluster artifacts and ISOs
    loop Deploy OCP on each Node of the cluster
        OAS->>NODE: Boot node with LiveISO
	NODE-->>OAS: Report HW inventory
        OAS->>NODE: Assign role to node
        Note right of OAS: After all nodes report back, OAS trigger the installation
        NODE-->>OAS: Report progress and status
        OAS-->>RHACM: OAS report progress and status over API
        RHACM-->>SMO: RHACM report progress and status over API
        SMO-->>RHACM: (optional) SMO can query RHACM API for progress and status
    end
    Note right of RHACM: After completion of deployment RHACM can execute post-hooks
    Note right of SMO: SMO can retrieve progress and status over RHACM and OAS APIs
```
