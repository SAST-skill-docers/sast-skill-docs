# 一些 Android 的实用组件

下面将会介绍一些可能不是最核心，但非常实用的组件。

## 长期储存及数据库

制作一个应用程序，离不开数据的存储。对少量数据和大量结构化的数据，我们有不同的存储方式。

这里推荐对少量数据使用 Android 内置的 SharedPreferences 类进行存储。SharedPreferences 使用键值对的形式来存储数据，每个键都必须是唯一的。可以使用字符串作为键来存储各种数据类型的值，如整数、浮点数、布尔值、字符串等。注意，它直接使用 .xml 文件存储所有内容，因此**请仅用于存储少量数据**。下面展示一个使用该类的 `onCreate()` 方法。

```java
   protected void onCreate(Bundle savedInstanceState) {

       super.onCreate(savedInstanceState);
       setContentView(R.layout.activity_main);
       SharedPreferences preferences = getPreferences(MODE_PRIVATE);
       SharedPreferences.Editor editor = preferences.edit();
       
       Bundle bundle = this.getIntent().getExtras(); // load intent
       String intentUserName = bundle.getString("user_name");
       if(intentUserName != null && !intentUserName.equals("null")){
           editor.putString("user_name", intentUserName);
           editor.apply();
       }
       else {
           userName = preferences.getString("user_name", null);
           if (userName == null || userName.equals("null")){
               userName = "Clancy";
	           editor.putString("user_name", userName);
               editor.apply();
           }
       }
   }
```

大量的结构化数据应该使用数据库存储。如果你会使用 SQL 语法，可以使用 SQLite 保存应用程序的数据。一个数据库是 `SQLiteDatabase ` 类的一个对象。你可以使用 `db.execSQL(SQL_OPERATION);` 这样的语句来进行你想要的数据库操作，也可以用一些更为封装化的 API 接口，如 `insert()`, `delete()`, `query()`, `update()`。

```java
import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import android.content.ContentValues;
import android.database.Cursor;

public class DatabaseHelper extends SQLiteOpenHelper {
    private static final String DATABASE_NAME = "mydatabase.db";
    private static final int DATABASE_VERSION = 1;
    private static final String TABLE_NAME = "mytable";
    private static final String COLUMN_ID = "id";
    private static final String COLUMN_NAME = "name";

    public DatabaseHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        // create new table, you have to write raw SQL
        String createTableQuery = "CREATE TABLE " + TABLE_NAME + "("
                + COLUMN_ID + " INTEGER PRIMARY KEY AUTOINCREMENT,"
                + COLUMN_NAME + " TEXT)";

        db.execSQL(createTableQuery);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        // delete old database and build a new one
        db.execSQL("DROP TABLE IF EXISTS " + TABLE_NAME);
        onCreate(db);
    }

    public void insertData(String name) {
        SQLiteDatabase db = getWritableDatabase();
        ContentValues values = new ContentValues();
        values.put(COLUMN_NAME, name);
        db.insert(TABLE_NAME, null, values);
        db.close();
    }
    
    public void deleteData(String name) {
        SQLiteDatabase db = getWritableDatabase();
        String whereClause = COLUMN_NAME + " = ?";
        String[] whereArgs = {name};
        db.delete(TABLE_NAME, whereClause, whereArgs);
        db.close();
    }

    public Cursor getAllData() {
        SQLiteDatabase db = getReadableDatabase();
        // WARNING: THIS MAY BE SLOW!!!
        return db.rawQuery("SELECT * FROM " + TABLE_NAME, null);
    }
}
```

## 网络服务

OkHttp3 是一个功能强大、灵活且高效的 HTTP 客户端库，适用于各种网络通信需求。它为开发者提供了便捷的网络请求和处理功能，简化了网络编程的复杂性。首先，你需要在项目中添加 OkHttp3 库的依赖，在 build.gradle 文件中添加：`implementation 'com.squareup.okhttp3:okhttp:版本号'`。然后我们来看一个例子：

