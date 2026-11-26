# Automation-mobile

פרויקט אוטומציה למובייל (Android) עם **Appium**, **Selenium** ו-**JUnit 5**, ב-Java ו-Maven.
הטסטים רצים על אמולטור Android ובודקים שתי אפליקציות מובנות: **Clock** ו-**Settings**.

## טכנולוגיות

| רכיב | גרסה |
|------|------|
| Java | 19 |
| Selenium Java | 4.23.0 |
| Appium Java Client | 9.3.0 |
| JUnit Jupiter API | 5.10.4 |
| Automation engine | UiAutomator2 |

## מבנה הפרויקט

```
Automation-mobile/
├── pom.xml
└── src/
    ├── main/java/org/example/Main.java   # מחלקה ריקה (שלד של Maven)
    └── test/java/
        ├── AppiumTest.java               # טסט טיימר באפליקציית השעון
        └── SettingsTest.java             # טסט ניווט באפליקציית ההגדרות
```

## הטסטים

### `AppiumTest` – אפליקציית השעון (`com.google.android.deskclock`)

הטסט `Timer`:
1. מוודא שהאפליקציה עלתה.
2. עובר ללשונית **Timer** (ולוחץ על **Add timer** אם הכפתור קיים).
3. מקליד `0100` (דקה אחת) וקורא את תצוגת הזמן.
4. לוחץ **Start**, ממתין 30 שניות ובודק שהזמן המוצג השתנה.

האלמנטים מוגדרים עם `@AndroidFindBy` (PageFactory), ולכל אלמנט יש כמה locators חלופיים (טקסט באנגלית/עברית, accessibility id, resource-id), כך שהטסט עובד גם כשהמכשיר בשפה עברית.

### `SettingsTest` – אפליקציית ההגדרות (`com.android.settings`)

הטסט `openAboutEmulatedDeviceAndVerifyPage`:
1. מוודא שמסך ההגדרות עלה.
2. גולל (באמצעות `UiScrollable`) עד **About emulated device** ולוחץ עליו.
3. מוודא שהתבצע ניווט – לפי שינוי ה-Activity, הופעת כפתור **Navigate up**, או היעלמות שורת החיפוש.

ה-enum `MainMenuItem` מרכז את שמות הפריטים בתפריט הראשי של ההגדרות.

## דרישות מקדימות

- JDK 19 ומעלה
- Maven
- Android SDK + אמולטור עם **Android 14**, שרץ בשם `emulator-5554` (אפשר לבדוק עם `adb devices`)
- Node.js ו-Appium עם הדרייבר UiAutomator2:
  ```bash
  npm install -g appium
  appium driver install uiautomator2
  ```

## הרצה

1. הפעילו את האמולטור.
2. הפעילו את שרת Appium. הטסטים מתחברים ל-`http://127.0.0.1:4723/wd/hub`, ולכן ב-Appium 2 ומעלה צריך להגדיר base path:
   ```bash
   appium --base-path /wd/hub
   ```
3. הריצו את הטסטים מה-IDE (IntelliJ: קליק ימני על מחלקת הטסט ← **Run**).

> **הערה:** ב-`pom.xml` מוגדר רק `junit-jupiter-api`, בלי `junit-jupiter-engine` ובלי `maven-surefire-plugin`, ולכן `mvn test` עלול לא למצוא את הטסטים. כדי להריץ מה-command line, הוסיפו את ה-dependency של `junit-jupiter-engine` (scope `test`) ואת `maven-surefire-plugin` בגרסה 3.x, ואז:
> ```bash
> mvn test
> mvn test -Dtest=SettingsTest   # הרצת מחלקה אחת
> ```

## התאמות

ההגדרות של המכשיר כתובות ישירות בקוד, במתודות `@BeforeAll` של כל מחלקה (`InitOptions` / `setUp`):

- `setDeviceName` – שם המכשיר או האמולטור
- `setPlatformVersion` – גרסת Android
- כתובת שרת Appium – ב-`new URL(...)`

אם המכשיר שלכם שונה, צריך לעדכן את הערכים האלה.
