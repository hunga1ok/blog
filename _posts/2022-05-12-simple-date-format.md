---
layout: post
title: How to create a SimpleDateFormat
description: Create a SimpleDateFormat is so easy ^^
summary: This is a reference for creating a SimpleDateFormat and full format of date
tags: android coding
---
## [SimpleDateFormat](https://docs.oracle.com/javase/7/docs/api/java/text/SimpleDateFormat.html)
> Table content for reference:


| Letter | Date or Time Component                           | Presentation       | Examples                              |
| ------ | ------------------------------------------------ | ------------------ | ------------------------------------- |
| G      | Era designator                                   | Text               | AD                                    |
| y      | Year                                             | Year               | 1996; 96                              |
| Y      | Week year                                        | Year               | 2009; 09                              |
| M      | Month in year                                    | Month              | July; Jul; 07                         |
| w      | Week in year                                     | Number             | 27                                    |
| W      | Week in month                                    | Number             | 2                                     |
| D      | Day in year                                      | Number             | 189                                   |
| d      | Day in month                                     | Number             | 10                                    |
| F      | Day of week in month                             | Number             | 2                                     |
| E      | Day name in week                                 | Text               | Tuesday; Tue                          |
| u      | Day number of week (1 = Monday, ..., 7 = Sunday) | Number             | 1                                     |
| a      | Am/pm marker                                     | Text               | PM                                    |
| H      | Hour in day (0-23)                               | Number             | 0                                     |
| k      | Hour in day (1-24)                               | Number             | 24                                    |
| K      | Hour in am/pm (0-11)                             | Number             | 0                                     |
| h      | Hour in am/pm (1-12)                             | Number             | 12                                    |
| m      | Minute in hour                                   | Number             | 30                                    |
| s      | Second in minute                                 | Number             | 55                                    |
| S      | Millisecond                                      | Number             | 978                                   |
| z      | Time zone                                        | General time zone  | Pacific Standard Time; PST; GMT-08:00 |
| Z      | Time zone                                        | RFC 822 time zone  | -0800                                 |
| X      | Time zone                                        | ISO 8601 time zone | -08; -0800; -08:00                    |

##Some code extensions kotlin: 

> I use [JodaTime](https://github.com/JodaOrg/joda-time) library for support handle features

```kotlin

fun String?.formatHour(toPattern: String) = formatHour("HH:mm:ss", toPattern)

fun String?.formatHour(fromPattern: String, toPattern: String): String {
    if (this == null) return ""
    return try {
        DateTime.parse(this, DateTimeFormat.forPattern(fromPattern)).toString(toPattern)
    } catch (e: Exception) {
        "Invalid Date"
    }
}

fun String?.formatDate(toPattern: String): String {
    if (this == null) return ""
    return try {
        DateTime(this).toString(toPattern)
    } catch (e: Exception) {
        "Invalid Date"
    }
}

fun String?.formatDate(fromPattern: String, toPattern: String): String {
    if (this == null) return ""
    return try {
        DateTime.parse(this, DateTimeFormat.forPattern(fromPattern)).toString(toPattern)
    } catch (e: Exception) {
        "Invalid Date"
    }
}

fun String?.parseDate(pattern: String? = null): DateTime? {
    if (this == null) return null
    return try {
        pattern?.let { DateTime.parse(this, DateTimeFormat.forPattern(pattern)) } ?: DateTime(this)
    } catch (e: Exception) {
        null
    }
}


```
