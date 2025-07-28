# Kubectl logs

kubectl logs cluster-manager-5d96b875f4-pmjv5 | less

# kubectl get pods

kubectl get pods
\- Show all the pods currently active.

\[root@tufin ~\]# kubectl get pods
NAME READY STATUS RESTARTS AGE
alert-service-7c655f7c55-wc7br 1/1 Running 0 4d22h
<snip>

kubectl get pods | grep -v 0
\- Show pods with anything invert of zero (anything not zero)

kubectl get pods | grep cluster

# kubecdtl exec

kubectl exec --help
Execute a command in a container.

Examples:
\# Get output from running the 'date' command from pod mypod, using the first container by default
kubectl exec mypod -- date

Options:
\-c, --container='':
Container name. If omitted, use the [kubectl.kubernetes.io/default-container](http://kubectl.kubernetes.io/default-container) annotation for selecting the
container to be attached or the first container in the pod will be chosen

\-f, --filename=\[\]:
to use to exec into the resource

\--pod-running-timeout=1m0s:
The length of time (like 5s, 2m, or 3h, higher than zero) to wait until at least one pod is running

\-q, --quiet=false:
Only print output from the remote session

\-i, --stdin=false:
Pass stdin to the container

\-t, --tty=false:
Stdin is a TTY

kubectl exec -it deploy/device-collector -c device-collector -- /usr/local/st/st\_get\_dynamic\_topology\_data 6661

# Describe pod

kubectl describe pod cluster-manager-5d96b875f4-pmjv5 | less

# Get nodes

kubectl get nodes

# Review Loki

kubectl loki | less

# tos config get

tos config get - Get a summary of current setup.

\[root@tufin ~\]# tos config get
SERVICE PROPERTY VALUE DEFAULT MESSAGE

Global load.model large medium
Global scale.factor 4
Global server.timezone America/Chicago UTC
Global tos.sso.enabled true
lea-coordinator-service lea.maxProcessorsPerPod 2 10
lea-coordinator-service leaProcessorMemLimit 1Gi 512Mi
lea-coordinator-service leaProcessorMemRequest 512Mi 50Mi
securetrack-job st.trace.Mem false
st-scheduler memory.limit 18Gi
st-scheduler memory.request 10Gi
syslog-agent-service DEPLOY\_TS 1712332601
syslog-agent-service SYSLOG\_FILES\_MAX\_DIRECTORY\_SIZE 100GB ComputedEnvVar: 10GB
syslog-agent-service SYSLOG\_FILES\_MAX\_SIZE 200Mb 100Mb
syslog-agent-service SYSLOG\_LOAD\_FACTOR 8 3
syslog-agent-service affinity.preferWorkerNode.weight 100 1
syslog-agent-service memory.limit 3Gi
syslog-agent-service memory.request 1Gi

# Restart Extension PODS

kubectl get pods -oname | grep "$(tos apps list | awk '{print $1}' | egrep -v "NAME")" | xargs kubectl delete

# Restart pods

kubectl get pods | grep POD\_NAME | awk '{print $1}' | xargs kubectl delete pod

kubectl get pods | grep device-collector | awk '{print $1}' | xargs kubectl delete pod

kubectl get pods | grep tos-prometheus-node | awk '{print $1}' | xargs kubectl delete pod

kubectl get pods | grep sc-server | awk '{print $1}' | xargs kubectl delete pod

kubectl get pods | grep report | awk '{print $1}' | xargs kubectl delete pod

kubectl get pods | grep syslog-cleaner-xrdc | awk '{print $1}' | xargs kubectl delete pod

kubectl get pods | grep cloud-topology-service | awk '{print $1}' | xargs kubectl delete pod
kubectl get pods | grep loki-5c5ffc6579-d6d8c | awk '{print $1}' | xargs kubectl delete pod

kubectl get pods -oname | grep "$(tos apps list | awk '{print $1}' | egrep -v "NAME")" | xargs kubectl delete

loki-5c5ffc6579-d6d8c

kubectl get pods | grep sc-server | awk '{print $1}' | xargs kubectl delete pod; kubectl get pods | grep report | awk '{print $1}' | xargs kubectl delete pod;
kubectl get pods -oname | grep "$(tos apps list | awk '{print $1}' | egrep -v "NAME")" | xargs kubectl delete

# Restart single POD

kubectl get pods
\---- Find name of pod to restart

kubectl delete pod NAME\_OF\_POD

# Rebooting Process

1\. Run the command: tos stop
2\. Check to pods to ensure that they all finish terminating. Easy way to do this is to run command: watch kubectl get pods
a. Any extension pod (report-pack, ispa, dashboard essentials, etc…) will still show as “running”. This is expected and you can still safely reboot.
b. Service-controller pod will also stay running. You may also see some pods that are lingering with “completed” as status, that is OK too.
3\. You can now safely reboot the data node and/or the worker node with the reboot command
4\. When the OS comes back, you will need to start TOS with the command: tos start
a. If tos fails to start, you can check that the tos-registry pod is running with the command: kubectl get pods -n kube-system
5\. You can check the status of the pods by running: watch "kubectl get pod | grep -Pv '\\s+(\[1-9\]+)\\/\\1\\s+' | grep -v Completed"
a. When you run this command, it will show all of the pending pods that we are waiting for coming up. If you don’t see anything output from this command, then Tufin has completed starting.
6\. You can verify everything is looking good by logging into the GUI, and you can also check Tufin’s general status with this command: tos status
7\. Sometimes, the extensions pods (report-pack, ispa, etc…) will need to be restarted to capture the current environment variables of Tufin. You can restart ALL of them by running this: kubectl get pods -oname | grep "$(tos apps list | awk '{print $1}' | egrep -v "NAME")" | xargs kubectl delete

Shawn Babinyecz
Professional Services
Solutions Architect