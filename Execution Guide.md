# 06 — Implementation Guide

## 01 — الصورة الكاملة

سنحوّل التصميم الذي أعددناه في الملفات السابقة إلى Automation عملية باستخدام Zapier.

```text
Participant
     ↓
Google Form
     ↓
New Form Response
     ↓
AI by Zapier
     ↓
learning_goals_summary
     ↓
Validation & Decision Logic
     ↓
missing_information
     ↓
       ┌──────────────────┐
       │                  │
     VALID           NEEDS_REVIEW
       │                  │
       ↓                  ↓
  Normal Path        Human Review
       │                  │
       └────────┬─────────┘
                ↓
         Google Sheets
                ↓
        Email Notification
```

نريد توزيع المسؤوليات بوضوح:

- **AI:** يفهم وصف أهداف التعلم ويلخّصه.
- **Automation / Logic:** تتحقق من الحقول المطلوبة، وتحدد `missing_information` وحالة التسجيل ومساره.
- **Human:** يراجع التسجيلات التي تحتاج إلى تدخل بشري.

## 02 — إنشاء Google Form

أنشئ Google Form باسم:

**Workshop Registration Form**

أضف الحقول التالية:

| Field | Type | Required |
|---|---|---|
| `participant_name` | Short answer | Yes |
| `email` | Email | Yes |
| `workshop_name` | Short answer | Yes |
| `learning_goals` | Paragraph | No |

### لماذا نحتاج إلى هذه الحقول؟

نحتاج إلى بيانات واضحة لتحديد اسم المشارك والبريد الإلكتروني والورشة التي يرغب في التسجيل فيها، بالإضافة إلى وصف اختياري لأهداف التعلم.

يمثل `learning_goals` البيانات غير المنظمة التي سيستخدمها AI لفهم أهداف المشارك وتلخيصها.

**مهم:** اجعل الحقول الثلاثة الأولى مطلوبة في النموذج، واترك `learning_goals` اختياريًا.

إذا منع Google Forms إرسال حقل مطلوب فارغًا، فهذا سلوك متوقع. سنختبر هذه الحالة في قواعد التحقق بصورة منفصلة إذا لزم الأمر.

## 03 — إنشاء Google Sheet

أنشئ Google Sheet باسم:

**Workshop Registration Records**

استخدم الأعمدة التالية:

```text
registration_id
participant_name
email
workshop_name
learning_goals
learning_goals_summary
missing_information
validation_status
requires_human
routing
review_reason
```

يجب الاحتفاظ ببيانات التسجيل الأصلية، وإضافة نتائج المعالجة إلى السجل نفسه.

يجب أن يستخدم Zapier معرّفًا ثابتًا للسجل الأصلي، مثل معرّف صف الاستجابة الذي يوفره التكامل، حتى لا ينشئ سجلًا جديدًا لكل مرحلة من مراحل المعالجة.

تُحدد قيمة `learning_goals_summary` من مخرجات AI، بينما تُحدد قيم `missing_information` و`validation_status` و`requires_human` و`routing` باستخدام قواعد التحقق في Zapier.

## 04 — إنشاء Zap

في Zapier، اختر:

**Create → New Zap**

سمِّ الـZap:

```text
AI Workshop Registration & Participant Request Router
```

سنضيف خطوات التنفيذ بالترتيب، ثم نختبر كل خطوة قبل الاعتماد عليها.

## 05 — Trigger

اختر:

**Google Forms**

ثم اختر الحدث الذي يستقبل استجابة جديدة، مثل:

**New Form Response**

قد يختلف الاسم قليلًا حسب الخيارات المتاحة في حساب Zapier.

اربط حساب Google واختر نموذج التسجيل الذي أنشأته.

بعد ذلك، نفّذ:

**Test Trigger**

يجب أن تظهر بيانات الاستجابة، مثل:

```text
participant_name
email
workshop_name
learning_goals
```

### Trigger vs Input

**Trigger** هو الحدث الذي يبدأ الـAutomation:

```text
New Workshop Registration
```

