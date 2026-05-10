# 🐳 Docker + Project Workflow (Quick Reference)

## 🧠 الفكرة الأساسية

أي مشروع بيعدي بـ 3 مراحل:

1. **Install Dependencies**
    
    - تنزيل المكتبات
        
    - أمثلة:
        
        - Node: `npm install`
            
        - Python: `pip install`
            
        - Java: `mvn install`
            
2. **Build (اختياري)**
    
    - تجهيز المشروع (compile / bundle)
        
    - أمثلة:
        
        - Node: `npm run build`
            
        - Java: `mvn package`
            
3. **Run**
    
    - تشغيل المشروع
        
    - أمثلة:
        
        - Node: `npm run dev` أو `npm start`
            
        - Python: `python app.py`
            
        - Java: `java -jar app.jar`
            

---

## 🐳 Docker بيعمل إيه؟

Docker بيحول الـ 3 مراحل دول لصورة (Image):

- `RUN` → للـ Install & Build (وقت بناء الصورة)
    
- `CMD` → للـ Run (وقت تشغيل الكونتينر)
    

---

## 🔁 Mapping سريع

|المرحلة|Node|Python|Java|Docker|
|---|---|---|---|---|
|Install|npm install|pip install|mvn install|RUN|
|Build|npm run build|-|mvn package|RUN|
|Run|npm start|python app.py|java -jar|CMD|

---

## ⚠️ أخطاء شائعة

❌ استخدام:

```
npm run dev
```

داخل Docker

✔️ لأنه:

- Development only
    
- بطيء
    
- فيه hot reload
    

---

## ✅ الأفضل في Docker

```
npm run build
npm start
```

---

## 💡 قاعدة مهمة

> Dockerfile = Build مرة واحدة + Run وقت التشغيل

---

## 🧠 خلاصة

- Docker بيطبق نفس workflow أي مشروع
    
- الفرق بس إنك بتكتبه في Dockerfile
    
- افهم المراحل (Install → Build → Run) وهتفهم أي مشروع بسهولة