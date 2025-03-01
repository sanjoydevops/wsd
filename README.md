# Configuration management
 
### This command can display all ansible_ configuration for a host
`ansible webserver -m setup -i ~/wsd/ansible/inventory.yml`
or,
`ansible 192.168.1.105 -m setup -i ~/wsd/ansible/inventory.yml`

### configure a cron job that runs logrotate on all machines every 10 minutes between 2h - 4h.

For a single server

`crontab -e
*/10 2,3,4 * * * /usr/sbin/logrotate /etc/logrotate.conf`

For all server

`ansible-playbook -i ~/wsd/ansible/inventory.yml ~/wsd/ansible/logrotate_cron.yml --ask-become-pass`

### SSH these servers and Deploy ntpd package to the following servers.

```
ssh user@app-vm1.fra1.internal(192.168.0.2)
ssh user@db-vm1.fra1.db(192.168.0.3)
ssh user@web-vm1.fra1.web(192.168.0.4)
```

### After installing ntpd in these servers, custom configurations are attached to ntpd.conf file in /etc/ntpd.conf, run the below command.

```
sudo systemctl restart ntp
ntpq -p
```

# Docker/Kubernetes

### docker-compose for a nginx server.

```
docker-compose up -d
```

### Which Kubernetes command you will use to identify the reason for a pod restart in the project "internal" under namespace "production".

### identify the reason for a pod restart in the project.
```
kubectl logs -f <pod-name> -n production --previous
```
### For debugging use more command

```
kubectl get pods -n production -l project=internal
kubectl describe pod <pod-name> -n production
kubectl logs <pod-name> -c <container-name>-n production
```

### From a Kubernetes configuration perspective, there are a few possible reasons why your java-app pod is randomly restarting. 

1. Memory Overcommitment (Out of Memory - OOMKill)
Your pod's total memory consumption seems very close to the configured limits:

```
Total Memory Usage 
java-app: 951Mi java-app-logrotate: 45Mi 
java-app-fluentd: 84Mi 
mongos: 62Mi 
Total Used = 951 + 45 + 84 + 62 = 1142Mi
Memory Limit = 1500Mi
```
Even though you have some buffer left, your Java process (java-app) has an -Xmx value of 1000M, meaning the JVM itself can allocate up to 1000Mi memory, which could lead to unexpected OOMKill (Out of Memory Kill) when additional memory is needed.

Possible Fix:
Reduce the -Xmx value to something like 900M to leave room for other processes. Increase the memory limit if possible.
Check OOMKill logs:

```kubectl get pod java-app-7d9d44ccbf-lmvbc -o yaml | grep -i oom```

2. CPU Starvation

```
The total CPU requested is:
java-app: 3m
java-app-logrotate: 1m
java-app-fluentd: 1m
mongos: 4m
Total Used = 9m
```
The limit is 2000m, so CPU is not a likely issue unless the application has a CPU-intensive operation that temporarily spikes usage and gets throttled.

Possible Fix:
Check CPU throttling using:

```kubectl describe pod java-app-7d9d44ccbf-lmvbc | grep -i throttling```  (If CPU is throttled, increase the CPU request.)

3. Liveness or Readiness Probe Failures

Describe the pod and look for probe failures

```kubectl describe pod java-app-7d9d44ccbf-lmvbc```

If probes are failing:
Increase initialDelaySeconds in the probe settings. Ensure the probe endpoint is responding correctly.


  




