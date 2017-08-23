
``` shell

firewall-cmd --permanent --add-port={9200/tcp,9300/tcp}
firewall-cmd --reload
firewall-cmd --state
firewall-cmd --list-all

```
