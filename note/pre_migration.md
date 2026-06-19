# Odoo Pre Migration

Odoo Development လုပ်တဲ့အခါ Model Structure ပြောင်းတာ၊ Table အသစ်ထည့်တာ၊ Model Name ပြောင်းတာ၊ Field Name ပြောင်းတာတွေ မကြာခဏ ဖြစ်တတ်ပါတယ်။ Development Environment မှာ Database ကို Drop ပြီး Module ကို ပြန် Install လုပ်လို့ရပေမယ့် Production Database မှာတော့ Existing Data မပျောက်စေဘဲ Structure အသစ်ကို ပြောင်းပေးဖို့ Migration လိုအပ်ပါတယ်။

ကျွန်တော်တို့မှာ `school_management` Module ရှိတယ်ဆိုပါစို့။

Version အဟောင်းမှာ Student Information ကို `res.partner` ပေါ်မှာ သိမ်းထားတယ်။

```python
class ResPartner(models.Model):
    _inherit = "res.partner"

    is_student = fields.Boolean()
    grade = fields.Char()
```

နောက်ပိုင်း Design ပြောင်းပြီး Student အတွက် သီးသန့် Model တစ်ခု ဖန်တီးချင်လာတယ်။

```python
class SchoolStudent(models.Model):
    _name = "school.student"
    _description = "School Student"

    partner_id = fields.Many2one("res.partner", required=True)
    grade = fields.Char()
```

ဒီလို Design ပြောင်းတဲ့အခါ Existing Data တွေကို `res.partner` ကနေ `school.student` ထဲကို ပြောင်းထည့်ဖို့ Migration Script လိုလာပါတယ်။

ဥပမာ -

```python
# pre-migrate_student.py
def table_exists(cr, table_name):
    cr.execute("""
        SELECT EXISTS (
            SELECT 1
            FROM information_schema.tables
            WHERE table_name = %s
        )
    """, [table_name])
    return cr.fetchone()[0]


def column_exists(cr, table_name, column_name):
    cr.execute("""
        SELECT EXISTS (
            SELECT 1
            FROM information_schema.columns
            WHERE table_name = %s
              AND column_name = %s
        )
    """, [table_name, column_name])
    return cr.fetchone()[0]


def migrate(cr, version):
    if not table_exists(cr, "res_partner"):
        return

    if not column_exists(cr, "res_partner", "is_student"):
        return

    if not column_exists(cr, "res_partner", "grade"):
        return

    cr.execute("""
        CREATE TABLE IF NOT EXISTS school_student (
            id SERIAL PRIMARY KEY,
            partner_id INTEGER NOT NULL,
            grade VARCHAR
        )
    """)

    cr.execute("""
        INSERT INTO school_student (partner_id, grade)
        SELECT rp.id, rp.grade
        FROM res_partner rp
        WHERE rp.is_student IS TRUE
          AND NOT EXISTS (
              SELECT 1
              FROM school_student ss
              WHERE ss.partner_id = rp.id
          )
    """)

```
ဒီ script ကတော့
1. res_partner table ရှိမရှိစစ်တယ်
2. is_student နဲ့ grade columns ရှိမရှိစစ်တယ်
3. school_student table မရှိရင် create လုပ်တယ်
4. is_student = True ဖြစ်တဲ့ partners တွေအတွက် school_student record create လုပ်တယ်
5. Already migrated ဖြစ်ပြီးသား records တွေကို duplicate မဖြစ်အောင် skip လုပ်တယ် စသည်ဖြင့် အလုပ်လုပ်ပေးပါတယ်။


ဒီနေရာမှာ သတိထားရမယ့်အချက်က `-u school_management` ပေးလိုက်တာနဲ့ Odoo က အဲဒီ module ကို upgrade process ထဲ ထည့်ပါတယ်။ ဒါပေမယ့် migration scripts တွေကို run မ run ဆုံးဖြတ်တာကတော့ Database ထဲက installed version, migration folder version, manifest version တို့ကို နှိုင်းယှဉ်ပြီး ဆုံးဖြတ်တာဖြစ်ပါတယ်။

ဥပမာ Database Installed Version က `19.0.1.0.0` ဖြစ်ပြီး Manifest Version က `19.0.1.0.1` ဖြစ်နေရင် migration folder ထဲက scripts တွေ run နိုင်ပါတယ်။

ဒါပေမယ့် Database Installed Version က `19.0.1.0.1` ဖြစ်ပြီး Manifest Version ကလည်း `19.0.1.0.1` ဖြစ်နေပြီဆိုရင် အဲဒီ same version migration folder ထဲက scripts တွေ ထပ်ပြီးမ run တော့ပါဘူး။

