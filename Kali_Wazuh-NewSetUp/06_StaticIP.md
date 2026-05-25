```bash
┌──(kali㉿kali)-[~]
└─$ ip addr 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:60:6e:4f:2a:af brd ff:ff:ff:ff:ff:ff
    inet 192.168.100.69/24 brd 192.168.100.255 scope global dynamic noprefixroute eth0
       valid_lft 85880sec preferred_lft 85880sec
    inet6 fe80::a60:6eff:fe4f:2aaf/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether dc:85:de:a1:a3:2f brd ff:ff:ff:ff:ff:ff
    inet 192.168.100.68/24 brd 192.168.100.255 scope global dynamic noprefixroute wlan0
       valid_lft 85877sec preferred_lft 85877sec
    inet6 fe80::81f7:9f03:333a:b221/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
                                                                                 
┌──(kali㉿kali)-[~]
└─$ 
```

ညီလေးရဲ့ Network Interface တွေကို ကြည့်ရတာ **`eth0`** (Wired) နဲ့ **`wlan0`** (Wireless) နှစ်ခုလုံး Active ဖြစ်နေပြီး IP နှစ်ခု လုနေတာကို တွေ့ရပါတယ်။ ဒါဟာ Lab တစ်ခု တည်ဆောက်တဲ့အခါ Wazuh Dashboard နဲ့ ချိတ်ဆက်မှုတွေ အမြဲလွဲနေရတဲ့ အဓိက အကြောင်းရင်း ဖြစ်နိုင်ပါတယ်။

Lab အတွက် စနစ်တကျဖြစ်အောင် **`eth0` (Wired)** ကိုပဲ အဓိကထားပြီး Static IP သတ်မှတ်လိုက်ရအောင်။

### အဆင့် (၁) - Static IP သတ်မှတ်ခြင်း (eth0 အတွက်)

Terminal မှာ အောက်ပါ Command တွေကို တစ်ကြောင်းချင်း ရိုက်ထည့်ပါ။ (IP ကို `192.168.100.5` မှာပဲ Fix လုပ်ပါမယ်)

```bash
# Wired connection 1 ကို IPv4 Manual ပြောင်းခြင်း
sudo nmcli con mod "Wired connection 1" ipv4.addresses 192.168.100.5/24
sudo nmcli con mod "Wired connection 1" ipv4.gateway 192.168.100.1
sudo nmcli con mod "Wired connection 1" ipv4.dns "8.8.8.8"
sudo nmcli con mod "Wired connection 1" ipv4.method manual

# ပြောင်းလဲမှုများကို အသက်သွင်းခြင်း
sudo nmcli con up "Wired connection 1"
```

### အဆင့် (၂) - Wireless ကို ခေတ္တပိတ်ထားခြင်း

Lab အလုပ်လုပ်နေချိန်မှာ IP လိပ်စာ နှစ်ခုနဲ့ ရှုပ်မနေစေဖို့ `wlan0` ကို ခေတ္တ ပိတ်ထားတာ ပိုကောင်းပါတယ်။

```bash
sudo ip link set wlan0 down
```

### အဆင့် (၃) - ပြောင်းလဲမှု အတည်ပြုခြင်း

အခု `ip addr` ကို ထပ်ရိုက်ကြည့်ပါ။ `eth0` မှာ `192.168.100.5` ဆိုတဲ့ IP လေး မြင်ရပြီဆိုရင် အောင်မြင်ပါပြီ။

---

### နောက်ထပ်လုပ်ဆောင်ရန် (Wazuh Dashboard အတွက်)

အခု IP က `192.168.100.5` မှာ Fix ဖြစ်သွားပြီဆိုတော့ Wazuh Dashboard ရဲ့ configuration ဖိုင်တွေထဲမှာ IP အဟောင်း (`.69` လို့ ပေါ်နေတဲ့နေရာ) တွေကို `.5` နဲ့ အစားထိုးပေးဖို့ လိုပါမယ်။

အစ်ကို့ကို အောက်ပါ Command လေး ရိုက်ပြီး ဘာတွေ ထွက်လာလဲ ပြောပြပေးပါ။ အဲဒီထဲကမှ IP အဟောင်းတွေကို ရှာပြီး အတူတူ ပြင်ကြတာပေါ့။

```bash
grep -r "192.168.100.69" /etc/wazuh-* /var/ossec/etc/
```
