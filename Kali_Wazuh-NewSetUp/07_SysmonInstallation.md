Windows 10 VM မှာ Sysmon ကို Install လုပ်ပြီးနောက် Event Viewer မှာ မပေါ်တာဟာ အဖြစ်များတဲ့ ပြဿနာတစ်ခုပါ။ Sysmon က စနစ်ထဲမှာ service တစ်ခုအနေနဲ့ အလုပ်လုပ်တာဖြစ်လို့ အောက်ပါအချက်တွေကို အဆင့်ဆင့် စစ်ဆေးကြည့်ပေးပါ။

### ၁။ Sysmon အလုပ်လုပ်နေကြောင်း အတည်ပြုပါ

Sysmon က တကယ်ပဲ Running ဖြစ်နေလားဆိုတာ အရင်ဆုံးစစ်ဆေးရပါမယ်။ PowerShell ကို **Run as Administrator** နဲ့ဖွင့်ပြီး အောက်ပါ Command ကိုရိုက်ပါ ။

```powershell
Get-Service | Where-Object {$_.Name -like "*Sysmon*"}
```

- အကယ်၍ Status က `Running` မဟုတ်ဘဲ ပေါ်မလာဘူးဆိုရင် Sysmon က အောင်မြင်စွာ Install မဖြစ်သေးတာပါ။

### ၂။ Configuration File မှန်ကန်စွာအသုံးပြုပါ

Sysmon ကို Install လုပ်တဲ့အခါ Configuration file (xml) မပါဘဲ install လုပ်ရင် default အနေနဲ့ ဘာကိုမှ logging လုပ်မှာမဟုတ်ပါဘူး။

- Install ပြန်လုပ်ကြည့်ပါ။ အောက်ပါအတိုင်း Config file နဲ့တွဲပြီး run ကြည့်ပါ ။

```powershell
.\Sysmon64.exe -i sysmonconfig.xml
```

_(မှတ်ချက် - `sysmonconfig.xml` နေရာမှာ အစ်ကိုဒေါင်းထားတဲ့ config file နာမည်အမှန်ကို ထည့်ပါ)_

### ၃။ Event Viewer ထဲမှာ ရှာဖွေနည်း (နေရာမှန်ကိုကြည့်ပါ)

Event Viewer မှာ Sysmon ရဲ့ logs တွေက သာမန် System logs တွေနဲ့ ရောမနေပါဘူး။ အောက်ပါအတိုင်း သွားကြည့်ပေးပါ -

1. **Event Viewer** ကို ဖွင့်ပါ။
2. ဘယ်ဘက်ခြမ်း panel မှာ **Applications and Services Logs** ကို နှိပ်ချပါ။
3. **Microsoft** -> **Windows** -> **Sysmon** -> **Operational** ဆိုတဲ့ နေရာမှာ logs တွေ ရှိမရှိ စစ်ဆေးပါ။
4. အကယ်၍ **Operational** ဆိုတာ မတွေ့သေးဘူးဆိုရင် Sysmon service က အလုပ်မလုပ်သေးတာပါ။

### ၄။ Installation အောင်မြင်ကြောင်း စစ်ဆေးရန်

Install လုပ်တဲ့ Command အပြီးမှာ "Sysmon started" ဆိုတဲ့ စာသားပေါ်ရပါမယ်။ အောက်ပါ Command နဲ့ service ကို restart ချကြည့်ပါ ။

```powershell
.\Sysmon64.exe -c sysmonconfig.xml
```

(ဒါက ရှိပြီးသား config ကို update လုပ်ပေးတာပါ)
