### Windows 11 မှာ Wazuh Agent ထည့်သွင်းခြင်း အဆင့်ဆင့်

#### အဆင့် (၁): Wazuh Dashboard မှ Agent Registration Command ရယူခြင်း

၁။ Kali မှာ run နေတဲ့ Wazuh Dashboard ကို ဖွင့်ပါ။

၂။ ဘယ်ဘက်ခြမ်းက **Wazuh icon** ကို နှိပ်ပြီး **Agents** ကို ရွေးပါ။

၃။ အပေါ်ညာဘက်ထောင့်က **"Deploy new agent"** ခလုတ်ကို နှိပ်ပါ။

၄။ သင့်ရဲ့ Window 11 က **Windows** ဖြစ်တဲ့အတွက် Operating System အဖြစ် **Windows** ကို ရွေးပါ။

၅။ **Wazuh server address** နေရာမှာ သင့် Kali Linux ရဲ့ IP Address ကို ရိုက်ထည့်ပါ။

၆။ အောက်က **Command** box မှာ ပေါ်လာမယ့် စာသားတွေကို Copy ယူထားပါ။ (ဒီ command က သင့် agent ကို register လုပ်ပေးမှာပါ)။

#### အဆင့် (၂): Windows 11 တွင် Command run ခြင်း

၁။ သင့် Windows 11 မှာ **PowerShell** ကို **Run as Administrator** နဲ့ ဖွင့်ပါ။

၂။ အခုနက Copy ယူထားတဲ့ Command (ဥပမာ: `Invoke-WebRequest -Uri ...`) ကို PowerShell ထဲမှာ Paste လုပ်ပြီး Enter နှိပ်ပါ။

၃။ အောင်မြင်စွာ install ဖြစ်သွားပြီဆိုရင် Agent Service ကို စတင်ဖို့ အောက်ပါ command ကို ရိုက်ပါ။

```powershell
NET START WazuhSvc
```

#### အဆင့် (၃): Agent အလုပ်လုပ်နေခြင်းကို စစ်ဆေးခြင်း

၁။ Kali ရဲ့ Wazuh Dashboard **Agents** စာမျက်နှာကို ပြန်သွားပါ။

၂။ ခဏစောင့်ပြီးရင် သင့် Windows 11 က **Active** အနေနဲ့ ပေါ်လာပါလိမ့်မယ်။

၃။ အကယ်၍ မပေါ်သေးဘူးဆိုရင် Agent ရဲ့ Status ကို Windows PowerShell မှာ ဒီလိုစစ်ဆေးနိုင်ပါတယ်:

```powershell
& "C:\Program Files (x86)\ossec-agent\agent-control.exe" -i
```

---

### အရေးကြီးတဲ့ အချက်များ

- **Firewall:** Windows 11 ရဲ့ Firewall က Wazuh Server (Kali IP) ကို Port **1514** (TCP/UDP) နဲ့ ဆက်သွယ်ခွင့် ပေးထားဖို့ လိုပါတယ်။
- **Connectivity:** Kali နဲ့ Windows 11 ဟာ Network တစ်ခုတည်းမှာပဲ ရှိနေဖို့ သို့မဟုတ် တစ်ခုနဲ့တစ်ခု Ping လို့ ရနေဖို့ လိုပါတယ်။
