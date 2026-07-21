### Video 2
- قبل ال virtualization كان كل سيرفر لوحده بيشيل app واحد او service واحدة وبالتالي عندي هدر كبير في ال resources 
- ![[Pasted image 20260714150436.png]]

- كل VM شايله service فلو في service عايزه update مبيحصلش downtime للباقيين
- ال virtualization بيدينا مزايا زي ال high availability ان عندي نسخة لل VM على host تاني تشتغل لو الhost الاول وقع
- عندي برضو ميزة ال migration لو في load على host معين .. طبعا كانت شبه مستحيلة في ال physical environment
- فكرة ال virtualization قايمة على ال overutilization .. بمعنى اني بعمل VMs بإمكانيات أكبر من ال physical resources عشان اكسب اكتر .. لان اكيد ال VMs دي مش هتستهلك كل ال resources بشكل كامل في نفس الوقت .. وطبعا كل ده له حدود .. يعني ممكن يكون عندي 100GB RAM وعامل VMs l مجموع الRAM بتاعتهم 120GB
---
### Video 3
##### Desktop virtualization
- فكرة ال Desktop virtualization او VDI او App virtualization ان الAPP + Data بيكونوا على physical host في ال data center والclient بياخد access عن طريق thin client بامكانيات قليلة جدا ويفتح session بال username and password بتوعه .. فانا هنا وفرت تكلفة وكمان الموضوع secure اكتر لان الAPP +Data عندي مش عند ال client وده مشهور جدا ف البنوك
- كمان ميزة بيقدمها ان لو عندي update ل OS مثلا .. لو شغال على قديمو هضطر اجيب اعمل الupdate ب WSUS مثلا .. لكن مع وجود ال Desktop virtualization انا مجرد هعمل template او احدث ال template اللي هي golden image بمصطلح الشغل واي VM هتتعمل منها هتبقى updated
- الproduct بتاع VMware المسئول عن ال Desktop virtualization هو Horizon
##### Storage virtualization
- زمان كان الوضع عبارة عن اننا بنشتغل على ال local disks بتاعت ال hosts بس طبعا مساحتها صغيرة وبطيئة فروحنا لفكرة ال shared storage زي ال SAN و NAS عشان نستفيد بسرعات عالية ومساحات اكبر وكمان ال shared storage بتوفرلي مزايا مهمة زي ال high availability ان ال VM لو حصل مشكلة فيها في نسخة تانية على host تاني شايف نفس ال datastore بس طبعا كل ده غالي شوية بس معلش :)
- جه بقا فكرة ال storage virtualization من خلال فكرة ال HCI اننا نجمع كل ال local hard disks كpool of storage وسميناها vSAN .. طبعا في شروط من VMware عشان نعمل vSAN زي ان لازم ع الاقل 3 hosts وان لازم ع الاقل كل واحد فيهم يكون عنده واحد SSD عشان حتة ال cache tier
- الproduct بتاع VMware المسئول عن ال Storage virtualization هو vSAN
---
