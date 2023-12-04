# Monitor OpenWrt nodes with Prometheus

## Install scripts

You should start with an up to date OpenWrt router. Install the prometheus node exporter scripts:

    prometheus-node-exporter-lua
    prometheus-node-exporter-lua-nat_traffic
    prometheus-node-exporter-lua-netstat
    prometheus-node-exporter-lua-openwrt
    prometheus-node-exporter-lua-wifi
    prometheus-node-exporter-lua-wifi_stations

## Change listening interface

```
ssh root@172.16.99.x
cd /etc/config/
cat prometheus-node-exporter-lua
sed -i.bak 's#loopback#MGMT99#g' /etc/config/prometheus-node-exporter-lua
/etc/init.d/prometheus-node-exporter-lua restart
```
