#### PHP中 -> 、=>、:: 的区别

##### -> 访问对象或者类的成员（方法，属性），类似前端中的 `.`
- `$obj->value` -> 获取对象的 value 值
- `$obj->value = 1` -> 给对象的 value 属性赋值为 1
- `$obj->get($params)` -> 调用对象的 get 方法,参数 $params
- `new CountrySearch()->search`-> 调用CountrySearch类实例的search属性
- `new CountrySearch()->search($params)`-> 调用CountrySearch类实例化的search()方法,参数 $params

##### =>用来给关联数组赋值
关联数组是一种有序映射, 映射是把 values 关联到 keys 的类型
```php
索引数组: $cars=array("Volvo","BMW","Toyota");
$cars[0]="Volvo";
$cars[1]="BMW";
$cars[2]="Toyota";
```
```php
关联数组: $age=array("Peter"=>"35","Ben"=>"37","Joe"=>"43");
$age['Peter']="35";
$age['Ben']="37";
$age['Joe']="43";
```
##### :: 范围解析操作符
用来访问类的 Static 成员（方法，属性）、类的const 常量
- parent 调用父类中的方法
- self 调用当前类的方法
- 在类外部调用, 使用类名:: Static 成员（方法，属性）调用


#### PHP的命名空间 和 use

##### [命名空间](http://php.net/manual/zh/language.namespaces.importing.php)
类似于当前文件路径的别名，其他文件导入时，直接use别名下的xxx，方便操作。

如果文件没有任何命名空间，就相当于全局空间

只有以下类型的代码: 
类（包括抽象类和traits）、接口、函数和常量。

##### use 使用命名空间
use 使用命名空间中的类
use 命名空间下的类 as 别名， as可以给要使用的类一个别名