# Odoo Agentic Development with Codex CLI

GitHub Repository: [Duskwrath/codex_odoo_agentic](https://github.com/Duskwrath/codex_odoo_agentic)

ဒီ repo မှာ Codex ကို prompt ပေးလိုက်တာနဲ့ Odoo module development workflow တစ်ခုလုံးကို agentic development ပုံစံနဲ့ လုပ်ဆောင်နိုင်အောင် နမူနာပြထားပါတယ်။

Codex က prompt အတိုင်း code ရေးပေးပြီးနောက် code quality စစ်တဲ့အဆင့်တွေကိုပါ ဆက်လက်လုပ်ဆောင်ပါတယ်။

လုပ်ဆောင်ပုံ workflow က အကြမ်းအားဖြင့် အောက်ပါအတိုင်းဖြစ်ပါတယ်။

- Prompt ပေးလိုက်တာနဲ့ လိုအပ်တဲ့ code ကိုရေးခြင်း
- Code style / format စစ်ခြင်း
- Lint စစ်ခြင်း
- Unit test run ခြင်း
- Test fail ဖြစ်ရင် code ပြန်ပြင်ခြင်း
- Test ပြန် run ခြင်း
- အားလုံး pass ဖြစ်မှ git commit လုပ်ခြင်း

ဒါ့အပြင် git push လုပ်တဲ့အချိန်မှာလည်း workflow checks အားလုံး pass ဖြစ်မှ push process ကို အောင်မြင်အဖြစ်သတ်မှတ်ထားပါတယ်။

နမူနာအနေနဲ့ `sale approval` feature ကို လုပ်ပြထားပါတယ်။

ဒီလို workflow က Odoo Development မှာ manual ပြန်စစ်ရတဲ့အပိုင်းတွေကို လျှော့ချနိုင်ပြီး code quality, test reliability, development consistency တွေကို ပိုပြီးထိန်းထားနိုင်စေပါတယ်။

အထူးသဖြင့် team development မှာ commit မလုပ်ခင် format, lint, test တွေကို auto validate လုပ်ထားတာကြောင့် broken code တွေ main branch ထဲရောက်သွားနိုင်ခြေကို လျှော့ချပေးနိုင်ပါတယ်။
