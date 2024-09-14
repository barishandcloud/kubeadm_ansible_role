## content of 01-netcfg.yml
```
network :
  version : 2
  renderer : networkd
  ethernets :
    enp0s3 :
      dhcp4 : yes
    enp0s8 :
      dhcp4 : no
      dhcp6 : no
      addresses : [###ipaddress###/24]
      nameservers :
        addresses : [8.8.8.8]

#Replace ###ipaddress### with desired IP address, place in /etc/netplan/ dir &
sudo netplan apply
 ```