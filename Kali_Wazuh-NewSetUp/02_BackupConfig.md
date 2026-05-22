PowerShell ကနေ `ossec.conf` ကို backup လုပ်ဖို့ အောက်ပါ command ကို **Administrator အဖြစ်** PowerShell မှာ run ပေးပါ။

## **PowerShell Backup Command**

### **Method 1: Copy-Item (အကြံပြုလိုပါတယ်)**

```powershell
Copy-Item -Path "C:\Program Files (x86)\ossec-agent\ossec.conf" -Destination "C:\Program Files (x86)\ossec-agent\ossec.conf.backup"
```

### **Method 2: တိုတောင်းတဲ့ ပုံစံ**

```powershell
copy "C:\Program Files (x86)\ossec-agent\ossec.conf" "C:\Program Files (x86)\ossec-agent\ossec.conf.backup"
```

### **Method 3: timestamp ပါတဲ့ backup** (အကြံပြုဆုံး)

```powershell
$date = Get-Date -Format "yyyyMMdd_HHmmss"
Copy-Item -Path "C:\Program Files (x86)\ossec-agent\ossec.conf" -Destination "C:\Program Files (x86)\ossec-agent\ossec.conf.backup_$date"
```

### One line Command

```powershell
Copy-Item -Path "C:\Program Files (x86)\ossec-agent\ossec.conf" -Destination "C:\Program Files (x86)\ossec-agent\ossec.conf.backup_$(Get-Date -Format 'yyyyMMdd_HHmmss')"
```

## **Backup အောင်မြင်ကြောင်း စစ်ဆေးခြင်း**

### **Backup file ရှိမရှိ စစ်ပါ**

```powershell
Get-ChildItem "C:\Program Files (x86)\ossec-agent\" | Where-Object {$_.Name -like "ossec.conf*"}
```

### **Output မှာ ဒီလိုမြင်ရမယ်:**

```
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        2025-10-02  10:00 AM          12345 ossec.conf
-a----        2025-10-02  10:05 AM          12345 ossec.conf.backup
-a----        2025-10-02  10:06 AM          12345 ossec.conf.backup_20251002_100630
```

## **အဆင့်ဆင့် လုပ်ဆောင်နည်း**

### **Step 1: PowerShell ကို Administrator အနေနဲ့ ဖွင့်ပါ**

```
Windows Key → Type "PowerShell" → Right-click → "Run as administrator"
```

### **Step 2: Path ရှိမရှိ စစ်ပါ**

```powershell
Test-Path "C:\Program Files (x86)\ossec-agent\ossec.conf"
```

- `True` ပြရင် ဖိုင်ရှိတယ်
- `False` ပြရင် ဖိုင်မရှိဘူး

### **Step 3: Backup လုပ်ပါ**

```powershell
Copy-Item -Path "C:\Program Files (x86)\ossec-agent\ossec.conf" -Destination "C:\Program Files (x86)\ossec-agent\ossec.conf.backup" -Verbose
```

`-Verbose` ကြောင့် ဘာဖြစ်သွားသလဲဆိုတာပြပေးမယ်

### **Step 4: Verify**

```powershell
Get-FileHash "C:\Program Files (x86)\ossec-agent\ossec.conf"
Get-FileHash "C:\Program Files (x86)\ossec-agent\ossec.conf.backup"
```

Hash တူရင် backup အောင်မြင်ပြီ

## **အဆင်ပြေရင်**

Backup အောင်မြင်ပြီဆိုရင် configuration ပြင်လို့ရပါပြီ။ ပြင်ဆင်တဲ့အခါ **Notepad (Administrator)** သုံးဖို့ မမေ့ပါနဲ့။
