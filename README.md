# 有关Android的SQLite的学习笔记

**你妈的，你妈的，这\*\*WINDOWS11更新，害得我这是第三次写这玩意**

### 0、项目介绍

    就是一个用来测试的项目，没什么特别的。

    通过一个按钮和一个输入框，简单的实现一个按下按钮就会把输入框中的内容给放到数据库中。界面如下：

  ![avatar](https://github.com/Kanyooooo/Android-SQLite-Study/blob/main/images/01.png?raw=tru)

    然后数据库大概长这个样子：

| id  | name |
| --- | ---- |
| 1   | rh   |
| 2   | cgy  |

### 1、创建数据库

    首先，创建一个新的java类文件。然后继承(extends) SQLiteOpenHelper。继承后你还有一系列的操作，例如写相对于的构造函数啊，例如要把一些继承的内容写一遍啊等等。

#### 构造函数：

```java
    public DataHelper(@Nullable Context context) {
        super(context, "VALUE_DB.db", null, 1);
    }
```

    这个里面需要一个super，写四个参数。

    第一个参数是做数据接盘作用的。在A与B之间共享一些值什么的这些活。   

    第二个参数是数据库名，**更应该叫做数据库文件名**，为啥要强调呢？因为我没有写.db导致我找了半个小时的数据库文件。结果当然是我发现了这个问题，然后把文件改回来了。所以一定要有后缀。

    第三个参数应该是cursorfactory，是SqliteDatabase分出来的。适用于一些自定义的需求，当然我没有。一般情况下就使用它默认的cursor就可以了。使用默认的话，这个参数就写null。

    第四个参数是版本号。

#### onCreate和onUpgrade

    首先是onCreate，实际上它会被经常性的使用。这个暂且不谈。当第一个版本号出现的时候会执行onCreate。之后如果版本号有所变动，就会执行onUpgrade了。我们对onCreate需要进行重写，用它创建一个表用来存数据的。代码如下：

```java
    @Override
    public void onCreate(SQLiteDatabase sqLiteDatabase) {
        //CREATE TABLE website (
        //    id      INT              NOT NULL   PRIMARY KEY,
        //    name    VARCHAR(20)      NOT NULL,
        //);
        String sql = "CREATE TABLE value(id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,  name VARCHAR(20));";
        try{
            sqLiteDatabase.execSQL(sql);
        }catch (Exception e){}
    }
    
```

       这边用到了SQLiteDatabase里面的execSQL()方法。这个方法就是把一个string类型的字符串像用SQL的查询一样的给执行。但是要注意，他不会给你有什么消息回复的。

    然后就是一些数据库要注意的知识，id是PRIMARY KEY的同时记得设置自动增长AUTOCREMENT。

### 2、使用数据库

    这个相比创建就简单多了。比如我们用上面的execSQL方法进行数据的插入：

*MainActivity：*

```java
package com.example.myapplication;

import androidx.appcompat.app.AppCompatActivity;

import android.database.sqlite.SQLiteDatabase;
import android.os.Bundle;

public class MainActivity extends AppCompatActivity {

    DataHelper dataHelper;
    SQLiteDatabase sqLiteDatabase;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        dataHelper = new DataHelper(this);
        sqLiteDatabase = dataHelper.getWritableDatabase();
        sqLiteDatabase.execSQL("INSERT INTO value(name) VALUES(\'rh\')");
    }
}
```

    在初始化后，execSQL中直接执行INSERT语句让他插入数据就可以。

    当然，这样没有一个可视化界面，看不见表中的数据。为了有一个查看器供我测试。我先去settings安装了一个插件：

![](https://github.com/Kanyooooo/Android-SQLite-Study/blob/main/images/02.png?raw=true)

记得使用这个插件视图（需要重启android studio）：

![](https://github.com/Kanyooooo/Android-SQLite-Study/blob/main/images/03.png?raw=true)

    在资源管理器找到你的数据库文件，双击或者右键导出。

    然后在你的C盘（都搜一下，我就一个C盘。可以用everything）或者你导出的地方搜你的数据库名字。一般就能找到数据库了。把他加进去：

![](https://github.com/Kanyooooo/Android-SQLite-Study/blob/main/images/05.png?raw=true)

    ok，最后我们查询一下表吧：

![](https://github.com/Kanyooooo/Android-SQLite-Study/blob/main/images/04.png?raw=true)

    这样就可以看到数据了。



    在这个基础上就可以进行各种操作了。以至于SQLiteOpenHelper里面自带的什么insert啊这些操作用的函数我就不写文档了。虽然有返回值，但是用起来也有很多鸡肋的地方。没有必要写了。



    有必要记得每次读取数据的时候都需要导出一下数据库。虽然比较麻烦，但是必须这样。
