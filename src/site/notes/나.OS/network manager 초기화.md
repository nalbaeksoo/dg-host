---
{"dg-publish":true,"permalink":"/나.OS/network manager 초기화/","dgPassFrontmatter":true,"noteIcon":""}
---


#### CreateDate : 2025-08-11

### GOAL
NIC 인식할때 불필요한 profile 이름이나, 부적절한 이름으로 생성된 경우 (보통 vmguest os clone으로 인해) 초기화방법

### Tested Env, APPLIES TO:
Oracle Linux - 8.10
### SOLUTION

- 아래의 내용을 수행하되, udev 내용은 백업필요
```
rm -f /etc/NetworkManager/system-connections/*
rm -f /etc/udev/rules.d/70-persistent-net.rules
nmcli connection reload
reboot
```

- rebooting 이후 재 인식해야할때 아래 명령어 수행
```
nmcli connection add type ethernet ifname ens33 con-name ens33 autoconnect yes

수동ip 설정
nmcli connection modify ens33 ipv4.method manual ipv4.addresses 192.168.0.100/24 ipv4.gateway 192.168.0.1 ipv4.dns 8.8.8.8
nmcli connection up ens33
```

### Conclusion


### REFERENCES
chatgpto5

#nmcli #nic #networkmanager