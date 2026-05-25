## Step-by-Step Implementation Guide

အဓိက အပိုင်း (၃) ပိုင်း ခွဲပြီး လုပ်ဆောင်ရပါမယ်။

### အပိုင်း (၁) Windows 11 စက်မှာ Sysmon သွင်းခြင်း

1. **Download:** Microsoft ဝဘ်ဆိုက်ကနေ Sysmon ဇစ်ဖိုင်ကို ဒေါင်းလုဒ်ဆွဲပြီး `C:\Sysmon` ဆိုတဲ့ Folder ထဲ ဖြည်ထည့်ပါ။
2. **Config ယူခြင်း:** SwiftOnSecurity ရဲ့ `sysmonconfig-export.xml` ကို ဒေါင်းလုဒ်ဆွဲပြီး၊ `C:\Sysmon` ထဲမှာပဲ အမည်ကို `config.xml` လို့ ပြောင်းသိမ်းပါ။
3. **Install လုပ်ခြင်း:** CMD ကို **Run as administrator** နဲ့ ဖွင့်ပြီး အောက်ပါ Command တွေ ရိုက်ထည့်ပါ -

```cmd
cd C:\Sysmon
sysmon64.exe -i config.xml -accepteula
```

---

### အပိုင်း (၂) Windows 11 စက်က Wazuh Agent ကို လမ်းကြောင်းပြပေးခြင်း

Sysmon က ထုတ်ပေးတဲ့ Log တွေကို Kali က Wazuh Server ဆီ လှမ်းပို့ဖို့အတွက် Agent ရဲ့ Configuration ကို ပြင်ရပါမယ်။

1. `C:\Program Files (x86)\ossec-agent\` ထဲက **`ossec.conf`** ဖိုင်ကို Notepad (Admin) နဲ့ ဖွင့်ပါ။
2. `<ossec_config>` tag အောက်မှာ အောက်ပါ Log Channel လမ်းကြောင်းကို ထည့်ပေးပါ -

```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

3. ဖိုင်ကို Save လုပ်ပြီး ပိတ်ပါ။
4. Windows Services (`services.msc`) ထဲသွားပြီး **Wazuh** Service ကို **Restart** ချပေးပါ။

---

### အပိုင်း (၃) Kali က Wazuh Server မှာ Custom Rules ရေးခြင်း

အခုဆိုရင် Log တွေက ဆာဗာဆီ ရောက်လာပါပြီ။ ဒါပေမဲ့ Viber ကနေ သံသယဖြစ်ဖွယ် လုပ်ဆောင်ချက်တွေ ရှိလာရင် Alert တက်ဖို့ Rule ရေးပေးရပါမယ်။

1. Kali (သို့မဟုတ် Ubuntu) က Wazuh Server ရဲ့ terminal ကို ဖွင့်ပါ။
2. `/var/ossec/etc/rules/local_rules.xml` ဖိုင်ကို လှမ်းဖွင့်ပါ -

```bash
sudo nano /var/ossec/etc/rules/local_rules.xml
```

3. ဖိုင်ရဲ့ အောက်ဆုံးနားက `<group name="local,">` အထဲမှာ အောက်ပါ **Custom Rules** တွေကို ထည့်သွင်းပါ -

```xml
<rule id="100010" level="10">
  <if_sid>61603</if_sid>
  <field name="win.eventdata.parentImage">\\Viber.exe</field>
  <field name="win.eventdata.image">cmd.exe|powershell.exe|wscript.exe|cscript.exe|mshta.exe</field>
  <description>Wazuh Alert: Suspicious child process spawned from Viber! Potential Phishing Link Execution.</description>
  <mitre>
    <id>T1204.002</id>
  </mitre>
</rule>

<rule id="100011" level="7">
  <if_sid>61613</if_sid>
  <field name="win.eventdata.image">\\Viber.exe</field>
  <field name="win.eventdata.targetFilename">\.exe$|\.bat$|\.vbs$|\.ps1$|\.lnk$</field>
  <description>Wazuh Alert: Viber downloaded an executable or script file $(win.eventdata.targetFilename).</description>
  <mitre>
    <id>T1105</id>
  </mitre>
</rule>
```

4. ဖိုင်ကို Save လုပ်ပြီး ထွက်ပါ (`Ctrl+O` > `Enter` > `Ctrl+X`)။
5. ပြင်ဆင်မှုတွေ အသက်ဝင်ဖို့ **Wazuh Manager ကို Restart ချပေးပါ** -

```bash
# ရိုးရိုး သွင်းထားတာဆိုလျှင်
sudo systemctl restart wazuh-manager

# Docker စနစ်ဆိုလျှင်
docker restart wazuh-manager
```

---

## 📊 လုပ်ဆောင်ချက် ပြီးမြောက်ကြောင်း စစ်ဆေးခြင်း (Testing)

အဆင့်အားလုံး ပြီးသွားရင်တော့ Kali က **Wazuh Dashboard** ကို Browser ကနေ ဖွင့်ပါ။

- **Discover** ထဲကို သွားပြီး Search bar မှာ `win.system.providerName: "Microsoft-Windows-Sysmon"` လို့ ရိုက်ရှာကြည့်ပါ။
- Sysmon Log တွေ တက်နေပြီဆိုရင် လူကြီးမင်းရဲ့ Lab တည်ဆောက်မှု အောင်မြင်သွားပါပြီ။
