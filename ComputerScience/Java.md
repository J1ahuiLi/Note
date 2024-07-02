# Java

### 根据逗号截取字符串存入List
``` Java
String str = "one,two,three";
List<String> strList = Arrays.asList(str.split(","));
```

## stream流
### 字符串逗号分隔加引号
``` Java
String result = list.stream().map(s -> "\'" + s + "\'").collect(Collectors.joining(", "));
```
### List按某字段排序
``` Java
List<User> newList = list.stream().sorted(Comparator.comparing(User::getAge)).collect(Collectors.toList());
```
### List去重后元素个数
``` Java
long count = list.stream().distinct().count();
```
### 将List转换为用逗号隔开的字符串
``` java
String result = list.stream().map(String::valueOf).collect(Collectors.joining(","));
```
### List按某字段排序
``` java
```

