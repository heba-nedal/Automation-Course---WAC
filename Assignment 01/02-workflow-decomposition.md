# 02 — Workflow Decomposition

## 1. Process

**Workshop Registration Intake & Routing Process**

هي عملية استقبال تسجيلات المشاركين في الورش التقنية، وفهم أهدافهم، وتنظيم بياناتهم، والتحقق منها، ثم تحديد الإجراء المناسب لكل تسجيل.

## 2. Workflow

يتكون سير العمل من الخطوات التالية:

1. Receive Registration
2. Understand Participant Goals
3. Structure Information
4. Validate Registration
5. Route Registration
6. Record Results
7. Notify Relevant Person

## 3. Tasks

| Task | Description |
|---|---|
| Receive Registration | استقبال تسجيل المشارك وتسجيل بياناته. |
| Understand Participant Goals | فهم أهداف المشارك من حضور الورشة. |
| Structure Information | استخراج المعلومات ذات الصلة وتنظيمها. |
| Validate Registration | التحقق من اكتمال المعلومات المطلوبة ووضوحها. |
| Route Registration | تحديد المسار المناسب وفق قواعد محددة. |
| Record Results | تسجيل نتائج معالجة التسجيل. |
| Notify Relevant Person | إرسال الإشعار المناسب وفق نتيجة المعالجة. |

## 4. Subtasks

### Task 1: Receive Registration

- استقبال بيانات المشارك من Google Forms.
- تسجيل بيانات التسجيل في Google Sheets.
- الاحتفاظ بمعرّف السجل الأصلي لاستخدامه عند تحديث النتائج.

### Task 2: Understand Participant Goals

- قراءة وصف المشارك لأهدافه من حضور الورشة، إن توفر.
- فهم المعلومات التي قدمها المشارك.
- تلخيص أهداف التعلم باستخدام AI by Zapier.
- عدم افتراض أهداف لم يذكرها المشارك.

### Task 3: Structure Information

- تنظيم بيانات المشارك في حقول واضحة.
- استخراج ملخص أهداف التعلم إن توفرت معلومات كافية.
- تحديد المعلومات المطلوبة الناقصة.
- الاحتفاظ ببيانات التسجيل الأصلية دون استبدالها بالمخرجات المستخرجة.

### Task 4: Validate Registration

- التحقق من وجود الحقول المطلوبة.
- التحقق من صلاحية البريد الإلكتروني وفق قواعد التحقق المحددة.
- التحقق من وضوح المعلومات المطلوبة للمتابعة.
- عدم اعتبار غياب وصف أهداف التعلم الاختياري سببًا لرفض التسجيل.
- تحديد ما إذا كان التسجيل جاهزًا للمتابعة أو يحتاج إلى مراجعة بشرية.

### Task 5: Route Registration

- توجيه التسجيل المستوفي لشروط التحقق إلى المسار العادي.
- توجيه التسجيل الذي يحتوي على معلومات مطلوبة ناقصة أو غير واضحة إلى المراجعة البشرية.
- تطبيق قواعد التوجيه المحددة في `04-decision-and-exception-design.md`.

### Task 6: Record Results

- تحديث سجل التسجيل الأصلي في Google Sheets.
- حفظ نتائج استخراج المعلومات.
- حفظ حالة التحقق والمسار المحدد.
- تسجيل سبب المراجعة عند الحاجة إليها.

### Task 7: Notify Relevant Person

- إرسال تأكيد للمشارك باستلام التسجيل في المسار العادي.
- إرسال إشعار إلى المنظم عندما يحتاج التسجيل إلى مراجعة بشرية.
- عدم اعتبار تأكيد الاستلام قبولًا للمشارك في الورشة.

## 5. Sequence

يجب تنفيذ المهام بالترتيب التالي:

Receive Registration  
→ Understand Participant Goals  
→ Structure Information  
→ Validate Registration  
→ Route Registration  
→ Record Results  
→ Notify Relevant Person

لا يمكن تحديد المسار المناسب قبل توفر المعلومات اللازمة والتحقق من الحقول المطلوبة.

## 6. Dependencies

| Task | Depends On |
|---|---|
| Understand Participant Goals | Receive Registration |
| Structure Information | Understand Participant Goals |
| Validate Registration | Structure Information |
| Route Registration | Validate Registration |
| Record Results | Route Registration |
| Notify Relevant Person | Record Results |

## Expected Outcome

تحديد خطوات معالجة تسجيلات المشاركين، والمهام الفرعية لكل خطوة، وترتيب التنفيذ والاعتماديات بينها قبل تحويل سير العمل إلى نظام أتمتة باستخدام Zapier.
