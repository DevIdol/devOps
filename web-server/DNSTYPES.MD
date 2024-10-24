# DNS Record Types များ

## 1. A Record (Address Record)
- **ဘာလဲ**: Domain name ကို IPv4 address နဲ့ချိတ်ဆက်ပေးတယ်။
- **နမူနာ**: `example.com -> 192.168.1.1`
- **သုံးပုံ**: Web server တစ်ခုရဲ့ IP address ကို pointing လုပ်ဖို့သုံးပါတယ်။

## 2. AAAA Record (IPv6 Address Record)
- **ဘာလဲ**: Domain name ကို IPv6 address နဲ့ချိတ်ဆက်ပေးတယ်။
- **နမူနာ**: `example.com -> 2001:0db8:85a3:0000:0000:8a2e:0370:7334`
- **သုံးပုံ**: IPv6-based servers များကို pointing လုပ်ဖို့သုံးတယ်။

## 3. CNAME Record (Canonical Name Record)
- **ဘာလဲ**: Domain တစ်ခုကို တခြား domain တစ်ခုရဲ့ alias အဖြစ် သတ်မှတ်ပေးတယ်။
- **နမူနာ**: `www.example.com -> example.com`
- **သုံးပုံ**: Subdomain တစ်ခုက တခြား domain တစ်ခုဆီ redirect ဖြစ်စေချင်တဲ့အခါ သုံးတယ်။

## 4. MX Record (Mail Exchange Record)
- **ဘာလဲ**: Email messages တွေကိုပေးပို့ရာတွင် သုံးသော mail server address ကိုသတ်မှတ်ပေးတယ်။
- **နမူနာ**: `example.com -> mail.example.com`
- **သုံးပုံ**: Email delivery ကို handle လုပ်ဖို့သတ်မှတ်ရာတွင် သုံးတယ်။ (e.g., Gmail, Zoho mail)

## 5. TXT Record (Text Record)
- **ဘာလဲ**: Text-based information တွေကို DNS records ထဲမှာ ထည့်သွင်းဖို့သုံးတယ်။
- **နမူနာ**: `example.com -> "v=spf1 include:mailserver.com ~all"`
- **သုံးပုံ**: Domain verification (SPF, DKIM), security protocols, domain ownership proof စသည်ဖြင့် သုံးတယ်။

## 6. NS Record (Name Server Record)
- **ဘာလဲ**: Domain name ရဲ့ DNS zone ကို manage လုပ်မယ့် Name Server တွေကိုသတ်မှတ်ပေးတယ်။
- **နမူနာ**: `example.com -> ns1.example.com, ns2.example.com`
- **သုံးပုံ**: Domain name resolution ကို manage လုပ်တဲ့ name servers များကို သတ်မှတ်ရာတွင် သုံးတယ်။

## 7. PTR Record (Pointer Record)
- **ဘာလဲ**: IP address ကို domain name အဖြစ်ပြန်လည်ပြောင်းပေးတဲ့ DNS record ဖြစ်တယ်။
- **နမူနာ**: `192.168.1.1 -> example.com`
- **သုံးပုံ**: Reverse DNS lookup (IP address ကို domain အနေနဲ့ပြန်စစ်) လုပ်ဖို့သုံးတယ်။

## 8. SRV Record (Service Record)
- **ဘာလဲ**: Service (protocol/service) တစ်ခုကို specific hostname နဲ့ port တစ်ခုသတ်မှတ်ဖို့ သုံးတယ်။
- **နမူနာ**: `_sip._tcp.example.com -> 10 5 5060 sipserver.example.com`
- **သုံးပုံ**: Specific service (like SIP, XMPP) တိုင်းပြန် server နှင့် communication လုပ်ဖို့သုံးတယ်။

## 9. SOA Record (Start of Authority Record)
- **ဘာလဲ**: DNS zone အတွက် အခြေခံသော information (admin email, refresh rate, serial number) များကိုထောက်ပံ့ပေးတယ်။
- **နမူနာ**: Contains information like `ns1.example.com admin@example.com`
- **သုံးပုံ**: DNS zone management-related metadata ထောက်ပံ့ရာတွင် သုံးတယ်။

## 10. CAA Record (Certification Authority Authorization)
- **ဘာလဲ**: Domain တစ်ခုအတွက် SSL/TLS certificates ထုတ်ခွင့်ပေးမယ့် Certification Authority (CA) ကို သတ်မှတ်ပေးတယ်။
- **နမူနာ**: `example.com -> 0 issue "letsencrypt.org"`
- **သုံးပုံ**: Certificates ထုတ်ခြင်း process ကို control လုပ်ဖို့ သုံးတယ်။


## Summary
- ` A ` record က domain ကို IP ဆီချိတ်တယ်။
- ` CNAME ` က alias တစ်ခုလုပ်တယ်။
- ` MX ` က email handling လုပ်တဲ့ server ကိုသတ်မှတ်တယ်။
- ` TXT ` က text-based data ထည့်တယ်၊ usually security verification အတွက်။

