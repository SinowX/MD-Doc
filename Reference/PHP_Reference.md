# PHP_Reference

## Basic

### EOF(heredoc)

* To Build A String
* Heredoc 中变量可正常解析，变量不需要用连接符'.'
* 函数不能正常解析

```php
<?php
    echo <<< EOF	//EOF可用其他任意字符替换，常用EOT、EOD、EOF
    	<h1>My Title</h1>
    	<p>My Paragraph</p>
EOF;
//结尾必须用分号，且须从行首开始，前后不能衔接任何空白和字符

    ?>
```

### Array

#### Associative Array

* key-value

```php
//Build a Associaive Array
<?php
   	$age=array("Peter"=>"35","Ben"=>"37","Joe"=>"43");

	$age['Peter']="35";
	$age['Ben']="37";
	$age['Joe']="43";
    ?>
```

* Traverse

```php
<?php
    $age=array("Peter"=>"35","Ben"=>"37","Joe"=>"43");
 
	foreach($age as $x=>$x_value)
	{
	    echo "Key=" . $x . ", Value=" . $x_value;
    	echo "<br>";
	}
    ?>
```

### Super Globals

- $GLOBALS

  - $GLOBALS是一个包含全部全局变量的全局关联数组，变量的名字就是数组的键

  - ```php
    <?php 
    $x = 75; 
    $y = 25;
     
    function addition() 
    { 
        $GLOBALS['z'] = $GLOBALS['x'] + $GLOBALS['y']; 
    }
     
    addition(); 
    echo $z; 
    ?>
    ```

- $_SERVER

  - 包含了头信息、路径以及脚本位置等信息的数组

  - ```php
    <?php 
    echo $_SERVER['PHP_SELF'];
    echo "<br>";
    echo $_SERVER['SERVER_NAME'];
    echo "<br>";
    echo $_SERVER['HTTP_HOST'];
    echo "<br>";
    echo $_SERVER['HTTP_REFERER'];
    echo "<br>";
    echo $_SERVER['HTTP_USER_AGENT'];
    echo "<br>";
    echo $_SERVER['SCRIPT_NAME'];
    ?>
    ```

  - | 元素/代码                       | 描述                                                         |
    | :------------------------------ | :----------------------------------------------------------- |
    | $_SERVER['PHP_SELF']            | 当前执行脚本的文件名，与 document root 有关。例如，在地址为 http://example.com/test.php/foo.bar 的脚本中使用 $_SERVER['PHP_SELF'] 将得到 /test.php/foo.bar。__FILE__ 常量包含当前(例如包含)文件的完整路径和文件名。 从 PHP 4.3.0 版本开始，如果 PHP 以命令行模式运行，这个变量将包含脚本名。之前的版本该变量不可用。 |
    | $_SERVER['GATEWAY_INTERFACE']   | 服务器使用的 CGI 规范的版本；例如，"CGI/1.1"。               |
    | $_SERVER['SERVER_ADDR']         | 当前运行脚本所在的服务器的 IP 地址。                         |
    | $_SERVER['SERVER_NAME']         | 当前运行脚本所在的服务器的主机名。如果脚本运行于虚拟主机中，该名称是由那个虚拟主机所设置的值决定。(如: www.runoob.com) |
    | $_SERVER['SERVER_SOFTWARE']     | 服务器标识字符串，在响应请求时的头信息中给出。 (如：Apache/2.2.24) |
    | $_SERVER['SERVER_PROTOCOL']     | 请求页面时通信协议的名称和版本。例如，"HTTP/1.0"。           |
    | $_SERVER['REQUEST_METHOD']      | 访问页面使用的请求方法；例如，"GET", "HEAD"，"POST"，"PUT"。 |
    | $_SERVER['REQUEST_TIME']        | 请求开始时的时间戳。从 PHP 5.1.0 起可用。 (如：1377687496)   |
    | $_SERVER['QUERY_STRING']        | query string（查询字符串），如果有的话，通过它进行页面访问。 |
    | $_SERVER['HTTP_ACCEPT']         | 当前请求头中 Accept: 项的内容，如果存在的话。                |
    | $_SERVER['HTTP_ACCEPT_CHARSET'] | 当前请求头中 Accept-Charset: 项的内容，如果存在的话。例如："iso-8859-1,*,utf-8"。 |
    | $_SERVER['HTTP_HOST']           | 当前请求头中 Host: 项的内容，如果存在的话。                  |
    | $_SERVER['HTTP_REFERER']        | 引导用户代理到当前页的前一页的地址（如果存在）。由 user agent 设置决定。并不是所有的用户代理都会设置该项，有的还提供了修改 HTTP_REFERER 的功能。简言之，该值并不可信。) |
    | $_SERVER['HTTPS']               | 如果脚本是通过 HTTPS 协议被访问，则被设为一个非空的值。      |
    | $_SERVER['REMOTE_ADDR']         | 浏览当前页面的用户的 IP 地址。                               |
    | $_SERVER['REMOTE_HOST']         | 浏览当前页面的用户的主机名。DNS 反向解析不依赖于用户的 REMOTE_ADDR。 |
    | $_SERVER['REMOTE_PORT']         | 用户机器上连接到 Web 服务器所使用的端口号。                  |
    | $_SERVER['SCRIPT_FILENAME']     | 当前执行脚本的绝对路径。                                     |
    | $_SERVER['SERVER_ADMIN']        | 该值指明了 Apache 服务器配置文件中的 SERVER_ADMIN 参数。如果脚本运行在一个虚拟主机上，则该值是那个虚拟主机的值。(如：someone@runoob.com) |
    | $_SERVER['SERVER_PORT']         | Web 服务器使用的端口。默认值为 "80"。如果使用 SSL 安全连接，则这个值为用户设置的 HTTP 端口。 |
    | $_SERVER['SERVER_SIGNATURE']    | 包含了服务器版本和虚拟主机名的字符串。                       |
    | $_SERVER['PATH_TRANSLATED']     | 当前脚本所在文件系统（非文档根目录）的基本路径。这是在服务器进行虚拟到真实路径的映像后的结果。 |
    | $_SERVER['SCRIPT_NAME']         | 包含当前脚本的路径。这在页面需要指向自己时非常有用。__FILE__ 常量包含当前脚本(例如包含文件)的完整路径和文件名。 |
    | $_SERVER['SCRIPT_URI']          | URI 用来指定要访问的页面。例如 "/index.html"。               |

