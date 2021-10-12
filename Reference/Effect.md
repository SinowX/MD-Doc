## Effect

* hide() and show()

  ```js
  $("#hide").click(function(){
    $("p").hide();
  });
   
  $("#show").click(function(){
    $("p").show();
  });
  
  $(selector).hide(speed,callback);
  
  $(selector).show(speed,callback);
  ```

* toggle()

  ```js
  $("button").click(function(){
    $("p").toggle();
  });
  
  $(selector).toggle(speed,callback);
  ```

* fade

  ```js
  $(selector).fadeIn(speed,callback);
  $(selector).fadeOut(speed,callback);
  $(selector).fadeToggle(speed,callback);
  $(selector).fadeTo(speed,opacity,callback);
  
  $("button").click(function(){
    $("#div1").fadeIn();
    $("#div2").fadeIn("slow");
    $("#div3").fadeIn(3000);
  });
  
  $("button").click(function(){
    $("#div1").fadeOut();
    $("#div2").fadeOut("slow");
    $("#div3").fadeOut(3000);
  });
  
  $("button").click(function(){
    $("#div1").fadeToggle();
    $("#div2").fadeToggle("slow");
    $("#div3").fadeToggle(3000);
  });
  
  $("button").click(function(){
    $("#div1").fadeTo("slow",0.15);
    $("#div2").fadeTo("slow",0.4);
    $("#div3").fadeTo("slow",0.7);
  });
  ```

* slide

  ```js
  $(selector).slideDown(speed,callback);
  $(selector).slideUp(speed,callback);
  $(selector).slideToggle(speed,callback);
  
  $("#flip").click(function(){
    $("#panel").slideDown();
  });
  
  $("#flip").click(function(){
    $("#panel").slideUp();
  });
  
  $("#flip").click(function(){
    $("#panel").slideToggle();
  });
  ```

* animate

  ```js
  $(selector).animate({params},speed,callback);
  //必需的 params 参数定义形成动画的 CSS 属性。
  //可选的 speed 参数规定效果的时长。它可以取以下值："slow"、"fast" 或毫秒。
  //可选的 callback 参数是动画完成后所执行的函数名称。
  
  $("button").click(function(){
    $("div").animate({left:'250px'});
  });
  
  $("button").click(function(){
    $("div").animate({
      left:'250px',
      opacity:'0.5',
      height:'150px',
      width:'150px'
    });
  });
  
  $("button").click(function(){
    $("div").animate({
      left:'250px',
      height:'+=150px',
      width:'+=150px'
    });
  });
  
  $("button").click(function(){
    $("div").animate({
      height:'toggle'
    });
  });
  
  $("button").click(function(){
    var div=$("div");
    div.animate({height:'300px',opacity:'0.4'},"slow");
    div.animate({width:'300px',opacity:'0.8'},"slow");
    div.animate({height:'100px',opacity:'0.4'},"slow");
    div.animate({width:'100px',opacity:'0.8'},"slow");
  });
  
  
  $("button").click(function(){
    var div=$("div");
    div.animate({left:'100px'},"slow");
    div.animate({fontSize:'3em'},"slow");
  });
  ```

* stop

  ```js
  $(selector).stop(stopAll,goToEnd);
  可选的 stopAll 参数规定是否应该清除动画队列。默认是 false，即仅停止活动的动画，允许任何排入队列的动画向后执行。
  可选的 goToEnd 参数规定是否立即完成当前动画。默认是 false。
  
  
  $("#stop").click(function(){
    $("#panel").stop();
  });
  
  ```

* Callback 函数在当前动画 100% 完成之后执行

  ```js
  $("button").click(function(){
    $("p").hide("slow",function(){
      alert("段落现在被隐藏了");
    });
  });
  ```

* Chaining

  ```js
  在相同的元素上运行多条 jQuery 命令，一条接着另一条
  
  $("#p1").css("color","red")
    .slideUp(2000)
    .slideDown(2000);
  
  ```


## HTML DOM

* text() - 设置或返回所选元素的文本内容

* html() - 设置或返回所选元素的内容（包括 HTML 标记）

* val() - 设置或返回表单字段的值

  ```js
  //获取值
  $("#btn1").click(function(){
    alert("Text: " + $("#test").text());
  });
  $("#btn2").click(function(){
    alert("HTML: " + $("#test").html());
  });
  $("#btn1").click(function(){
    alert("值为: " + $("#test").val());
  });
  
  //改变值
  $("#btn1").click(function(){
      $("#test1").text("Hello world!");
  });
  $("#btn2").click(function(){
      $("#test2").html("<b>Hello world!</b>");
  });
  $("#btn3").click(function(){
      $("#test3").val("RUNOOB");
  });
  
  //回调函数
  $("#btn1").click(function(){
      $("#test1").text(function(i,origText){
          return "旧文本: " + origText + " 新文本: Hello world! (index: " + i + ")"; 
      });
  });
   
  $("#btn2").click(function(){
      $("#test2").html(function(i,origText){
          return "旧 html: " + origText + " 新 html: Hello <b>world!</b> (index: " + i + ")"; 
      });
  });
  ```

* attr() - 设置或返回属性值

  ```js
  //返回属性值
  $("button").click(function(){
    alert($("#runoob").attr("href"));
  });
  
  //设置属性值
  $("button").click(function(){
    $("#runoob").attr("href","http://www.runoob.com/jquery");
  });
  $("button").click(function(){
      $("#runoob").attr({
          "href" : "http://www.runoob.com/jquery",
          "title" : "jQuery 教程"
      });
  });
  
  //回调函数
  $("button").click(function(){
    $("#runoob").attr("href", function(i,origValue){
      return origValue + "/jquery"; 
    });
  });
  ```

