## Basic

> ### key-value
>
> * key : value
> * type(value) = [int, float, string , bool, array, object, null ]

---

>### int
>
>```json
>"age": 18
>```
>
>---
>
>### float
>
>```json
>"height": 178.8
>```
>
>---
>
>### object
>
>```json
>{key0 :  value0, key1 : value1, ...}
>```
>
>---
>
>### array
>
>```json
>[
>    {key0 : value0, value0},
>    {key1 : value1, value1},
>    ...
>]
>```
>
>---
>
>### bool
>
>```json
>"key" : true
>```
>
>---
>
>### null
>
>```json
>"key" : null
>```
>
>---



## Object

### access

```json
var Obj={"key" :value }
x = Obj.key;
x = Obj["key"];
```

### loop

```json
var Obj={"key0":value0, "key1":value1, "key2": value2};
for (x in Obj){
    document.getElementById("demo").innerHTML+=x+"<br>";
}//x is the key

for (x in Obj){
    document.getElementById("demo").innerHTML+=Obj[x]+"<br>";
}//Obj[x] is the value
```

### nestification

```json
var Obj={
    "name":"Sinow",
    "Country":Zh,
    "Task":{
        "task0":"Get Json Done",
        "task1":"Finish It"
    }
};
```

### modify

```json
Obj.Task.task1="Come Off It";

Obj.Task["task1"]="Come Off It";
```

### delete

```json
delete Obj.Task.task1;

delete Obj.Task["task1"];
```



---



## array

* ```json
  [value0, value1 ,value2,...]
  ```

* type(value) = [int, float, string , bool, array, object, null]+JavaScript_Expression[function, date, undefined]

### loop

```json
for(i in array){
    console.log(array[i]);
}//i is the index

for(i=0; i<array.length; i++){
    console.log(array[i]);
}
```

### nestification

```json
array=[
    "Sinow",
    [
        "Coding",
        "Video Game",
        "Music"
    ],
    {
    	"Like":"Candy",
        "Hate":"Cola"
    }
]
```



### modify

```json
array[1][1]="Homework";
array[2].Like="Git";
```

### delete

```json
delete array[1];
```



---



## JSON.parse()

* JSON.parse(string[, reviver])
* transfer a valid string into an Object or Array
* reviver is a result function

```json
string = '{"name":"Sinow", "Country":"China", "gender":1}';//Be aware of the Single Quotes and Double Quotes usage 
var Obj=JSON.parse(string);
```

* Date and Function cannot be stored in JSON straightly, but can be stored as a String. Later, it can be transfer into Date or Function 

```json
var text = '{ "name":"Runoob", "initDate":"2013-12-14", "site":"www.runoob.com"}';
var obj = JSON.parse(text);
obj.initDate = new Date(obj.initDate);
 
document.getElementById("demo").innerHTML = obj.name + "创建日期: " + obj.initDate;
```

```json
var text = '{ "name":"Runoob", "alexa":"function () {return 10000;}", "site":"www.runoob.com"}';
var obj = JSON.parse(text);
obj.alexa = eval("(" + obj.alexa + ")");
 
document.getElementById("demo").innerHTML = obj.name + " Alexa 排名：" + obj.alexa();
```



---



## JSON.stringify()

* JSON.stringify(value[, replacer[, space]])
* value can be an Object or Array
* replacer:
  * function
    * 调用该function，传入每个成员的键和值，最后使用返回值
    * 若返回值为undefined，则排除该成员
    * value的键是空字符串 ""
  * array
    * ???
* space:
  * 文本添加缩进、空格、换行符
  * 若是数字，则所该指定数目空格，最多10个
  * 可用转移字符\t
* JSON.stringify() transfer Date into String

```json
var obj = { "name":"Runoob", "initDate":new Date(), "site":"www.runoob.com"};
var myJSON = JSON.stringify(obj);
document.getElementById("demo").innerHTML = myJSON;
```

* JSON.stringify() cannot process Function, for it will discard all functions, including key and value. Solution is to use Obj.toString() previously

```json
var obj = { "name":"Runoob", "alexa":function () {return 10000;}, "site":"www.runoob.com"};
obj.alexa = obj.alexa.toString();
var myJSON = JSON.stringify(obj);
 
document.getElementById("demo").innerHTML = myJSON;
```

