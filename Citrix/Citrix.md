## Concept
- عبارة عن virtualization solution بيخلي ال end user يستخدم ال app او ال desktop بتاع مهما كان ال OS اللي عنده عن طريق VDI بحيث ان الداتا وال app متخزنين على remote server وان ال user بيظهرله حاجته ك interface بس.
![[Pasted image 20260726150425.png]]
## Delivery Controller (DC)
- ده ال core management component on site مسئول عن ال authentication and authorization لما user يجي يطلب VDI مثلا وكمان هو بيعمل load balance بحيث يشوف هيجيب ال VDI من انهي server وهو connection broker بيكلم ال hypervisor يقوله اعمل VM للuser ده يدخل عليها وبي monitor كل ال sessions ويسجل في ال site database
## Database
- كل Site لازم تحتوي على Microsoft SQL Server Database واحدة على الأقل.
- الـ Site Database تخزن :-
	- ال configuration data زي ال delivery group, machine catalog, apps, storefront, policies
	- ال Session Information -> مين دخل على ايه امتى
- كل خدمات الـ Delivery Controller (مثل Broker Service وMonitor Service) تقرأ وتكتب بياناتها في قاعدة البيانات.
- يجب أن تكون قاعدة البيانات داخل الـ Data Center وباتصال دائم مع الـ Delivery Controller.
- يوجد عادةً ثلاث قواعد بيانات:
	- ال site database
	- ال  Configuration Logging Database: تسجل جميع التغييرات التي يجريها الـ Admin.
	- ال Monitoring Database: تخزن بيانات الأداء والإحصائيات والتقارير.
## Virtual Delivery Agent (VDA)
- يُثبت على كل Machine (Physical أو Virtual) ستقدم Applications أو Desktops للمستخدمين.
- يسجل الـ Machine عند الـ Delivery Controller (Registration).
- يدير الاتصال بين المستخدم والـ Desktop أو الـ Application.
- يطبق سياسات Citrix (Policies) على الـ Session.
- يتحقق من وجود Citrix License متاحة قبل بدء الجلسة.
- يحتوي على Broker Agent الذي يتواصل مع الـ Broker Service في الـ Delivery Controller عبر TCP Port 80.
- يرسل معلومات الـ Session وحالة الـ Machine إلى الـ Controller.
- نوعان رئيسيان:
  - Single-session VDA: لمستخدم واحد لكل Desktop (Windows 10/11).
  - Multi-session VDA: لعدة مستخدمين على نفس Windows Server.
- يوجد أيضًا Linux VDA لأنظمة Linux.
## StoreFront

- بوابة الدخول لبيئة Citrix.
- يعمل Authentication للمستخدمين (غالبًا بالاعتماد على Active Directory).
- يعرض التطبيقات والـ Desktops المسموح بها لكل مستخدم.
- يوفر Self-Service بحيث يختار المستخدم ما يريد تشغيله.
- يحتفظ بالـ Favorites والـ Subscriptions لتقديم نفس التجربة على جميع الأجهزة.
## Citrix Workspace App

- برنامج يُثبت على جهاز المستخدم (Client).
- يستخدم للوصول إلى التطبيقات والـ Virtual Desktops.
- يتصل أولًا بـ StoreFront للحصول على الموارد المتاحة.
## Citrix Director

- أداة Web-based للـ Monitoring وTroubleshooting.
- يستخدمها الـ Help Desk والـ Citrix Admin.
- تعرض بيانات Real-time من Broker Service.
- تعرض بيانات Historical من Monitor Service.
- تجمع وتحلل بيانات الأداء من Citrix Gateway.
- تسمح للـ Admin بمشاهدة جلسة المستخدم وتقديم Remote Assistance.
- يمكنها مراقبة أكثر من Citrix Site من واجهة واحدة.
## Citrix Studio

- أداة الإدارة الرئيسية (Administration Console) في Citrix.
- يستخدمها Citrix Administrator لإدارة البيئة بالكامل.
- من خلالها يمكن:
  - إنشاء Machine Catalogs.
  - إنشاء Delivery Groups.
  - نشر Applications وDesktops.
  - إدارة الـ VDAs.
  - إدارة الـ Hypervisor Connections.
  - إنشاء وتعديل Policies.
  - إدارة المستخدمين والصلاحيات.
- لا تُستخدم لمراقبة الأداء أو Troubleshooting، فهذه مهمة Citrix Director.