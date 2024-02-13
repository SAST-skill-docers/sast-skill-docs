# Android 的组件

编写 Android 应用程序就像搭积木，组件是构建应用程序的基本单元。对组件来说，最重要的方法是围绕着生命周期和交互进行的。我们下面将对这些概念进行介绍。

## Activity & Fragment

活动（Activity）代表应用程序的一个屏幕或一个交互页面。Android 应用程序通常由多个活动组成，并且可以通过意图（Intent）在活动之间进行切换。通过启动新的活动，可以实现屏幕之间的导航和交互。片段（Fragment）是一种可重复使用的 UI 组件，通常嵌入在活动中，每个 Activity 可以包含多个 Fragment。

Activity 和 Fragment 通过布局文件定义其用户界面的外观和结构。布局文件使用 XML 格式编写，其中包含各种视图组件，如按钮、文本框、图像等。通过调用 `setContentView()` 方法，可以将布局文件与活动关联起来。下面是一个最简单的例子。我们使用 `R.<path>` 来获取资源库的对应文件（如果你不确定写法，不妨打一个 `R.`，然后让强大的 Android Studio 补全），这样你就可以看见你的组件了。

```java
// MyActivity1.java
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;

public class MyActivity1 extends Activity {

    private TextView textView;
    private Button button;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_1);

        textView = findViewById(R.id.textView);
        button = findViewById(R.id.button);

        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Toast.makeText(MainActivity.this, "Button Clicked", Toast.LENGTH_SHORT).show();
            }
        });
    }
}
```

```xml
<!-- activity_1.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    android:padding="16dp"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello, World!"
        android:textSize="24sp" />

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Click Me" />

</LinearLayout>
```

看见组件之后，你需要给组件定义实际的行为。每个 Activity 和 Fragment 都有其生命周期，描述了从创建到销毁的整个过程。在生命周期中，系统会调用特定的生命周期回调方法，以便程序可以在适当的时机执行相关操作。例如，在 `onCreate()` 中进行初始化，`onResume()` 中启动动画，`onPause()` 中保存数据等。Activity 和 Fragment 可以响应用户的触摸事件、按键事件等，并执行相应的操作。开发者可以通过重写事件处理方法，如 `onTouch()`、`onKeyDown()` 等，来处理这些事件。下面就是一个重载 `onTouch()` 的 Fragment 的例子。

```java
// CustomFragment.java
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.MotionEvent;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Button;
import android.widget.TextView;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.fragment.app.Fragment;

public class CustomFragment extends Fragment {

    private TextView textView;

    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fragment_custom, container, false);

        Button button = view.findViewById(R.id.button);
        textView = view.findViewById(R.id.textView);

        button.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                switch (event.getAction()) {
                    case MotionEvent.ACTION_DOWN:
                        textView.setText("Pressing");
                        return true;
                    case MotionEvent.ACTION_MOVE:
                        textView.setText("Moving");
                        return true;
                    case MotionEvent.ACTION_UP:
                        textView.setText("");
                        return true;
                    default:
                        return false;
                }
            }
        });
        /* or use lambda function:
        button.setOnTouchListener((v, event) -> {
            switch (event.getAction()) {
                case MotionEvent.ACTION_DOWN:
                    textView.setText("Pressing");
                    return true;
                case MotionEvent.ACTION_MOVE:
                    textView.setText("Moving");
                    return true;
                case MotionEvent.ACTION_UP:
                    textView.setText("");
                    return true;
                default:
                    return false;
            }
        });
        */

        return view;
    }
}
```

```xml
<!-- fragment_custom.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    android:padding="16dp"
    tools:context=".CustomFragment">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button" />

    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text=""
        android:textSize="24sp" />

</LinearLayout>
```

### Adapter