أما **Input** فهو البيانات التي وصلت مع الحدث.

إذًا:

`Trigger ≠ Input`

## 06 — AI by Zapier: Data In

أضف Action باسم:

**AI by Zapier**

أنشئ Input باسم:

```text
learning_goals_text
```

واجعل قيمته هي حقل `learning_goals` القادم من Google Forms.

نرسل وصف أهداف التعلم إلى AI لأنه نص غير منظم يحتاج إلى فهم وتلخيص.

لا نحتاج إلى إرسال البريد الإلكتروني أو اسم المشارك أو اسم الورشة إلى AI؛ لأن التحقق من هذه الحقول عملية حتمية تنفذها قواعد Zapier.

## 07 — AI Task: Prompt

استخدم الـPrompt التالي داخل AI by Zapier:

```text
Analyze the participant's learning goals and produce
a concise, structured summary.

Use ONLY information explicitly provided by the participant.
Never invent, assume, infer, estimate, or guess information.

### Input

learning_goals_text:
The participant's description of their learning goals.

### Extraction Rules

learning_goals_summary:
- Summarize the participant's stated learning goals
  in one concise sentence.
- Preserve the original meaning.
- Do not add goals or interests that the participant
  did not mention.
- If the input is empty or contains no meaningful
  learning goals, return exactly:
  Not provided

### Output

Return only this field:

learning_goals_summary
```

**ملاحظة:** هذا الـPrompt مسؤول عن تلخيص أهداف التعلم فقط. لا يُستخدم لتحديد صلاحية التسجيل أو الحقول الناقصة.

## 08 — AI Output

اضبط مخرجات AI لتكون:

| Output | Type |
|---|---|
| `learning_goals_summary` | Text |

### Test Input

استخدم النص التالي لاختبار AI:

```text
I want to learn Python programming and build
practical AI applications.
```

### Expected Output

```text
learning_goals_summary =
The participant wants to learn Python programming
and build practical AI applications.
```

### Test Optional Information

استخدم إدخالًا فارغًا:

```text
learning_goals_text = ""
```

النتيجة المتوقعة:

```text
learning_goals_summary = Not provided
```

يجب ألا يؤدي غياب أهداف التعلم وحده إلى `NEEDS_REVIEW`.

إذا لم ينتج AI المخرجات المتوقعة، راجع الـPrompt وحقول المخرجات قبل الانتقال إلى الخطوات التالية.

**مهم:** هذه الاختبارات تتحقق من مخرجات AI فقط، ولا تثبت وحدها أن التسجيل صالح.

## 09 — Validation & Decision Logic

بعد التأكد من مخرجات AI، أضف خطوة للتحقق من البيانات باستخدام قواعد واضحة في Zapier.

### Required Fields

تحقق من:

- وجود `participant_name`.
- وجود `email` وأن صيغته مقبولة وفق آلية التحقق المتاحة.
- وجود `workshop_name`.

لا تجعل وجود `learning_goals_summary` شرطًا لصلاحية التسجيل.

التحقق من صيغة البريد الإلكتروني لا يثبت أن البريد فعّال أو أن صاحبه يستطيع استلام الرسائل.

### Determine Missing Information

يحدد Zapier قيمة `missing_information` بناءً على الحقول المطلوبة التي لم تستوفِ شروط التحقق.

إذا كان البريد الإلكتروني مفقودًا:

```text
missing_information = email
```

إذا كان اسم المشارك والبريد الإلكتروني مفقودين:

```text
missing_information = participant_name, email
```

إذا كانت جميع الحقول المطلوبة موجودة:

```text
missing_information = NONE
```

لا تضف `learning_goals` إلى `missing_information`؛ لأنه حقل اختياري.

### Rule A — VALID

إذا كانت الحقول المطلوبة مكتملة وتستوفي شروط التحقق:

```text
missing_information = NONE
validation_status = VALID
requires_human = false
routing = normal
```

### Rule B — NEEDS_REVIEW

إذا كان أحد الحقول المطلوبة ناقصًا أو غير مستوفٍ لشروط التحقق:

