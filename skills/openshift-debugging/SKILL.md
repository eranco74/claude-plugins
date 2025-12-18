---
name: openshift-debugging
description: Comprehensive troubleshooting and debugging skill for OpenShift clusters including pods, nodes, operators, networking, and storage issues. Use when diagnosing cluster problems, investigating failed deployments, or resolving operational issues.
---

# OpenShift Debugging

This skill provides systematic approaches to troubleshooting and debugging OpenShift clusters, covering common operational issues and diagnostic techniques.

## When to Use This Skill

- Investigating pod failures or crashes
- Diagnosing deployment or rollout issues
- Troubleshooting networking problems
- Resolving storage and PVC issues
- Debugging operator failures
- Investigating node health problems
- Analyzing cluster resource constraints

## Debugging Workflow

### Step 1: Identify the Problem Scope

Determine what layer the issue is occurring at:
- Application layer (pods, deployments, statefulsets)
- Platform layer (operators, controllers, API server)
- Infrastructure layer (nodes, networking, storage)

### Step 2: Gather Initial Information

```bash
# Check cluster status
oc get clusterversion
oc get clusteroperators

# Check node status
oc get nodes
oc describe nodes

# Check critical namespace health
oc get pods -A --field-selector status.phase!=Running,status.phase!=Succeeded
```

### Step 3: Pod-Level Debugging

```bash
# Get pod details
oc get pods -n <namespace>
oc describe pod <pod-name> -n <namespace>

# Check pod logs (current and previous)
oc logs <pod-name> -n <namespace>
oc logs <pod-name> -n <namespace> --previous

# For multi-container pods
oc logs <pod-name> -n <namespace> -c <container-name>

# Check events related to the pod
oc get events -n <namespace> --field-selector involvedObject.name=<pod-name>

# Get pod resource usage
oc adm top pod <pod-name> -n <namespace>
```

### Step 4: Deployment and Rollout Issues

```bash
# Check deployment status
oc get deployment <deployment-name> -n <namespace>
oc describe deployment <deployment-name> -n <namespace>
oc rollout status deployment/<deployment-name> -n <namespace>

# Check replica sets
oc get rs -n <namespace>
oc describe rs <replicaset-name> -n <namespace>

# View rollout history
oc rollout history deployment/<deployment-name> -n <namespace>

# Pause/resume rollout
oc rollout pause deployment/<deployment-name> -n <namespace>
oc rollout resume deployment/<deployment-name> -n <namespace>
```

### Step 5: Networking Debugging

```bash
# Check services and endpoints
oc get svc -n <namespace>
oc get endpoints -n <namespace>
oc describe svc <service-name> -n <namespace>

# Check routes
oc get routes -n <namespace>
oc describe route <route-name> -n <namespace>

# Check network policies
oc get networkpolicy -n <namespace>
oc describe networkpolicy <policy-name> -n <namespace>

# Test connectivity from a pod
oc exec -it <pod-name> -n <namespace> -- curl <service-url>
oc exec -it <pod-name> -n <namespace> -- nslookup <service-name>

# Check DNS resolution
oc exec -it <pod-name> -n <namespace> -- cat /etc/resolv.conf
```

### Step 6: Storage Debugging

```bash
# Check PVCs and PVs
oc get pvc -n <namespace>
oc get pv
oc describe pvc <pvc-name> -n <namespace>
oc describe pv <pv-name>

# Check storage classes
oc get storageclass
oc describe storageclass <storage-class-name>

# Check volume attachments
oc get volumeattachment
```

### Step 7: Operator Debugging

```bash
# List all operators
oc get clusteroperators
oc get csv -A

# Check operator status
oc describe clusteroperator <operator-name>
oc get csv -n <operator-namespace>

# Check operator logs
oc logs -n <operator-namespace> deployment/<operator-deployment>

# Check operator subscriptions
oc get subscription -n <namespace>
oc describe subscription <subscription-name> -n <namespace>

# Check install plans
oc get installplan -n <namespace>
```

### Step 8: Node Debugging

