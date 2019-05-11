---
title: Build Number Ranges
---
<style>
  table {
    width:100%;
  }
  th:first-child, td:first-child {
    width: 10%;
  }
</style>

使用此编译范围的引用来指定插件描述符中正确的`since-build`和`until-build`值.


从IntelliJ IDEA 9 beta开始,使用多部分内部版本号,例如`IU-162.94`.


该号码由以下部分组成:


* 产品ID(IDEA社区的`IC`,IDEA Ultimate的'IU`,RubyMine的`RM`,PyCharm的'PY`等)

* 分行号码(`162`)

* 分支中的内部编号(`94`)


从 *IntelliJ Platform* 的2016.2版本开始,分支编号基于`YYYY.R` [IDE release version numbers](https://blog.jetbrains.com/blog/2016/03/09/jetbrains-toolbox-release-and-versioning-changes/).
分支编号采用年份的最后两位数字和“R”发布编号.
例如,2016年的“162”,2016年的“163”,2017年的“171”.
在这个方案中,“IU-162.94”对应于2016.2版本.


从2016.2开始,内部版本号也可能有多个组件:`IU-162.94`,`IU-162.94.11`,`IU-162.94.11.256.42`.
这为第三方插件和IDE开发人员提供了更大的灵活性.
插件可以更精确地指定兼容版本; 
IDE供应商可能具有基于特定 *IntelliJ Platform* 版本的内部版本号,并且还指定了其他内部版本(例如,“XX-162.94.11.256.42”中的“256.42”),以允许插件开发人员为其IDE指定兼容性.


多部分构建号也可以用在`idea-version`的`since-build`和`until-build`属性中.
通常,您应该省略产品ID并仅使用分支编号和内部版本号,例如:


```xml
<idea-version since-build="94.539"/>
<idea-version since-build="162.539.11"/>
<idea-version until-build="162"/> <!-- any build until 162, not inclusive!-->
<idea-version since-build="162" until-build="162.*"/> <!-- any 162-based version, 162.94, 162.94.11, etc.-->
```

以下分支编号用于基于 *IntelliJ Platform* 的产品,并构建最新IDE版本的编号:


| Branch number                                                   | IntelliJ Platform version |
|-----------------------------------------------------------------|---------------------------|
| [191](https://github.com/JetBrains/intellij-community/tree/191) | 2019.1                    |
| [183](https://github.com/JetBrains/intellij-community/tree/183) | 2018.3                    |
| [182](https://github.com/JetBrains/intellij-community/tree/182) | 2018.2                    |
| [181](https://github.com/JetBrains/intellij-community/tree/181) | 2018.1                    |
| [173](https://github.com/JetBrains/intellij-community/tree/173) | 2017.3                    |
| [172](https://github.com/JetBrains/intellij-community/tree/172) | 2017.2                    |
| [171](https://github.com/JetBrains/intellij-community/tree/171) | 2017.1                    |
| [163](https://github.com/JetBrains/intellij-community/tree/163) | 2016.3                    |
| [162](https://github.com/JetBrains/intellij-community/tree/162) | 2016.2                    |

请注意,没有'170`.
在`YYYY.R`版本控制方案中,`R`部分从1开始.


## 2016年之前IntelliJ平台的分支和内部版本号


| Branch number | Product version |
|---------------|-----------------|
| [145](https://github.com/JetBrains/intellij-community/tree/145) | IntelliJ IDEA 2016.1, AppCode 2016.1, CLion 2016.1, DataGrip 2016.1, PhpStorm 2016.1, PyCharm 2016.1, RubyMine 2016.1, WebStorm 2016.1 |
| [143](https://github.com/JetBrains/intellij-community/tree/143) | IntelliJ IDEA 15, AppCode 3.3, CLion 1.2, DataGrip 1.0, PhpStorm 10, PyCharm 5, RubyMine 8, WebStorm 11 |
| [141](https://github.com/JetBrains/intellij-community/tree/141) | IntelliJ IDEA 14.1, Android Studio 1.3, PyCharm 4.1, WebStorm 10 |
| [139](https://github.com/JetBrains/intellij-community/tree/139) | IntelliJ IDEA 14, PhpStorm 8, PyCharm 4, RubyMine 7, WebStorm 9 |
| [135](https://github.com/JetBrains/intellij-community/tree/135) | IntelliJ IDEA 13.1, WebStorm 8 |
| [133](https://github.com/JetBrains/intellij-community/tree/133) | IntelliJ IDEA 13, PhpStorm 7, PyCharm 3.1, WebStorm 7 |
| [131](https://github.com/JetBrains/intellij-community/tree/131) | PhpStorm 7, PyCharm 3.0, WebStorm 7 |
| [129](https://github.com/JetBrains/intellij-community/tree/129) | IntelliJ IDEA 12.1, bug-fix updates for PyCharm 2.7 and PhpStorm/WebStorm 6.0 |
| [127](https://github.com/JetBrains/intellij-community/tree/127) | AppCode 2.0, PhpStorm/WebStorm 6.0 |
| [125](https://github.com/JetBrains/intellij-community/tree/125) | PyCharm 2.7, RubyMine 5.0 |
| [123](https://github.com/JetBrains/intellij-community/tree/123) | IntelliJ IDEA 12 |
| [121](https://github.com/JetBrains/intellij-community/tree/121) | AppCode 1.6, PyCharm 2.6, PhpStorm/WebStorm 5.0 |
| [119](https://github.com/JetBrains/intellij-community/tree/119) | RubyMine 4.5 |
| [117](https://github.com/JetBrains/intellij-community/tree/117) | IntelliJ IDEA 11.1, AppCode 1.5, PyCharm 2.5, RubyMine 4.0.x, PhpStorm/WebStorm 4.0 |
| 111           | IntelliJ IDEA 11.0 |
| 107           | IDEA 10.5 |
| 103           | IDEA 10.0.2\+ |
| 99            | IDEA 10.0+ |
| 95            | IDEA 9.0.2\+ |
| 93            | IDEA 9.0 |

IntelliJ IDEA版本的内部版本号:


| IntelliJ IDEA version | Build number  |
|-----------------------|---------------|
| 12.0                  | 123.72        |
| 11.1.3                | 117.798       |
| 11.1.2                | 117.418       |
| 11.1.1                | 117.117       |
| 11.1                  | 117.105       |
| 11.0.2                | 111.277       |
| 11.0.1                | 111.167       |
| 11.0                  | 111.69        |
| 10.5.2                | 107.587       |
| 10.5.1                | 107.322       |
| 10.5                  | 107.105       |
| 10.0.3                | 103.255       |
| 10.0.2                | 103.72        |
| 10.0.1                | 99.32         |
| 10.0                  | 99.18         |
| 9.0.4                 | 95.627        |
| 9.0.3                 | 95.429        |
| 9.0.2                 | 95.66         |
| 9.0.1                 | 93.94         |
| 9.0                   | 93.13         |

## IntelliJ IDEA 9.0之前的内部版本号


在IntelliJ IDEA 9.0之前,使用了线性内部版本号,具有以下范围:


| IntelliJ IDEA version | Build number range |
|-----------------------|--------------------|
| 8.1.x                 | 9500-9999          |
| 8.0.x                 | 9100-9499          |
| 8.0                   | 8000-9099          |
| 7.0.2+                | 7500-7999          |
| 7.0 final             | 7200-7499          |
| 7.0 pre-M2            | 6900-7199          |
| 7.0 pre-M1            | 6500-6899          |
| 6.0.2 branch          | 6000-6499          |
| 6.0 branch            | 5000-5999          |
| 5.1 branch            | 4000-4999          |

每个版本的内部版本号:


| IntelliJ IDEA version | Build number |
|-----------------------|--------------|
| 8.1                   | 9732         |
| 8.0.1                 | 9164         |
| 8.0                   | 9013         |
| 8.0M1                 | 8664         |
| 7.0.5                 | 7971         |
| 7.0.3                 | 7757         |
| 7.0.2                 | 7590         |
| 7.0 final             | 7361         |
| 7.0 M2                | 7126         |
| 7.0 M1                | 6813         |
| 6.0.6                 | 6197         |
| 6.0.5                 | 6180         |
| 6.0.1                 | 5784         |
| 5.1.2                 | 4267         |