Custom module migration scripts တွေကို ပုံမှန်အားဖြင့် အောက်ပါ path မှာထားပါတယ်။

```text
school_management/migrations/<version>/
```

ဥပမာ -

```text
school_management/
└── migrations/
    └── 19.0.1.0.1/
        ├── pre-migrate_student.py
        └── post-fill_data.py
```

Odoo 19 မှာ `migrations/` အပြင် `upgrades/` folder ကိုလည်း support လုပ်ထားပါတယ်။

```text
school_management/
└── upgrades/
    └── 19.0.1.0.1/
        └── post-fix_data.py
```

ဒါပေမယ့် custom addons တွေမှာ အများဆုံးသုံးတာကတော့ `module_name/migrations/<version>/` ဖြစ်ပါတယ်။

Odoo က migration file names တွေမှာ အောက်ပါ prefix တွေနဲ့စတဲ့ Python files တွေကိုပဲ `pre-`, `post-`, `end-` စသည်ဖြင့် stage အလိုက် run ပါတယ်။

ဥပမာ -

```text
pre-migrate_student.py
post-fill_data.py
end-cleanup.py
```

Odoo မှာ `migrations/0.0.0/` ဆိုတဲ့ special migration folder တစ်ခုလည်း ရှိပါတယ်။ ဒီ folder ထဲက scripts တွေက version change ဖြစ်တိုင်း run နိုင်ပါတယ်။

## Pre Migration Script ဆိုတာ

`pre-` migration script ဆိုတာ target module ရဲ့ Python module ကို load မလုပ်ခင် run တဲ့ script ဖြစ်ပါတယ်။

အသုံးများတဲ့ use cases တွေကတော့ -

- Table အသစ် Create လုပ်ခြင်း
- Column Rename လုပ်ခြင်း
- Old Data ကို Temporary Table ထဲ Copy လုပ်ခြင်း
- Old Schema ရှိနေသေးတဲ့အချိန် Data Prepare လုပ်ခြင်း

ဥပမာ Version အဟောင်းမှာ `student_grade` ဆိုတဲ့ column ရှိပြီး Version အသစ်မှာ `grade_level` လို့ပြောင်းမယ်ဆိုပါစို့။

Pre migration ထဲမှာ old column data ကို ကြိုတင်ပြောင်းပေးနိုင်ပါတယ်။

```python
def migrate(cr, version):
    cr.execute("""
        ALTER TABLE res_partner
        RENAME COLUMN student_grade TO grade_level
    """)
```

`pre-` stage မှာ ORM model အသစ်တွေကို အပြည့်အဝ မှီခိုဖို့ မသင့်တော်သေးတဲ့အတွက် raw SQL ကိုသုံးတာ ပို safe ဖြစ်ပါတယ်။

## Production DB အတွက် သတိထားရန်

Production DB အတွက် migration script ရေးတဲ့အခါ အောက်ပါအချက်တွေကို လိုက်နာသင့်ပါတယ်။

- Backup အရင်ယူပါ
- Staging DB မှာအရင်စမ်းပါ
- Idempotent ဖြစ်အောင်ရေးပါ
- `table_exists` / `column_exists` checks ထည့်ပါ
- Duplicate data မဖြစ်အောင် `NOT EXISTS` သုံးပါ
- Pre stage မှာ raw SQL ကို ဦးစားပေးပါ
- Post stage မှာ ORM သုံးလို့ပိုသင့်တော်နိုင်ပါတယ်
- Script order အရေးကြီးရင် filename prefix ကို `01`, `02`, `03` စသဖြင့်ထည့်ပါ
- Migration run ပြီးသား file ကိုပြင်မယ့်အစား version အသစ်တိုးပြီး folder အသစ်ထည့်ပါ။

Odoo Migration ကို နားလည်ထားခြင်းက Production Database တွေကို data မပျောက်ဘဲ လုံခြုံစွာ Upgrade လုပ်နိုင်ဖို့ အရေးကြီးပါတယ်။

အများအားဖြင့် အောက်ပါအခြေအနေတွေမှာ Migration Scripts တွေက မရှိမဖြစ်လိုအပ်ပါတယ်။

- Model Structure ပြောင်းခြင်း
- Table အသစ်ထည့်ခြင်း
- Field Rename လုပ်ခြင်း
- Many2one မှ One2many ပြောင်းခြင်း
- Product-based Design မှ Master Table Design ပြောင်းခြင်း

အားလုံးအဆင်ပြေကြပါစေ။
