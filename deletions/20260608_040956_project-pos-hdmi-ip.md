---
name: project-pos-hdmi-ip-stream
description: פרויקט חיבור מסך POS לרשת IP — הצגת המסך כמצלמת אבטחה ב-NVR דרך ממיר HDMI-to-IP
metadata: 
  node_type: memory
  type: project
  originSessionId: 025d930d-452c-4daf-9609-07d8a8931e3d
---

# פרויקט: הזרמת מסך קופת POS לרשת IP כמצלמת אבטחה

## מטרה
חיבור מסך קופת POS לרשת IP באמצעות ממיר HDMI-to-IP, והצגת תמונת המסך כחלק ממערכת מצלמות האבטחה ב-NVR.

**Why:** לצפייה בפעילות הקופה דרך מערכת הריגול הקיימת, ללא התקנת מצלמה נוספת.  
**How to apply:** לפתוח זיכרון זה בתחילת כל שיחה הקשורה לפרויקט לפני כל פעולה.

---

## טופולוגיית חיבור
```
POS (DP out)
    ↓
DP-to-HDMI adapter
    ↓
HDMI-to-IP Encoder
    ↓ (LAN)
NVR / צופה ברשת
```

---

## פרטי מכשירים

### קופת POS
- מערכת הפעלה: **Windows 10 Pro**
- יציאה יחידה למסך: **DisplayPort (DP)**
- **מפרט מחשב מלא**: ממתין לתמונה מהמשתמש ⏳

### ממיר HDMI-to-IP
- **מפרט / דגם**: ממתין לקובץ הוראות הפעלה ⏳
- גישה דרך ממשק ווב ברשת המקומית

### NVR
- **פרטים**: לא התקבלו עדיין ⏳

### אביזרים זמינים
- ✅ מתאם DP to HDMI
- ✅ כבלים

---

## גישה מרחוק לרשת החנות

### מצב נוכחי
- לא ברור אם יש VPN או כלי גישה מרחוק מותקן בחנות
- **לבדוק עם המשתמש**: AnyDesk / TeamViewer / Chrome Remote Desktop / RDP

### תכנון השיחה הבאה
1. פתיחת דפדפן (Playwright)
2. כניסה לכלי גישה מרחוק
3. המשתמש מאשר חיבור בחנות
4. גישה לממשק הממיר ול-NVR דרך הרשת המקומית של החנות

---

## בעיות צפויות ופתרונות (לפי תרחיש)

### תרחיש A — ניתוק תמונה לאחר זמן (Windows 10 Power Management)
**סיבות:**
- חסכון בחשמל כיבה את המסך
- Screen Saver פעיל
- Windows Update הפעיל מחדש

**פתרונות:**
```powershell
# כיבוי שינה ומסך — חשמל:
powercfg /change standby-timeout-ac 0
powercfg /change monitor-timeout-ac 0
powercfg /change hibernate-timeout-ac 0
# בחירת High Performance:
powercfg /setactive SCHEME_MIN
```
- כיבוי Screen Saver דרך Settings
- עדכוני Windows: דחיית עדכונים אוטומטיים

---

### תרחיש B — HDCP חוסם לכידת תמונה
**הסבר:** HDMI Copy Protection — חלק מהממירים לא יציגו תמונה מ-HDCP source.
**בדיקה:** האם הממיר תומך ב-HDCP bypass / stripping?
**פתרון חלופי:** שימוש ב-DP Splitter שמסיר HDCP לפני הממיר (ציוד חיצוני).

---

### תרחיש C — POS צריך גם מסך וגם ממיר בו-זמנית
**בעיה:** יציאה אחת בלבד (DP) — לא ניתן לחבר שני מכשירים ישירות.
**פתרונות:**
1. **DP Splitter (מפצל)** → מסך + ממיר בו-זמנית
2. **ממיר עם HDMI Loop-Out** → קלט HDMI + יציאת HDMI למסך + זרם לרשת
3. בדיקה אם ממיר ה-HDMI-to-IP שנרכש כולל loop-out ⏳

---

### תרחיש D — NVR לא מזהה את הממיר כמצלמה
**בעיה:** NVR מצפה ל-ONVIF camera, לא RTSP stream כללי.
**פתרונות:**
1. בדיקה אם הממיר תומך ONVIF ⏳
2. הגדרת ה-NVR להוספה ידנית של RTSP stream
3. שימוש בתוכנת ביניים (iSpy / Blue Iris) שמזהה stream ומציגה ל-NVR

---

### תרחיש E — בעיות ברזולוציה / frame rate
**פתרון:** כוונון ידני ב-Windows Display Settings + ממשק הממיר.

---

## שלבי העבודה המתוכננים (לא להתחיל ביישום)

| # | שלב | מצב |
|---|-----|-----|
| 1 | קבלת פרטי הממיר (הוראות + דגם) | ⏳ |
| 2 | קבלת פרטי מחשב ה-POS (תמונה) | ⏳ |
| 3 | בדיקה: יש loop-out לממיר? | ⏳ |
| 4 | בדיקה: יש VPN/גישה מרחוק לחנות? | ⏳ |
| 5 | חיבור פיזי: POS → DP-to-HDMI → ממיר | לא מתחילים |
| 6 | גישה לממשק ווב של הממיר | לא מתחילים |
| 7 | הגדרת IP / RTSP ב-ממיר | לא מתחילים |
| 8 | הוספת ה-stream ל-NVR | לא מתחילים |
| 9 | הגדרות Windows 10 Power Management | לא מתחילים |
| 10 | בדיקות יציבות | לא מתחילים |

---

## ממצאי מחקר

> יתמלא בהדרגה

### HDMI-to-IP Encoders — עקרונות כלליים
- רוב הממירים בשוק מייצרים RTSP stream ב-H.264/H.265
- גישה לממשק: IP address + HTTP port (לרוב 80 או 8080)
- פרוטוקולים נפוצים: RTSP, HTTP-FLV, HLS, ONVIF
- דגמים נפוצים: Kiloview, Magewell, Lenkeng, AVMATRIX

### Windows 10 Pro — הגדרות רלוונטיות
- Display Settings → Multiple Displays: Duplicate / Extend / Second screen only
- Power & Sleep → כיבוי מסך
- Power Plans: Balanced (ברירת מחדל) / High Performance / Power Saver
- Group Policy: גלישה דרך `gpedit.msc` לנעילת הגדרות

---

## קישורים שיתווספו
> ייתווספו בהמשך המחקר

---

## מצב כללי
- שלב: **מחקר והכנה** — לא מתחילים ביישום
- תאריך עדכון אחרון: 2026-06-08