```text
validation_status = NEEDS_REVIEW
requires_human = true
routing = human_review
```

إذا كان سبب المراجعة هو نقص حقل، تُسجل أسماء الحقول الناقصة في `missing_information`.

أما إذا كانت هناك مشكلة أخرى تستدعي المراجعة، فيُسجل السبب في `review_reason`.

يجب تنفيذ القواعد باستخدام منطق واضح وقابل للاختبار، بدل الاعتماد على نص حر من AI لتحديد المسار.

## 10 — Paths by Zapier

بعد التحقق من البيانات، أنشئ مسارين باستخدام **Paths by Zapier** إذا كان ذلك متاحًا في إعداد الـZap.

```text
VALID
   ↓
Normal Path

NEEDS_REVIEW
   ↓
Human Review Path
```

### Path A — Normal Path

الشرط:

```text
validation_status
Exactly matches
VALID
```

ينتقل التسجيل إلى المسار العادي.

### Path B — Human Review

الشرط:

```text
validation_status
Exactly matches
NEEDS_REVIEW
```

ينتقل التسجيل إلى مسار المراجعة البشرية.

يجب أن تكون شروط المسارين متبادلة الاستبعاد، بحيث لا يُوجّه التسجيل نفسه إلى المسارين معًا.

لا تستخدم مسار المراجعة لمجرد غياب `learning_goals`.

## 11 — Update Google Sheets

أضف خطوة لتحديث سجل التسجيل في Google Sheets.

استخدم معرّف السجل الأصلي لتحديد الصف الذي ستُحدّثه.

حدّث الحقول التالية:

```text
learning_goals_summary
missing_information
validation_status
requires_human
routing
review_reason
```

### لماذا نحدّث السجل الأصلي؟

لأن كل تسجيل يجب أن يحتفظ بسجل واحد يحتوي على بياناته الأصلية ونتائج معالجته.

لا نريد إنشاء صف جديد عند كل مرحلة من مراحل الـAutomation.

إذا تعذر تحديد السجل الأصلي أو تحديثه، يجب التعامل مع ذلك باعتباره خطأ تقنيًا ومراجعته في سجل تنفيذ Zapier.

## 12 — Email Notification

### Path A — Normal Path

أضف خطوة إرسال بريد إلكتروني إلى المشارك.

**Subject:**

```text
Workshop Registration Received
```

**Email Body:**

```text
Hello {{participant_name}},

We have received your registration for
{{workshop_name}}.

Your registration details have been recorded.
This email confirms receipt only and does not
guarantee acceptance or a seat in the workshop.

Thank you.
Workshop Team
```

اربط المتغيرات ببيانات التسجيل الفعلية.

### Path B — Human Review

أضف خطوة إرسال إشعار إلى المنظم المسؤول.

**Subject:**

```text
Workshop Registration Requires Review
```

**Email Body:**

```text
A workshop registration requires human review.

Participant: {{participant_name}}
Email: {{email}}
Workshop: {{workshop_name}}

Missing Information:
{{missing_information}}

Review Reason:
{{review_reason}}

Please review the registration and determine
the appropriate next action.
```

يجب أن يحتوي الإشعار على المعلومات اللازمة لمراجعة التسجيل، دون الادعاء بأن المشارك قد قُبل أو رُفض.

## 13 — Testing the Complete Automation

اختبر الـAutomation باستخدام حالات مختلفة.

| Test | Scenario | Expected Status | Expected Path | Expected `missing_information` |
|---|---|---|---|---|
| 01 | تسجيل مكتمل وصحيح | `VALID` | Normal Path | `NONE` |
| 02 | البريد الإلكتروني غير موجود | `NEEDS_REVIEW` | Human Review | `email` |
| 03 | حقل مطلوب آخر غير موجود | `NEEDS_REVIEW` | Human Review | اسم الحقل الناقص |
| 04 | أهداف التعلم غير موجودة | `VALID` إذا اكتملت بقية الحقول | Normal Path | `NONE` |
| 05 | فشل تقني أثناء التنفيذ | Technical Error | Debugging | لا يُعامل كقرار تحقق |

