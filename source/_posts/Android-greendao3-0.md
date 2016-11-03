---
title: Android-Greendao3.0
date: 2016-10-29 15:17:21
tags: 数据库
category: Android
---

greenDao2.0 时代的时候 教程太多，所以就没有写..

现在greenDao3.0了, 而且用法有点变化... 所以简单些一下用法

关于greenDAO的概念可以看官网[greenDAO](http://greenrobot.org/greendao/)

<!-- more -->

greenDao是一个将对象映射到SQLite数据库中的轻量且快速的ORM解决方案。

## greenDAO 优势
 - 一个精简的库
 - 性能最大化
 - 内存开销最小化
 - 易于使用的 APIs
 - 对 Android 进行高度优化

## greendao 配置

 - build.gradle
 
```gradle
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.2.2'
        //greendao插件
        classpath 'org.greenrobot:greendao-gradle-plugin:3.2.0'
     }
}

```

 - app/build.gradle
 
```
    apply from: 'greendao.gradle'
    
    dependencies {
    	compile 'org.greenrobot:greendao:3.2.0'
    }
```

 - greendao.gradle

```gradle
apply plugin: 'org.greenrobot.greendao'

greendao {
    //数据库schema版本，也可以理解为数据库版本号
    schemaVersion 1
    //设置DaoMaster 、DaoSession、Dao包名
    daoPackage 'com.sky.db'
    //设置DaoMaster 、DaoSession、Dao目录
    targetGenDir 'src/main/java'
    //设置生成单元测试目录
//    targetGenDirTest
    //设置自动生成单元测试用例
//    generateTests
}
```

## greendao 注解
### 实体类注解
```calss
@Entity(
        schema = "myschema",
 
        active = true,
         
        nameInDb = "AWESOME_USERS",
         
        indexes = {
                @Index(value = "name DESC", unique = true)
        },
         
        createInDb = false
)
public class User {
  ...

```

 - schema是一个项目中有多个schema时 标明要让这个dao属于哪个schema

 - active 是标明是否支持实体类之间update，refresh，delete等操作 
 - nameInDb 就是写个存在数据库里的表名（不写默认是一致）

 - indexes 定义索引，这里可跨越多个列

 - CreateInDb 如果是有多个实体都关联这个表，可以把多余的实体里面设置为false避免重复创建（默认是true）

 
### 属性注解

```class
@Entity
public class User {
    @Id(autoincrement = true)
    private Long id;
  
  	 @Unique
    @Property(nameInDb = "USERNAME")
    private String name;
  
    @NotNull
    private int repos;
  
    @Transient
    private int tempUsageCount;
    
    @OrderBy
    private int updateTime;
  
    ...
}
```

 - @Entity 用于标识这是一个需要Greendao帮我们生成代码的bean

 - @Id 标明主键，括号里可以指定是否自增 相当于2.2版本的

 - @Property 用于设置属性在数据库中的列名（默认不写就是保持一致）

 - @NotNull 非空

 - @Transient 标识这个字段是自定义的不会创建到数据库表里
 
 - @OrderBy 排序 用法``` @OrderBy("name, age DESC") List collection ```

 - @Unique 唯一约束

### 关系注解

#### 一对一

 - @ToOne 是将自己的一个属性与另一个表建立关联

```class
@Entity
public class Order {
    @Id private Long id;
  
    private long customerId;
  
    @ToOne(joinProperty = "customerId")
    private Customer customer;
}
  
@Entity
public class Customer {
    @Id private Long id;
}
```



#### 多对多 关系比较复杂, 拆分出来

 - @ToMany 的使用场景有些多

##### 一对多 外键约束

```class
@Entity
public class User {
    @Id private Long id;
  
    @ToMany(referencedJoinProperty = "ownerId")
    private List<Site> ownedSites;
}
  
@Entity
public class Site {
    @Id private Long id;
    private long ownerId;
}
```

##### 一对多 关联约束

```class
@Entity
public class User {
    @Id private Long id;
    @Unique private String authorTag;
  
    @ToMany(joinProperties = {
            @JoinProperty(name = "authorTag", referencedName = "ownerTag")
    })
    private List<Site> ownedSites;
}
  
@Entity
public class Site {
    @Id private Long id;
    @NotNull private String ownerTag;
}
```
##### 多对多
```class
@Entity
public class Site {
    @Id private Long id;
  
    @ToMany
    @JoinEntity(
            entity = JoinSiteToUser.class,
            sourceProperty = "siteId",
            targetProperty = "userId"
    )
    private List<User> authors;
}
  
@Entity
public class JoinSiteToUser {
    @Id private Long id;
    private Long siteId;
    private Long userId;
}
  
@Entity
public class User {
    @Id private Long id;
}
```

### 属性转换器 PropertyConverter

 - 这是一个接口，我们使用它可以让实体的属性类型不再局限于原始类型， 可以自定义任何的类型，充分保证了实体的灵活性

 - PropertyConverter<P,D>接口中需要定义两个泛型，意思分别为P：实体中自定义的类型，D：数据库中的类型。

```class
public class NoteTypeConverter implements PropertyConverter<NoteType, String> {
    @Override
    public NoteType convertToEntityProperty(String databaseValue) {
        return NoteType.valueOf(databaseValue);
    }

    @Override
    public String convertToDatabaseValue(NoteType entityProperty) {
        return entityProperty.name();
    }
}
```

 - 在属性中自定义类型

```class
@Convert(converter = NoteTypeConverter.class, columnType = String.class)
private NoteType type;
```

 - converter  指定转换类
 - columnType 在数据库的属性

## 使用

 - 官方推荐的初始化 是在application里，以下官方demo 代码
 
 - ENCRYPTED 是否加密
 
 - helper.getEncryptedWritableDb("super-secret") 参数是密码

```class
import android.app.Application;

import org.greenrobot.greendao.database.Database;
import org.greenrobot.greendao.example.DaoMaster.DevOpenHelper;

public class App extends Application {
    /** A flag to show how easily you can switch from standard SQLite to the encrypted SQLCipher. */
    public static final boolean ENCRYPTED = true;

    private DaoSession daoSession;

    @Override
    public void onCreate() {
        super.onCreate();

        DevOpenHelper helper = new DevOpenHelper(this, ENCRYPTED ? "notes-db-encrypted" : "notes-db");
        Database db = ENCRYPTED ? helper.getEncryptedWritableDb("super-secret") : helper.getWritableDb();
        daoSession = new DaoMaster(db).newSession();
    }

    public DaoSession getDaoSession() {
        return daoSession;
    }
}
```

 - greenDao3.0支持加密 在3.0.1进行了合并

```gradle
//没合并前gradle引用
compile 'net.zetetic:android-database-sqlcipher:3.5.2'
```

## 结尾

使用了官方的demo 所以没有自己建项目,进行图文解说...


[demo地址](https://github.com/greenrobot/greenDAO/tree/master/examples/DaoExample/src/main)
