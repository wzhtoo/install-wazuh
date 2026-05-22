(`C:\Users\<userName>\AppData\Local\Google\Chrome\User Data\optimization_guide_model_store`) ကို Wazuh ကနေ **File Integrity Monitoring (FIM)** Custom Rule

### အဆင့် (၁) Wazuh Agent ရဲ့ `ossec.conf` မှာ Path ထည့်ခြင်း

Windows Agent စက်ရဲ့ `C:\Program Files (x86)\ossec-agent\ossec.conf` ဖိုင်ကို Edit လုပ်ပြီး `<syscheck>` ထဲမှာ အောက်ပါအတိုင်း ထည့်ပေးရပါမယ်။

```xml
<syscheck>
  <directories check_all="yes" realtime="yes">C:\Users\*\AppData\Local\Google\Chrome\User Data\optimization_guide_model_store</directories>
</syscheck>
```

_(မှတ်ချက်: `<userName>` နေရာမှာ `_` ကို သုံးလိုက်ခြင်းဖြင့် User အားလုံးအတွက် အကျုံးဝင်သွားပါမယ်။)\*

ပြီးရင် Wazuh Agent ကို Restart ချပေးပါ။

```powershell
Restart-Service wazuh
```

---

### အဆင့် (၂) Custom Rule ရေးသားခြင်း

ဖိုင်တစ်ခုခု အပြောင်းအလဲဖြစ်တဲ့အခါ (ဥပမာ- ဖိုင်အသစ်ဝင်လာတာ၊ ဖျက်လိုက်တာ၊ ပြင်လိုက်တာ) သတိပေးချက် (Alert) တက်လာအောင် Wazuh Manager ရဲ့ `local_rules.xml` မှာ Rule အသစ် ထည့်ပါမယ်။

Wazuh Manager (Ubuntu) မှာ `/var/ossec/etc/rules/local_rules.xml` ကိုဖွင့်ပြီး အောက်ပါအတိုင်း ထည့်ပါ -

```xml
<group name="chrome_ai_monitor,">
  <rule id="100001" level="7">
    <if_sid>550</if_sid> <field name="file">C:\\Users\\.*\\AppData\\Local\\Google\\Chrome\\User Data\\optimization_guide_model_store</field>
    <description>Chrome AI Model Store folder modified: Potential Gemini Nano model update.</description>
    <group>syscheck,pci_dss_11.5,</group>
  </rule>
</group>
```

**Rule ရှင်းလင်းချက်:**

- `if_sid 550`: Wazuh ရဲ့ FIM event တွေကို အခြေခံထားပါတယ်။
- `level 7`: အရေးကြီးမှုအဆင့်ကို သတ်မှတ်ထားတာပါ။ (၁၂ အထက်ဆိုရင် အန္တရာယ်ပိုများတယ်လို့ သတ်မှတ်ပါတယ်)။
- `description`: Dashboard မှာ ပေါ်လာမယ့် စာသားပါ။

---

### အဆင့် (၃) စမ်းသပ်ခြင်း

1. အပြောင်းအလဲလုပ်ဖို့အတွက် အဲဒီ Folder ထဲကို File အသစ်တစ်ခု Copy ကူးထည့်လိုက်ပါ (သို့) ဖိုင်နာမည်တစ်ခု ပြောင်းကြည့်ပါ။
2. Wazuh Dashboard -> **Security Events** ကို သွားကြည့်ပါ။
3. Rule ID `100001` နဲ့ Alert တက်လာတာကို တွေ့ရပါလိမ့်မယ်။

### သတိထားရမည့်အချက်

ဒီ Folder ထဲမှာ Chrome က Update ခဏခဏ လုပ်တတ်တဲ့အတွက် Alert တွေ တောက်လျှောက်တက်နေမှာကို စိုးရိမ်ရပါတယ်။ အဲဒီအခါကျရင် **Wazuh Dashboard** ကနေ **"Filter"** သုံးပြီး အဲဒီ Rule ကို အချိန်တစ်ခုပဲ ပေါ်အောင် လုပ်ထားလို့ရပါတယ်။
