---
title: Plugins List API
---

This API endpoint provides a list of all plugins compatible with a provided IDE build:
 
```
https://plugins.jetbrains.com/plugins/list/?build=<productCode>-<buildNumber>
```

哪里


* ** productCode **是一个双字符产品代码.
可以是空白的.


* ** buildNumber **是IDE的内部版本号(在产品和发行说明中的​​ About Dialog *中指定).


例如
对于IntelliJ IDEA 2017.1(`productCode = IU`,`buildNumber = 171.3780.107`):


```
https://plugins.jetbrains.com/plugins/list/?build=IU-171.3780.107
```