### طريقة الاختبار

لكل حالة:

1. أدخل بيانات الاختبار.
2. أرسل النموذج إذا كان يسمح بالحالة المطلوبة.
3. راقب نتيجة Trigger.
4. راجع مخرجات AI.
5. تحقق من نتيجة قواعد التحقق.
6. راقب المسار الذي تم اختياره.
7. تحقق من تحديث Google Sheets.
8. تحقق من إرسال البريد المناسب.
9. قارن النتيجة الفعلية بالنتيجة المتوقعة.

```text
Expected Result
       VS
Actual Result
```

إذا تطابقت النتيجتان:

```text
PASS
```

إذا اختلفتا:

```text
FAIL → Debug → Retest
```

لا تسجل أي اختبار على أنه `PASS` قبل تنفيذه فعليًا.

### ملاحظة حول اختبار الحقول المطلوبة الناقصة

إذا كان Google Forms يمنع إرسال الحقول المطلوبة فارغة، فلن نستطيع إنشاء هذه الحالة من خلال النموذج نفسه.

في هذه الحالة، اختبر قواعد التحقق بصورة منفصلة باستخدام بيانات اختبار تسمح بتمرير قيمة فارغة إلى خطوة التحقق، أو باستخدام وسيلة اختبار مناسبة في Zapier.

يجب توثيق الاختبار الذي نُفّذ فعلًا، وعدم الادعاء بأن النموذج استقبل تسجيلًا ناقصًا إذا كان قد منعه.

## 14 — Exception vs Technical Error

### Business Exception

مثال:

```text
email = missing
```

هذا يعني أن بيانات التسجيل لا تستوفي شروط المعالجة العادية، ولذلك يحتاج التسجيل إلى مراجعة بشرية.

### Technical Error

أمثلة:

```text
AI Action Failed
Google Sheets Update Failed
Email Action Failed
```

هذه أخطاء في تنفيذ النظام، وليست نتائج تحقق من التسجيل.

عند حدوث خطأ تقني:

1. افتح سجل تنفيذ الـZap.
2. حدد أول خطوة فشلت.
3. راجع بيانات الإدخال والإعدادات.
4. أصلح المشكلة.
5. أعد الاختبار للتأكد من نجاح الخطوة.

لا تعتبر فشل إرسال البريد دليلًا على أن التسجيل يحتاج إلى مراجعة بشرية، ولا تعتبر فشل تحديث Google Sheets نتيجة تحقق من بيانات المشارك.

## 15 — Definition of Done

يُعتبر التنفيذ مكتملًا عندما:

- [ ] Google Form يعمل ويستقبل التسجيلات.
- [ ] Trigger يستقبل الاستجابات الجديدة.
- [ ] AI ينتج ملخصًا صحيحًا لأهداف التعلم.
- [ ] غياب أهداف التعلم لا يؤدي وحده إلى المراجعة.
- [ ] قواعد التحقق تحدد `missing_information` بصورة صحيحة.
- [ ] قواعد التحقق تحدد `VALID` و`NEEDS_REVIEW` بصورة صحيحة.
- [ ] المسار العادي يعمل.
- [ ] مسار المراجعة البشرية يعمل.
- [ ] يتم تحديث سجل التسجيل الأصلي.
- [ ] يتم إرسال تأكيد استلام التسجيل في المسار العادي.
- [ ] يتم إرسال إشعار المنظم عند الحاجة إلى المراجعة.
- [ ] تم اختبار الحالات المحددة وتوثيق النتائج الفعلية.
- [ ] تم توثيق أي أخطاء تقنية ظهرت أثناء الاختبار.

## Expected Outcome

بناء واختبار نظام أتمتة عملي لتسجيل المشاركين في الورش التقنية، مع استخدام AI لتلخيص أهداف التعلم، وقواعد واضحة للتحقق من البيانات وتحديد المعلومات الناقصة والتوجيه، ومراجعة بشرية للحالات التي تحتاج إلى تدخل، وتوثيق نتائج التنفيذ والاختبار.
