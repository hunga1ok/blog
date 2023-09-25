---
layout: post
title: Android logging with Timber
description: Android logging with timber and how to implement
summary: android logging
tags: android logging
---

# Android logging with Timber

## Triển khai Timber
Logging là một vấn đề không còn xa lạ với các lập trình viên. Hôm nay mình cũng sẽ trình bày về cách implement logging sử dụng thư viện Timber.

Đầu tiên thì Timber là một API cho class Log của Android. Nó bổ trợ và nâng cấp cho chức năng log của Android. Ta làm việc đó bằng cách trồng (plant) một đối tượng Tree. Để sử dụng Timber, ta khai báo việc implement timber vào build.gradle 

```gradle
repositories {
  mavenCentral()
}

implementation 'com.jakewharton.timber:timber:5.0.1'
```

Việc cài đặt timber rất dễ dàng chúng ta chỉ cần tạo một Timber Tree. Tree ở đây là một dạng cây hỗ trợ việc triển khai cách ghi log vào hệ thống mà ta có thể tự tuỳ chỉnh hoặc dùng tree default là DebugTree. Ví dụ:

``` Kotlin
class MyApplication: Application() {
    override fun onCreate() {
        super.onCreate()
        Timber.plant(Timber.DebugTree())
    }

    override fun onTerminate() {
        super.onTerminate()
        Timber.uprootAll()
    }
}
```

Trên đây là một cách cài đặt đợt giản, chúng ta nên cài đặt Timber sớm nhất có thể. Khi dùng chúng ta có thể dùng như log bình thường:

```kotlin
Timber.v("content to log")
Timber.i("content to log")
Timber.d("content to log")
```

## Tạo một cây tuỳ chỉnh với Timber
Sau đây mình cùng tiến hành tạo một Timber Tree cho mình, ta cần override lại function log :

```kotlin
class LoggingCustom : Timber.Tree() {
    override fun log(priority: Int, tag: String?, message: String, t: Throwable?) {
        TODO("Not yet implemented")
    }
}
```

Dưới đây mình có một ví dụ về lưu custom Tree dùng với logger để lưu log vào file: 
- tạo 1 file `FileLoggingCustom.kt`

```kotlin
class FileLoggingCustom private constructor(
    context: Context,
    fileDir: String,
    fileName: String,
    sizeLimit: Int,
    fileLimitCount: Int,
    isAppendable: Boolean,
    tag: String,
    levelLog: Level
): Timber.Tree() {

    private var myLogger: Logger
    init {
        val path: String = File(context.getExternalFilesDir(fileDir), fileName).absolutePath
        val fileHandler: FileHandler
        val logger: Logger = MyLogger(tag)
        logger.level = levelLog
        if (logger.handlers.isEmpty()) {
            fileHandler = FileHandler(path, sizeLimit, fileLimitCount, isAppendable)
            fileHandler.formatter = SimpleFormatter()
            logger.addHandler(fileHandler)
        } else {
            fileHandler = logger.handlers[0] as FileHandler
            logger.addHandler(fileHandler)
        }

        myLogger = logger
    }

    override fun log(priority: Int, tag: String?, message: String, t: Throwable?) {
        myLogger.log(fromPriorityToLevel(priority), message)
    }

    private fun fromPriorityToLevel(priority: Int): Level {
        return when (priority) {
            Log.VERBOSE -> Level.FINER
            Log.DEBUG -> Level.FINE
            Log.INFO -> Level.INFO
            Log.WARN -> Level.WARNING
            Log.ERROR -> Level.SEVERE
            Log.ASSERT -> Level.SEVERE
            else -> Level.FINEST
        }
    }
    data class Builder(
        val context: Context,
        private var fileDir: String = "LogDir",
        private var fileName: String = "File%g.log",
        private var sizeLimit: Int = 1000_000,
        private var fileLimitCount: Int = 3,
        private var isAppendable: Boolean = true,
        private var tag: String = "FileLoggerTree",
        private var levelLog: Level = Level.ALL
    ) {
        fun fileDir(dir: String) = apply { this.fileDir = dir }
        fun fileName(name: String) = apply { this.fileName = name }
        fun sizeLimit(limit: Int) = apply { this.sizeLimit = limit }
        fun fileLimit(limit: Int) = apply { this.fileLimitCount = limit }
        fun isAppendable(append: Boolean) = apply { this.isAppendable = append }
        fun tag(tag: String) = apply { this.tag = tag }
        fun levelLog(level: Level) = apply { this.levelLog = level }
        fun build() = FileLoggingCustom(context, fileDir, fileName, sizeLimit, fileLimitCount, isAppendable, tag, levelLog)
    }
}
```

Khi khởi tạo Tree:

```kotlin
val t: Tree = FileLoggingCustom.Builder(this).build()
Timber.plant(t)
```
ta có thể custom lại các thông số của Logging:


```kotlin
val t: Tree = FileLoggingCustom.Builder(this)
                    .fileDir("DirFile")
                    .fileName("file%g.log")
                    .tag("CustomTag")
                    .sizeLimit(1000_000_000) // ~1MB
                    .fileLimit(3) // create limit 3 file
                    .levelLog(Level.ALL) // receive all log
                    .isAppendable(true) // true: append log to file, false: create new file
                    .build()
                Timber.plant(t)
```

File log sẽ được lưu vào path

sdcard/Android/data/`your package name`/DirFile/file`i`.log

Trên đây là một cách triển khai Timber và timber log to file.


Dưới đây là source code: [Source Code](https://github.com/hunga1ok/POC-Logging)