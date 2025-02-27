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