- $_REQUEST

  - 用于收集HTML表单提交的数据

  - ```html
    <html>
    <body>
     
    <form method="post" action="<?php echo $_SERVER['PHP_SELF'];?>">
    Name: <input type="text" name="fname">
    <input type="submit">
    </form>
    // 当用户通过点击 "Submit" 按钮提交表单数据时, 表单数据将发送至<form>标签中 action 属性中指定的脚本文件
    <?php 
    $name = $_REQUEST['fname']; 
    echo $name; 
    ?>
     
    </body>
    </html>
    ```

  - 

- $_POST

  - 与$_REQUEST 类似，广泛用于收集表单数据

  - method = "post"

  - ```html
    <html>
    <body>
     
    <form method="post" action="<?php echo $_SERVER['PHP_SELF'];?>">
    Name: <input type="text" name="fname">
    <input type="submit">
    </form>
     
    <?php 
    $name = $_POST['fname']; 
    echo $name; 
    ?>
     
    </body>
    </html>
    ```

- $_GET

  - 广泛用于收集表单数据，method = "get"

  - 也可用于收集URL中发送的数据

  - ```html
    <html>
    <body>
    
    <a href="test_get.php?subject=PHP&web=runoob.com">Test $GET</a>
    //点击连接后，参数 "subject" 和 "web" 将发送至"test_get.php",你可以在 "test_get.php" 文件中使用 $_GET 变量来获取这些数据。
    </body>
    </html>
    ```

  - ```php
    <html>
    <body>
     
    <?php 
    echo "Study " . $_GET['subject'] . " @ " . $_GET['web'];
    ?>
     
    </body>
    </html>
    ```

- $_FILES

- $_ENV

- $_COOKIE

- $_SESSION



### 魔术常量

* php提供大量预定义常量

* 很多常量由不同扩展库定义，需要加载这些扩展库

* `__LINE__`

  * 文件中当前行号

* `__FILE__`

  * 文件的绝对路径

* `__DIR__`

  * 文件所在目录
  * 等价于dirname(`__FILE__`)
  * 除非是根目录，否则目录中不包含末尾斜杠

* `__FUNCTION__`

  * 返回函数的名称
  * 返回该函数被定义时的名字，区分大小写

* `__CLASS__`

  * 返回类的名称

  * 返回类被定义时的名字，区分大小写

  * 对trait也起作用

  * ```php
    <?php
    class test {
        function _print() {
            echo '类名为：'  . __CLASS__ . "<br>";
            echo  '函数名为：' . __FUNCTION__ ;
        }
    }
    $t = new test();
    $t->_print();
    ?>
    ```

  * 

* `__TRAIT__`

  * 实现了代码复用

  * 可以进行覆写，类会覆盖trait方法，trait方法会覆盖基类中的方法

  * ```php
    <?php
    class Base {
        public function sayHello() {
            echo 'Hello ';
        }
    }
     
    trait SayWorld {
        public function sayHello() {
            parent::sayHello();
            echo 'World!';
        }
    }
     
    class MyHelloWorld extends Base {
        use SayWorld;
    }
     
    $o = new MyHelloWorld();
    $o->sayHello();
    ?>
    ```

  * ```shell
    Hello World!
    ```