```java
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;

public class OkHttpExample {
    public static void sendGetRequest() {
        OkHttpClient client = new OkHttpClient();
        Request request = new Request.Builder()
                .url("YOUR_URL")
                .build();
        try {
            Response response = client.newCall(request).execute();
            if (response.isSuccessful()) {
                String responseData = response.body().string();
                // do something, such as parse a json file
                // you may try obj =  Gson().fromJson(responseData, yourClass.class);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    public static void sendPostRequest() {
        OkHttpClient client = new OkHttpClient();
        MediaType mediaType = MediaType.parse("application/json; charset=utf-8");
        String requestBody = "{\"key\": \"value\"}";
        RequestBody body = RequestBody.create(mediaType, requestBody);
        Request request = new Request.Builder()
                .url("YOUR_URL_2")
                .post(body)
                .build();
        try {
            Response response = client.newCall(request).execute();
            if (response.isSuccessful()) {
                String responseData = response.body().string();
                // do something...
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## 多媒体

加载本地的图片是很容易的（自己试试看）。如果要通过 URL 加载图片，可以使用 Glide，它是一个开源图片加载和缓存库。首先我们要有一个 ImageView，然后通过 Glide 把给定 URL 的图片加载出来。

```java
ImageView imageView = view.findViewById(R.id.your_image);
Glide.with(YourApplication.getContext()).load(imageURL).into(imageView);
```

对于网络视频，我们可以直接使用 URL 进行加载。

```java
VideoView videoView = view.findViewById(R.id.your_video);
videoView.setVideoPath(video);
MediaController mediaController = new MediaController(getContext());
videoView.setMediaController(mediaController);
videoView.requestFocus();
```

## 序列化与反序列化：JSON

JSONObject 和 JSONArray 是在处理 JSON 数据时使用的两种基本数据结构。它们是轻量级的，并且通常用于在 Web 服务和移动应用之间传输数据。在 Android 开发中，这两个类通常由 `org.json` 库提供，这个库不是 Android 标准库的一部分，因此需要单独添加到项目中。

### JSONObject

JSONObject 是一个映射表，用来存储键值对，其中键是字符串，值可以是以下类型之一：

- `String`
- `Number`
- `JSONObject`
- `JSONArray`
- `Boolean`
- `null`

**基本用法**:

1. **创建 JSONObject**:

   ```java
   JSONObject jsonObject = new JSONObject();
   ```

2. **添加数据**:

   ```java
   jsonObject.put("key", "value");
   jsonObject.put("key2", 123);
   jsonObject.put("key3", anotherJsonObject);
   ```

3. **获取数据**:

   ```java
   String value = jsonObject.getString("key");
   int number = jsonObject.getInt("key2");
   JSONObject jobj = jsonObject.getJSONObject("key3");
   ```

4. **遍历 JSONObject**:

   ```java
   Iterator<String> keys = jsonObject.keys();
   while (keys.hasNext()) {
       String key = keys.next();
       Object value = jsonObject.get(key);
       // 处理 key 和 value
   }
   ```

### JSONArray

JSONArray 是一个列表，用于存储有序的值集合。与 JSONObject 类似，JSONArray 中的值也可以是字符串、数字、布尔值、另一个 JSONObject 或 JSONArray。

**基本用法**:

1. **创建 JSONArray**:

   ```java
   JSONArray jsonArray = new JSONArray();
   ```

2. **添加数据**:

   ```java
   jsonArray.put("value1");
   jsonArray.put(123);
   jsonArray.put(anotherJsonObject);
   jsonArray.put(anotherJsonArray);
   ```

3. **获取数据**:

   ```java
   String value = jsonArray.getString(0);
   int number = jsonArray.getInt(1);
   JSONObject jobj = jsonArray.getJSONObject(2);
   JSONArray jarray = jsonArray.getJSONArray(3);
   ```

4. **访问长度**:

   ```java
   int length = jsonArray.length();
   ```

5. **遍历 JSONArray**:

   ```java
   for (int i = 0; i < jsonArray.length(); i++) {
       Object value = jsonArray.get(i);
       // 处理 value
   }
   ```

### 示例代码

以下是一个简单的示例，演示了如何创建和使用 `JSONObject` 和 `JSONArray`：

```java
import org.json.JSONArray;
import org.json.JSONObject;

public class JsonExample {
    public static void main(String[] args) {
        // 创建 JSONObject
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("name", "John Doe");
        jsonObject.put("age", 30);
        
        // 创建 JSONArray
        JSONArray jsonArray = new JSONArray();
        jsonArray.put("Apple");
        jsonArray.put("Banana");
        jsonArray.put("Cherry");
        
        // 将 JSONArray 添加到 JSONObject
        jsonObject.put("fruits", jsonArray);
        
        // 输出整个 JSON 对象
        System.out.println(jsonObject.toString());
        
        // 获取并输出 name
        System.out.println(jsonObject.getString("name"));
        
        // 获取并遍历 fruits
        JSONArray fruits = jsonObject.getJSONArray("fruits");
        for (int i = 0; i < fruits.length(); i++) {
            System.out.println(fruits.get(i));
        }
    }
}
```

## 输出日志：Log

在 Android 开发中，Log 类是用于输出日志信息的非常有用的工具。它属于 `android.util` 包，提供了多种方法来打印不同级别的日志信息，帮助开发者调试应用。

日志信息通常包括一个标签（tag）和一个消息（message）。标签是一个简短的字符串，用于标识日志信息的来源，而消息则是要输出的具体内容。

```java
Log.d("preferenceTest", "This is a debug message.");
```

Log 类输出的日志可以在 Android Studio 的 Logcat 窗口中查看。Logcat 是一个实时日志查看工具，允许开发者根据日志级别、标签等条件过滤日志信息。

使用 Log 类是 Android 开发中调试和监控应用状态的重要手段。合理使用日志可以帮助你快速定位问题并优化应用性能。
