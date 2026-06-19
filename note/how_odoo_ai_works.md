# How Odoo AI Works?

ဒီတစ်ခါမှာတော့ Odoo AI ဘယ်လို အလုပ်လုပ်တယ်ဆိုတာကို ကျနော် နားလည်သလောက် ရှင်းပြပေးမှာ ဖြစ်ပါတယ်။

Odoo AI က ပုံမှန် ရိုးရိုး chatbot အနေနဲ့ အလုပ်လုပ်တာမျိုး မဟုတ်ပါဘူး။ Odoo ထဲမှာ embed လုပ်ပေးထားပြီး assistance အနေနဲ့ အလုပ်လုပ်ပါတယ်။

User က AI ကို Odoo ရဲ့ list views, form views, search views, live chat, website, fields စတာတွေကနေ အသုံးပြုနိုင်ပါတယ်။

ဥပမာ user က -

- "ဒီ customer အကြောင်း summary ရေးပေးပါ"
- "ဒီ filtered result ကို Excel export လုပ်ပေးပါ"
- "Website page အတွက် content generate လုပ်ပေးပါ"
- "Knowledge article ထဲက အချက်အလက်အပေါ်မူတည်ပြီး ဖြေပေးပါ"
- "Livechat visitor ကို ဖြေပေးပါ"

စတဲ့ မေးခွန်းတွေ မေးနိုင်ပါတယ်။

ဒီအဆင့်မှာ AI model ကို မခေါ်သေးပါဘူး။ Odoo က user request နဲ့ current context ကို စုဆောင်းနေတဲ့အဆင့်ဖြစ်ပါတယ်။

## Frontend မှ Backend ဆီ Request ပို့ခြင်း

နောက်တစ်ဆင့်မှာတော့ user request ကို Odoo frontend ကနေ backend ဆီ ပို့ပါတယ်။

ဒီအဆင့်မှာ -

- AI chat ဖွင့်ခြင်း
- User prompt ပို့ခြင်း
- Current view information ပို့ခြင်း
- Livechat request များကို handle လုပ်ခြင်း
- Website AI generation ကို trigger လုပ်ခြင်း
- AI session / chat ပိတ်ခြင်း

စတာတွေကို လုပ်ဆောင်ပေးပါတယ်။

ဥပမာ user က list view ထဲကနေ Ask AI ကို သုံးတဲ့အခါ frontend က current model, view type, active filters, search facets စတဲ့ information တွေကို backend ဆီ ပို့ပါတယ်။

## Conversation Layer

Backend ဆီ request ရောက်လာတဲ့အခါ Odoo က `discuss.channel` ကို conversation layer အနေနဲ့ အသုံးပြုပါတယ်။

`discuss.channel` ထဲမှာ -

- Conversation messages
- Previous chat history
- Participants
- Linked AI agent
- AI session context
- Livechat data

စတာတွေ သိမ်းထားနိုင်ပါတယ်။

ဒါကြောင့် AI က prompt တစ်ကြောင်းတည်းပေါ်မူတည်ပြီး ဖြေတာမဟုတ်ပါဘူး။ Conversation history နဲ့ current session context ကိုပါ အသုံးပြုနိုင်ပါတယ်။

`discuss.channel` ကနေ AI agent ကို request ပို့ပါတယ်။

## AI Agent

ဒီနေရာမှာ `ai.agent` က core အနေနဲ့ အဓိက အလုပ်လုပ်ပေးပါတယ်။

`ai.agent` က -

- User prompt ကို ဖတ်ခြင်း
- Chat history ကို စုခြင်း
- System prompt ကို ထည့်ခြင်း
- Topic instructions တွေ ထည့်ခြင်း
- UI context ထည့်ခြင်း
- RAG context လိုအပ်ရင် ထည့်ခြင်း
- Available tools တွေ ပြင်ဆင်ခြင်း
- Selected LLM provider ဆီ request ပို့ဖို့ ပြင်ဆင်ခြင်း

စတာတွေကို လုပ်ဆောင်ပေးပါတယ်။

Agent က system prompt, topics, chat history, UI context, RAG, selected model / provider context, response style စတဲ့ context တွေကို ပေါင်းစပ်ပေးပါတယ်။

ဒီအဆင့်မှာ agent က final answer မထုတ်သေးပါဘူး။ LLM ဆီပို့မယ့် request ကို တည်ဆောက်နေတဲ့အဆင့်ဖြစ်ပါတယ်။

Agent က ပထမဆုံး system prompt, topics, chat history, UI context တွေကို ပေါင်းစပ်ပါတယ်။

ဥပမာ user က Sales Orders list view ထဲကနေ "ဒီ result ကို summarize လုပ်ပေးပါ" လို့ မေးရင် AI က current model က `sale.order` ဖြစ်တယ်၊ filter တွေ active ဖြစ်နေတယ်၊ user က ဘယ် company ထဲမှာ အလုပ်လုပ်နေတယ်ဆိုတာတွေ သိနိုင်ပါတယ်။

ဒီ context က AI ကို Odoo business environment အတွင်းမှာ နားလည်စေပါတယ်။

## RAG Flow