你可能想到，在上面的例子，组件显示的内容是静态的，在实际的场景中，数据量的大小是不定的，因此我们需要一个可以动态扩展的组件，并将组件与数据绑定。这就是 Adapter 的作用。Adapter 是一个中间层，用于将数据与组件进行绑定。在 Android 中，ListView、GridView、RecyclerView 等组件都是通过 Adapter 来实现数据绑定的。下面是一个简单显示文字列表的 RecyclerView 的 Adapter 的例子。

```java
// MyAapter.java
List<String> textList = new ArrayList<>();

public class MyAdapter extends RecyclerView.Adapter<MyViewHolder> {

    @NonNull
    @Override
    public MyViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(context).inflate(R.layout.text_item, parent, false);
        MyViewHolder holder = new MyViewHolder(view);
        return holder;
    }

    @Override
    public void onBindViewHolder(@NonNull MyViewHolder holder, int pos) {
        String text = textList.get(pos);
        holder.text.setText(text);
    }

    public int getItemCount() {
        return textList.size();
    }
}

class MyViewHolder extends RecyclerView.ViewHolder {
    TextView text;

    public MyViewHolder(@NonNull View itemView) {
        super(itemView);
        text = itemView.findViewById(R.id.text);
    }
}
```

```xml
<!-- text_item.xml -->
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/text"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:padding="16dp"
    android:textSize="24sp" />
```

在上面的例子中，我们使用了 List 来存储文本并使用 RecyclerView 来显示列表。RecyclerView 是一个高度可定制的组件，可以实现各种各样的列表。它的工作原理是，当列表中的某个元素需要显示时，RecyclerView 会调用 Adapter 的 `onCreateViewHolder()` 方法，创建一个 ViewHolder，然后调用 `onBindViewHolder()` 方法，将数据与 ViewHolder 绑定。ViewHolder 是一个包含了列表元素的视图组件的容器，它可以缓存列表元素的视图组件，以便重复使用。

在数据发生变化时，我们需要调用 Adapter 的 `notifyDataSetChanged()` 方法，通知 RecyclerView 更新列表，你也可以使用 `notifyItemInserted()`、`notifyItemRemoved()` 等方法，来更新列表的某个元素。

## Intent

假如我们现在有了一堆 Activity 等组件，一个很自然的需求就是让他们之间可以进行通信。最简单的例子是，一个活动调用另一个（我们可以在活动之间传递信息，就像函数一样）。

```java
Intent intent = new Intent(CurrentActivity.this, TargetActivity.class);
/* optional:
	Bundle bundle = new Bundle();
	bundle.putString("string1", "YOUR_STRING");
	intent.putExtras(bundle);
*/
startActivity(intent);

/*  get your bundle in the target activity:
	Bundle bundle = this.getIntent().getExtras();
	String str1 = bundle.getString("string1");
*/
```

## Service

并不是所有组件都是 Activity 和 Fragment，还有很多组件和界面无关，比如播放音乐或下载文件的组件。这就需要用到 Service。它的生命周期与 Activity 等有些差异。这里是一个音乐播放的 Service，在 `onStartCommand()` 方法中，我们通过接收 Intent 中的音乐 URL，使用 MediaPlayer 播放音乐。在服务销毁时，我们则需要释放 MediaPlayer 的资源。

```java
// MusicPlayerService.java
public class MusicPlayerService extends Service {

    private MediaPlayer mediaPlayer;

    @Override
    public void onCreate() {
        super.onCreate();
        mediaPlayer = new MediaPlayer();
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        String musicUrl = intent.getStringExtra("music_url");
        try {
            mediaPlayer.setDataSource(musicUrl);
            mediaPlayer.prepare();
            mediaPlayer.start();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return START_NOT_STICKY; // don't restart when killed by system
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        if (mediaPlayer != null) {
            mediaPlayer.release();
            mediaPlayer = null;
        }
    }

    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }
}

/*
	Intent intent = new Intent(CurrentActivity.this, MusicPlayerService.class);
	Bundle bundle = new Bundle();
	bundle.putString("music_url", "YOUR_URL");
	intent.putExtras(bundle);
	startService(intent);
*/
```