* `__METHOD__`

  * 返回类的方法的名字，区分大小写

  * ```php
    <?php
    function test() {
        echo  '函数名为：' . __METHOD__ ;
    }
    test();
    ?>
    ```

* `__NAMESPACE__`

  * 返回当前命名空间的名称

  * ```php
    <?php
    namespace MyProject;
     
    echo '命名空间为："', __NAMESPACE__, '"'; // 输出 "MyProject"
    ?>
    ```

  * ```shell
    命名空间为："MyProject"
    ```



### OOP

```php
//创建对象
$BMW = new Car();

//类定义
<?php
class phpClass {
  var $var1;
  var $var2 = "constant string";
  
  function myfunc ($arg1, $arg2) {
     [..]
  }
  [..]
}
?>

//调用成员方法
$taobao->setUrl( 'www.taobao.com' );
$google->setUrl( 'www.google.com' );

//构造函数
<?php
class Site {
  /* 成员变量 */
  var $url;
  var $title;

  function __construct( $par1, $par2 ) {
    $this->url = $par1;
    $this->title = $par2;
  }
}

$runoob = new Site('www.runoob.com', '菜鸟教程');
$taobao = new Site('www.taobao.com', '淘宝');
$google = new Site('www.google.com', 'Google 搜索');
?>
    
//析构函数
void __destruct ( void )

<?php
class MyDestructableClass {
   function __construct() {
       print "构造函数\n";
       $this->name = "MyDestructableClass";
   }

   function __destruct() {
       print "销毁 " . $this->name . "\n";
   }
}

$obj = new MyDestructableClass();
?>
    
构造函数
销毁 MyDestructableClass
    
    
//继承
class Child extends Parent {
   // 代码部分
}

//重写
//访问控制
public
private
protected
    

//抽象类
//类中存在至少一个抽象方法，则为抽象类
//抽象类不能被实例化
//仅仅声明了函数调用方式，不能定义具体实现
//继承一个抽象类的时候，子类必须定义父类中的所有抽象方法
//抽象方法在子类中的访问控制须与父类一样或更为宽松，即子类中抽象方法私密级别不得高于父类
//子类方法可以包含父类抽象方法中不存在的可选参数
    
    
 
    
//接口
<?php

// 声明一个'iTemplate'接口
//接口中定义的所有方法都必须是公有，这是接口的特性
//通过 interface 关键字来定义的，就像定义一个标准的类一样，但其中定义所有的方法都是空的
interface iTemplate
{
    public function setVariable($name, $var);
    public function getHtml($template);
}
// 实现接口
class Template implements iTemplate
{
    private $vars = array();
  
    public function setVariable($name, $var)
    {
        $this->vars[$name] = $var;
    }
  
    public function getHtml($template)
    {
        foreach($this->vars as $name => $value) {
            $template = str_replace('{' . $name . '}', $value, $template);
        }
 
        return $template;
    }
}


//常量
const constant = '常量值';


//static
//可以不实例化类而直接访问
//静态属性不能通过一个类已实例化的对象来访问（但静态方法可以）
//伪变量 $this 在静态方法中不可用
//静态属性不可以由对象通过 -> 操作符来访问
//可以用一个变量来动态调用类。但该变量的值不能为关键字 self，parent 或 static
<?php
class Foo {
  public static $my_static = 'foo';
  
  public function staticValue() {
     return self::$my_static;
  }
}

print Foo::$my_static . PHP_EOL;
$foo = new Foo();

print $foo->staticValue() . PHP_EOL;
?>    

//Final
//如果一个类被声明为 final，则不能被继承
//如果父类中的方法被声明为 final，则子类无法覆盖该方法

    

    
    
    
//调用父类构造方法
//需要在子类的构造方法中调用 parent::__construct() 
<?php
class BaseClass {
   function __construct() {
       print "BaseClass 类中构造方法" . PHP_EOL;
   }
}
class SubClass extends BaseClass {
   function __construct() {
       parent::__construct();  // 子类构造方法不能自动调用父类的构造方法
       print "SubClass 类中构造方法" . PHP_EOL;
   }
}
class OtherSubClass extends BaseClass {
    // 继承 BaseClass 的构造方法
}

// 调用 BaseClass 构造方法
$obj = new BaseClass();

// 调用 BaseClass、SubClass 构造方法
$obj = new SubClass();

// 调用 BaseClass 构造方法
$obj = new OtherSubClass();
?>
```