* append() - 在被选元素的结尾插入内容

* prepend() - 在被选元素的开头插入内容

* after() - 在被选元素之后插入内容

* before() - 在被选元素之前插入内容

  ```js
  function appendText(){
      var txt1="<p>文本-1。</p>";              // 使用 HTML 标签创建文本
      var txt2=$("<p></p>").text("文本-2。");  // 使用 jQuery 创建文本
      var txt3=document.createElement("p");
      txt3.innerHTML="文本-3。";               // 使用 DOM 创建文本 text with DOM
      $("body").append(txt1,txt2,txt3);        // 追加新元素
  }
  
  function afterText()
  {
      var txt1="<b>I </b>";                    // 使用 HTML 创建元素
      var txt2=$("<i></i>").text("love ");     // 使用 jQuery 创建元素
      var txt3=document.createElement("big");  // 使用 DOM 创建元素
      txt3.innerHTML="jQuery!";
      $("img").after(txt1,txt2,txt3);          // 在图片后添加文本
  }
  ```

* remove() - 删除被选元素（及其子元素）

* empty() - 从被选元素中删除子元素

  ```js
  $("#div1").remove();
  $("#div1").empty();
  
  //过滤删除
  $("p").remove(".italic");
  ```

## CSS

- addClass() - 向被选元素添加一个或多个类

- removeClass() - 从被选元素删除一个或多个类

- toggleClass() - 对被选元素进行添加/删除类的切换操作

  ```js
  $("button").click(function(){
    $("h1,h2,p").addClass("blue");
    $("div").addClass("important");
  });
  
  $("button").click(function(){
    $("body div:first").addClass("important blue");
  });
  
  $("button").click(function(){
    $("h1,h2,p").removeClass("blue");
  });
  
  $("button").click(function(){
    $("h1,h2,p").toggleClass("blue");
  });
  ```

- css() - 设置或返回样式属性

  ```js
  //返回CSS属性
  $("p").css("background-color");
  
  //设置CSS属性
  $("p").css("background-color","yellow");
  $("p").css({"background-color":"yellow","font-size":"200%"});
  
  ```

- 返回或设置尺寸

  - width()

  - height()

  - innerWidth()

  - innerHeight()

  - outerWidth()

  - outerHeight()

    ```js
    $("button").click(function(){
      var txt="";
      txt+="div 的宽度是: " + $("#div1").width() + "</br>";
      txt+="div 的高度是: " + $("#div1").height();
      $("#div1").html(txt);
    });
    
    $("button").click(function(){
      var txt="";
      txt+="div 宽度，包含内边距: " + $("#div1").innerWidth() + "</br>";
        txt+="div 高度，包含内边距: " + $("#div1").innerHeight();
      $("#div1").html(txt);
    });
    
    $("button").click(function(){
      var txt="";
      txt+="div 宽度，包含内边距和边框: " + $("#div1").outerWidth() + "</br>";
      txt+="div 高度，包含内边距和边框: " + $("#div1").outerHeight();
      $("#div1").html(txt);
    });
    ```

## Traverse

- parent() - 返回被选元素的直接父元素

- parents() - 返回被选元素的所有祖先元素，它一路向上直到文档的根元素 (<html>)

- parentsUntil() - 返回介于两个给定元素之间的所有祖先元素

  ```js
  $(document).ready(function(){
    $("span").parents();
  });
  
  $(document).ready(function(){
    $("span").parents("ul");
  });
  
  $(document).ready(function(){
    $("span").parentsUntil("div");
  });
  ```

- children() - 返回被选元素的所有直接子元素

- find() - 返回被选元素的后代元素，一路向下直到最后一个后代

  ```js
  $(document).ready(function(){
    $("div").children();
  });
  
  $(document).ready(function(){
    $("div").children("p.1");
  });
  
  $(document).ready(function(){
    $("div").find("span");
  });
  
  $(document).ready(function(){
    $("div").find("*");
  });
  ```

- siblings() - 返回被选元素的所有同胞元素

- next() - 返回被选元素的下一个同胞元素

- prev() - 返回被选元素的上一个同胞元素

  ```js
  $(document).ready(function(){
    $("h2").next();
  });
  ```

- nextAll() - 返回被选元素的所有跟随的同胞元素

- prevAll() - 返回被选元素的所有之前的同胞元素

  ```js
  $(document).ready(function(){
    $("h2").nextAll();
  });
  ```

- nextUntil() - 返回介于两个给定参数之间的所有跟随的同胞元素

- prevUntil()

  ```js
  $(document).ready(function(){
    $("h2").nextUntil("h6");
  });
  ```

## Filter

* first() 方法返回被选元素的首个元素。

* last() 方法返回被选元素的最后一个元素

* eq() 方法返回被选元素中带有指定索引号的元素

* filter() 方法允许您规定一个标准。不匹配这个标准的元素会被从集合中删除，匹配的元素会被返回

* not() 方法返回不匹配标准的所有元素。

  ```js
  $(document).ready(function(){
    $("div p").first();
  });
  
  $(document).ready(function(){
    $("div p").last();
  });
  
  $(document).ready(function(){
    $("p").eq(1);
  });
  
  $(document).ready(function(){
    $("p").filter(".url");
  });
  
  $(document).ready(function(){
    $("p").not(".url");
  });
  ```

  