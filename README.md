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
