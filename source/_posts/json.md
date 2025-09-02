---
title: json
date: 2024-09-05 01:00:06
tags:
---




# 1.序列化（对象转为JSON）

## Java对象转Json字符串------JSON.toJSONString()

```java
public void testObjectToJson(){
Student studednt = new Student();
student.setId(1);
student.setName("张三");
student.setAge(20);
student.setBirthday(getDate());
//student对象，转到Json格式字符串
String jsonString = JSON.toJSONString(student);
system.out.println(jsonString);
//{"age":20,"birthday":15842577591,"id":1,"name":"张三"}
//转出来的是对象
}
```





## Java中的集合List序列化为Json格式字符串------JSON.toJSONString()

```java
public void testListToJson(){
List<Student> list = new ArrayList<Student>();

Student studednt1 = new Student();
student1.setId(1);
student1.setName("张三");
student1.setAge(20);
student1.setBirthday(getDate());

Student studednt2 = new Student();
student2.setId(2);
student2.setName("李四");
student2.setAge(22);
student2.setBirthday(getDate());

list.add(studednt1);
list.add(studednt2);

//List集合，序列化为Json格式字符串
String jsonString = JSON.toJSONString(list);
system.out.println(jsonString);
//[{"age":20,"birthday":15842577591,"id":1,"name":"张三"},
//{"age":22,"birthday":15842577591,"id":2,"name":"李四"}]
//转出来的是数组
}
```





## Java中的集合Map序列化为Json格式字符串------JSON.toJSONString()

```java
public void testMapToJson(){
Map<String,Student> map = new HashMap<String,Studen>();

Student studednt1 = new Student();
student1.setId(1);
student1.setName("张三");
student1.setAge(20);
student1.setBirthday(getDate());

Student studednt2 = new Student();
student2.setId(2);
student2.setName("李四");
student2.setAge(22);
student2.setBirthday(getDate());

map.put("student1",studednt1);
map.put("student2",studednt2);

//Map集合，序列化为Json格式字符串
String jsonString = JSON.toJSONString(map);
system.out.println(jsonString);
//{
//"student1":{"age":20,"birthday":15842577591,"id":1,"name":"张三"},
//"student2":{"age":22,"birthday":15842577591,"id":2,"name":"李四"}
//}
//转出来的是对象，对象中有2个键，键对应的值是student对象
}
```





# 1.反序列化（JSON转为对象）

## Json字符串转Java对象-----JSON.parseObject()

```java
public void testJsonToObject(){
String jsonString="{\"age\":20,\"birthday\":15842577591,\"id\":1,\"name\":\"张三\"}"
//Json类的静态方法 parseObject
//传递要反序列化的Json字符串，传递Java对象的class对象
Student student = JSON.parseObject(jsonString,Student.class);
system.out.println(student);
//Student(id=1,name=张三,age=20,birthday=Sun Mar 15 15:35:59 CST 2021)
}
```



## Json格式字符串反序列化为Java中的集合List-----JSON.parseArray()

```java
public void testJsonToList(){
String jsonString="[{\"age\":20,\"birthday\":15842577591,\"id\":1,\"name\":\"张三\"},{\"age\":22,\"birthday\":15842577591,\"id\":2,\"name\":\"李四\"}]"
//Json类的静态方法 parseArray
//传递要反序列化的Json字符串，传递Java对象的class对象
List<Student> list = JSON.parseArray(jsonString,Student.class);
system.out.println(list);
//Student(id=1,name=张三,age=20,birthday=Sun Mar 15 15:35:59 CST 2021)
//Student(id=2,name=李四,age=22,birthday=Sun Mar 15 15:35:59 CST 2021)
}
```



## Json格式字符串反序列化为Java中的集合Map-----JSON.parseObject()

```java
public void testJsonToMap(){
String jsonString="{"student1":{\"age\":20,\"birthday\":15842577591,\"id\":1,\"name\":\"张三\"}","student2":{\"age\":22,\"birthday\":15842577591,\"id\":2,\"name\":\"李四\"}"}
//Json类的静态方法 parseObject
//传递要反序列化的Json字符串，传递Java对象的class对象
Map<<String,Student>> map = JSON.parseObject(jsonString,new TypeReference<Map<String,Student>>(){});
system.out.println(student);
//student1::Student(id=1,name=张三,age=20,birthday=Sun Mar 15 15:35:59 CST 2021)
//student2::Student(id=2,name=张三,age=20,birthday=Sun Mar 15 15:35:59 CST 2021)
}
```