Agent မှာ sources attach လုပ်ထားရင် RAG flow ကို အသုံးပြုပါတယ်။ Sources တွေက file, URL, web page, article စတာတွေ ဖြစ်နိုင်ပါတယ်။

RAG ဆိုတာ Retrieval-Augmented Generation ကို ဆိုလိုပါတယ်။ Odoo AI ရဲ့ agent တစ်ခုမှာ sources attach လုပ်ထားတဲ့အခါ RAG ကို အသုံးပြုပါတယ်။

RAG က -

- Source content ကို extract လုပ်ပါတယ်
- Content ကို chunks ခွဲပါတယ်
- Chunk တစ်ခုချင်းစီကို embedding vector ပြောင်းပါတယ်
- Embeddings တွေကို `ai.embedding` ထဲမှာ သိမ်းပါတယ်

User question ရောက်လာတဲ့အခါ -

- User question ကို embedding ပြောင်းပါတယ်
- Stored source embeddings တွေနဲ့ similarity search လုပ်ပါတယ်
- အနီးစပ်ဆုံး chunks တွေကို ရွေးပါတယ်
- အဲဒီ chunks တွေကို LLM prompt context ထဲ ထည့်ပါတယ်

ဒီအဆင့်ကြောင့် AI က uploaded files, Knowledge articles, website content, internal documents တွေအပေါ် အခြေခံပြီး ဖြေနိုင်ပါတယ်။

## AI Tools

Agent မှာ topics တွေရှိပြီး topics ထဲမှာ tools တွေ attach လုပ်ထားရင် AI tools တွေကို LLM request ထဲ ထည့်ပေးပါတယ်။

Odoo မှာ AI tools တွေကို `ir.actions.server` မှတစ်ဆင့် expose လုပ်ပါတယ်။

ဥပမာ AI tools တွေက -

- Records ရှာခြင်း
- Grouped data ဖတ်ခြင်း
- Menus ဖွင့်ခြင်း
- Filters apply လုပ်ခြင်း
- CRM leads create လုပ်ခြင်း
- Fields update လုပ်ခြင်း
- Excel export generate လုပ်ခြင်း

စတာတွေကို လုပ်နိုင်ပါတယ်။

အရေးကြီးတာက LLM က Odoo code ကို တိုက်ရိုက် မ run ပါဘူး။ LLM က tool call request ပဲ ပြန်ပေးပါတယ်။ Odoo က arguments ကို validate လုပ်ပြီး သက်ဆိုင်ရာ server action ကို execute လုပ်ပါတယ်။

## LLM API Service

Prompt context, RAG context, AI tools တွေ ပြင်ပြီးသွားရင် `ai.agent` က request ကို LLM API Service ဆီ ပို့ပါတယ်။

Agent က ပြင်ဆင်ထားတဲ့ prompt, RAG context, tool definitions တွေကို LLM API Service ဆီ ပို့ပါတယ်။ LLM API Service က selected provider အလိုက် request format ပြောင်းပြီး external AI provider ဆီ ပို့ပါတယ်။

Provider က request ကို process လုပ်ပြီး result ပြန်ပေးပါတယ်။

Provider response က အဓိကအားဖြင့် နှစ်မျိုး ဖြစ်နိုင်ပါတယ်။

- Final answer
- Tool call request

Final answer ပြန်လာရင် response flow က agent ဆီ ပြန်သွားပါတယ်။

Tool call request ပြန်လာရင် Odoo က tool execution flow ကို ဆက်လုပ်ပါတယ်။ LLM က tool call ပြန်ပေးရင် Odoo က `ir.actions.server` tool ကို execute လုပ်ပါတယ်။

ဥပမာ -

- Search records tool
- Excel export tool
- Menu open / search adjust tool

စတာတွေကို ခေါ်နိုင်ပါတယ်။

Tool execution ပြီးသွားရင် tool result ကို LLM API Service ကနေ LLM ဆီ ပြန်ပို့ပါတယ်။

LLM က tool result ကို ကြည့်ပြီး final answer ပြန်ပေးနိုင်သလို နောက်ထပ် tool call ထပ်တောင်းနိုင်ပါတယ်။

ဒီ process က final response ရလာတဲ့အထိ loop လုပ်နိုင်ပါတယ်။

နောက်ဆုံးမှာ `ai.agent` က final response ကို `discuss.channel` ထဲကို message အဖြစ် post လုပ်ပါတယ်။ ဒီအချိန်မှ ကျနော်တို့ UI မှာ response မြင်ရမှာ ဖြစ်ပါတယ်။

ဒီလောက်ဆို အားလုံး Odoo AI ကို ကောင်းကောင်းအသုံးချနိုင်မယ်လို့ မျှော်လင့်ပါတယ်။

## Related Modules

Odoo မှာ Ollama Provider နဲ့ဖြစ်စေ Excel export နဲ့ဖြစ်စေ အသုံးပြုချင်တယ်ဆို အောက်ပါ module တွေကို အသုံးပြုနိုင်ပါတယ်။

- [Ollama Odoo](https://apps.odoo.com/apps/modules/19.0/slo_ai_ollam)
- [Excel Export](https://apps.odoo.com/apps/modules/19.0/slo_ai_export_excel)
