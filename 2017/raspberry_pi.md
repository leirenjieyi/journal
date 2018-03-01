### 无线网卡设置
*1 配置 /etc/wpa_supplicant/wpa_supplicant.conf

network={ \
  ssid="name" \
  psk="password" \
}

*2 配置 /etc/network/interfaces
```shell
allow-hotplug wlan0
iface wlan0 inet static
address 192.168.1.14/24
gateway 192.168.1.1
wpa-ssid ni_wei
wpa-psk wuziyao_123
  wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf

allow-hotplug wlan1
iface wlan1 inet dhcp
   wpa-ssid ni_wei
   wpa-psk wuziyao_123
        wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
```