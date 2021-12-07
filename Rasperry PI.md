Rasperry PI

rasp-conf reduce GPU memory to minimum 16M



detect new disk

```
for host in /sys/class/scsi_host/*; do echo "- - -" | sudo tee $host/scan; ls /dev/sd* ; done
```