```bash
# Get node details
oc get nodes -o wide
oc describe node <node-name>

# Check node conditions
oc get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.status.conditions[?(@.type=="Ready")].status}{"\n"}{end}'

# Check node capacity and allocatable resources
oc describe node <node-name> | grep -A 5 "Capacity:"

# Access node for debugging (if needed)
oc debug node/<node-name>

# Check machine and machine sets (for automated infrastructure)
oc get machines -n openshift-machine-api
oc get machinesets -n openshift-machine-api
```

### Step 9: Resource Constraints

```bash
# Check resource quotas
oc get resourcequota -n <namespace>
oc describe resourcequota <quota-name> -n <namespace>

# Check limit ranges
oc get limitrange -n <namespace>
oc describe limitrange <limit-range-name> -n <namespace>

# Check resource usage
oc adm top nodes
oc adm top pods -n <namespace>
```

### Step 10: Advanced Diagnostics

```bash
# Collect must-gather for support cases
oc adm must-gather

# Check API server logs
oc logs -n openshift-apiserver <apiserver-pod>

# Check audit logs
oc adm node-logs <node-name> --path=kube-apiserver/audit.log

# Check cluster alerts
oc get prometheus -n openshift-monitoring
oc port-forward -n openshift-monitoring prometheus-k8s-0 9090:9090
```

## Common Issues and Solutions

### Image Pull Errors

```bash
# Check image pull secrets
oc get secrets -n <namespace>
oc describe secret <pull-secret-name> -n <namespace>

# Link secret to service account
oc secrets link default <pull-secret-name> --for=pull -n <namespace>

# Check image registry access
oc get imagestreams -n <namespace>
oc describe imagestream <imagestream-name> -n <namespace>
```

### CrashLoopBackOff

1. Check pod logs for application errors
2. Verify liveness/readiness probes configuration
3. Check resource limits and requests
4. Verify environment variables and config maps
5. Check dependencies (databases, services)

### Pending Pods

1. Check if nodes have sufficient resources
2. Verify node selectors and affinity rules
3. Check taints and tolerations
4. Verify PVC binding for pods with volumes
5. Check pod security policies

### Networking Issues

1. Verify service selector matches pod labels
2. Check network policies aren't blocking traffic
3. Verify routes and ingress configuration
4. Check DNS resolution
5. Verify SDN/CNI plugin health

## Best Practices

1. **Start Broad, Then Narrow**: Begin with cluster-level checks, then drill down to specific components
2. **Check Events**: Events often provide the first clue about what's wrong
3. **Compare with Working State**: Compare problematic resources with known working configurations
4. **Use Labels**: Leverage labels to group and filter related resources
5. **Enable Debug Logging**: Temporarily increase log levels when needed
6. **Document Findings**: Keep notes of what you've checked and what you found
7. **Check Recent Changes**: Review recent deployments, configuration changes, or upgrades

## Useful Commands Reference

```bash
# Quick health check
oc get clusterversion && oc get clusteroperators && oc get nodes

# Find pods not running
oc get pods -A --field-selector status.phase!=Running,status.phase!=Succeeded

# Get all events sorted by time
oc get events -A --sort-by='.lastTimestamp'

# Port forward for local debugging
oc port-forward <pod-name> <local-port>:<remote-port> -n <namespace>

# Execute commands in a pod
oc exec -it <pod-name> -n <namespace> -- /bin/bash

# Copy files to/from pods
oc cp <pod-name>:/path/to/file ./local-file -n <namespace>
oc cp ./local-file <pod-name>:/path/to/file -n <namespace>

# Watch resources in real-time
oc get pods -n <namespace> --watch

# Get YAML/JSON output for analysis
oc get <resource> <name> -n <namespace> -o yaml
oc get <resource> <name> -n <namespace> -o json | jq '.'
```

## Next Steps

After debugging:
1. Document the root cause and resolution
2. Consider if automation can prevent recurrence
3. Update monitoring/alerting if needed
4. Share findings with the team
5. Update runbooks or documentation

## Related Skills

- `openshift-cluster-upgrade` - For upgrade-related issues
- `openshift-operator-troubleshooting` - Deep dive into operator issues
- `openshift-node-operations` - Node-specific operations and maintenance
