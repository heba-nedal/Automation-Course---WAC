# 05 — Automation Design

## 1. Automation Goal

بناء نظام يساعد فريق تنظيم الورش التقنية على معالجة تسجيلات المشاركين بصورة منظمة، بدءًا من استقبال التسجيل وحتى التحقق من البيانات وتحديد المسار المناسب وتسجيل النتائج وإرسال الإشعار المناسب.

## 2. Automation Flow

Google Forms  
→ Google Sheets  
→ AI by Zapier  
→ Validate Information  
→ Determine Missing Information  
→ Decision & Routing  
→ Update Google Sheets  
→ Send Email Notification

## 3. Step 1 — Receive Registration

**Tool:** Google Forms + Google Sheets

عندما يرسل المشارك نموذج التسجيل:

1. يستقبل النظام بيانات المشارك.
2. تُحفظ البيانات في Google Sheets.
3. يبدأ تدفق الأتمتة عند وصول تسجيل جديد.
4. يحتفظ النظام بمعرّف السجل الأصلي لاستخدامه عند تحديث نتائج المعالجة.

يجب ألا يؤدي تحديث التسجيل إلى إنشاء سجل جديد للتسجيل نفسه.

## 4. Step 2 — Summarize Learning Goals

**Tool:** AI by Zapier

يستقبل AI وصف أهداف التعلم `learning_goals`، إن توفر، ثم ينتج:

- `learning_goals_summary`

يجب أن يعتمد AI على المعلومات التي قدمها المشارك، وألا يخترع أهدافًا أو تفاصيل غير موجودة.

إذا لم يقدم المشارك وصف أهداف التعلم، فيُترك الملخص فارغًا أو تُستخدم قيمة واضحة مثل `Not provided`.

لا يتولى AI تحديد `missing_information` أو `validation_status` أو `requires_human` أو `routing`.

غياب وصف أهداف التعلم وحده لا يجعل التسجيل غير صالح؛ لأنه حقل اختياري.

## 5. Step 3 — Validate Information

**Tool:** Zapier Logic / Validation Rules

يتحقق النظام من الحقول المطلوبة:

- `participant_name`
- `email`
- `workshop_name`

تشمل عملية التحقق التأكد من وجود القيم المطلوبة، والتحقق من صيغة البريد الإلكتروني وفق آلية التحقق المتاحة في التنفيذ.

لا يُستخدم AI للتحقق من وجود هذه الحقول أو لاتخاذ القرار النهائي بشأن صلاحية التسجيل.

## 6. Step 4 — Determine Missing Information

**Tool:** Zapier Logic

بعد التحقق من البيانات، يحدد النظام قيمة `missing_information` بناءً على الحقول المطلوبة التي لم تستوفِ شروط التحقق.

مثال عند غياب البريد الإلكتروني:

`missing_information = email`

إذا كانت جميع الحقول المطلوبة موجودة:

`missing_information = NONE`

لا يُضاف `learning_goals` إلى `missing_information`؛ لأنه حقل اختياري.

إذا كانت المعلومات موجودة ولكنها غير واضحة بما يكفي لمتابعة التسجيل، يُسجل سبب المراجعة في `review_reason`، وتُطبق قواعد القرار المناسبة.

## 7. Step 5 — Decision & Routing

تُطبّق قواعد التحقق والتوجيه المحددة في `04-decision-and-exception-design.md`.

يوجد مساران:

### Path A — Normal

يُستخدم عندما تكون الحقول المطلوبة مكتملة وواضحة وتستوفي شروط التحقق.

النتيجة:

- `missing_information = NONE`
- `validation_status = VALID`
- `requires_human = false`
- `routing = normal`

### Path B — Human Review

يُستخدم عندما تكون هناك معلومات مطلوبة ناقصة أو غير واضحة، أو عندما يتعذر التحقق من استيفاء شروط المعالجة العادية بعد نجاح خطوات المعالجة.

النتيجة:

- `validation_status = NEEDS_REVIEW`
- `requires_human = true`
- `routing = human_review`

يجب أن تتطابق شروط المسارين، بحيث لا يُوجّه التسجيل نفسه إلى المسارين معًا.

## 8. Step 6 — Update the Record

**Tool:** Google Sheets

بعد تحديد المسار:

1. يحدد النظام سجل التسجيل الأصلي باستخدام `registration_id`.
2. يُحدّث السجل نفسه بنتائج تلخيص أهداف التعلم والتحقق.
3. تُحفظ قيمة `missing_information`.
4. تُحفظ قيمة `validation_status`.
5. تُحفظ قيمة `requires_human`.
6. تُحفظ قيمة `routing`.
7. يُسجل `review_reason` عند الحاجة إلى المراجعة.

يجب ألا يؤدي التحديث إلى إنشاء سجل مكرر للتسجيل نفسه.

## 9. Step 7 — Send Notification

**Tool:** Email by Zapier

### For Path A

إرسال رسالة إلى المشارك تؤكد استلام التسجيل.

يجب أن توضح الرسالة أن تأكيد الاستلام لا يعني قبول المشارك في الورشة أو ضمان توفر مقعد له.

### For Path B

إرسال إشعار إلى المنظم المسؤول يتضمن بيانات التسجيل وسبب الحاجة إلى المراجعة.

لا يُرسل تأكيد القبول للمشارك؛ لأن النظام لا يتخذ قرار قبول المشاركين في الورشة.

## 10. Technical Failures

إذا فشلت خطوة تقنية، مثل معالجة AI أو تحديث Google Sheets أو إرسال الإشعار، فلا ينبغي تسجيل هذا الفشل تلقائيًا على أنه `NEEDS_REVIEW`.

يجب التمييز بين:

- **Business Exception:** التسجيل يحتاج إلى مراجعة بسبب معلومات مطلوبة ناقصة أو غير واضحة.
- **Technical Error:** إحدى خطوات الأتمتة لم تُنفذ بنجاح.

عند حدوث فشل تقني، تتم مراجعة سجل التنفيذ في Zapier لتحديد الخطوة التي فشلت ومعالجة المشكلة.

## 11. Testing Requirements

يجب اختبار الأتمتة باستخدام الحالات التالية:

| Test Case | Expected Result |
|---|---|
| تسجيل مكتمل وواضح | `VALID` وتوجيه إلى `normal`، مع `missing_information = NONE`. |
| البريد الإلكتروني مفقود | `NEEDS_REVIEW` وتوجيه إلى `human_review`، مع تحديد `email` في `missing_information`. |
| معلومات مطلوبة غير واضحة | `NEEDS_REVIEW` وتوجيه إلى `human_review`، مع تسجيل سبب المراجعة. |
| أهداف التعلم غير موجودة | لا يُحال التسجيل إلى المراجعة لهذا السبب وحده. |
| فشل تقني في إحدى الخطوات | يُعامل كخطأ تقني، وليس كقرار تحقق من التسجيل. |

## Expected Outcome

تصميم تدفق أتمتة واضح باستخدام Zapier، يفصل بين تلخيص أهداف التعلم باستخدام AI والتحقق من البيانات باستخدام قواعد محددة، ثم يحدد المعلومات الناقصة ومسار التسجيل ويحدّث السجل ويرسل الإشعار المناسب قبل البدء في التنفيذ العملي.
