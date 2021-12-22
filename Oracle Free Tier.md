Oracle Free Tier



Shape: A1 4cpu 24G

boot volume: 100G



### Enable port 80 and 443

```shell
sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 80 -j ACCEPT
sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 443 -j ACCEPT
sudo netfilter-persistent save
```

