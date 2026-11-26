# Automation-mobile

פרויקט אוטומציה למובייל (Android) עם **Appium**, **Selenium** ו-**JUnit 5**, ב-Java ו-Maven.
הטסטים רצים על אמולטור Android ובודקים שתי אפליקציות מובנות: **Clock** ו-**Settings**.

## טכנולוגיות

| רכיב | גרסה |
|------|------|
| Java | 19 |
| Selenium Java | 4.23.0 |
| Appium Java Client | 9.3.0 |
| JUnit Jupiter (API + Engine) | 5.10.4 |
| Maven Surefire Plugin | 3.2.5 |
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
4. מוודא שבעמוד מופיע תוכן של About (למשל "Android version", "Build number" או "Model").

ה-enum `MainMenuItem` מרכז את שמות הפריטים בתפריט הראשי של ההגדרות.

## דרישות מקדימות

- JDK 19 ומעלה (כשמריצים `mvn` מהטרמינל, `JAVA_HOME` צריך להצביע על JDK 19 ומעלה. עם Java 17 תתקבל השגיאה `invalid target release: 19`)
- Maven
- Android SDK + אמולטור עם **Android 14**, שרץ בשם `emulator-5554` (אפשר לבדוק עם `adb devices`)
- Node.js ו-Appium עם הדרייבר UiAutomator2:
  ```bash
  npm install -g appium
  appium driver install uiautomator2
  ```

## הרצה

1. הפעילו את האמולטור.
2. הפעילו את שרת Appium (הטסטים מתחברים ל-`http://127.0.0.1:4723`, ברירת המחדל של Appium 2):
   ```bash
   appium
   ```
3. הריצו את הטסטים מה-IDE (IntelliJ: קליק ימני על מחלקת הטסט ← **Run**) או מהטרמינל:
   ```bash
   mvn test
   mvn test -Dtest=SettingsTest   # הרצת מחלקה אחת
   ```

## התאמות

ההגדרות של המכשיר כתובות ישירות בקוד, במתודות `@BeforeAll` של כל מחלקה (`InitOptions` / `setUp`):

- `setDeviceName` – שם המכשיר או האמולטור
- `setPlatformVersion` – גרסת Android
- כתובת שרת Appium – ב-`new URL(...)`

אם המכשיר שלכם שונה, צריך לעדכן את הערכים האלה.
