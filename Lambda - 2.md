# Lambda - 2
Lambda 表示式並不是匿名類別的語法糖，這在遇到 `this` 及 `final` 時會有明顯的差異。以下例子是沒有用 Lambda 表示式的測試範例，執行結果會是如何呢?
```java
import static java.lang.System.out;

class Hello {
    Runnable r1 = new Runnable() {
        @Override
        public void run() {
            out.println(this);
        }
    };

    Runnable r2 = new Runnable() {
        @Override
        public void run() {
            out.println(toString());
        }
    };

    public String toString() {
        return "Hello, world!";
    }
}

public class ThisDemo {
    public static void main(String[] args) {
        Hello hello = new Hello();
        hello.r1.run();
        hello.r2.run();
    }
}
```

印出來的結果如下：
![ ](/images/lambda-2.1.png)

`this` 指向 `new` 出來的 Runnable 物件，所以 `r1` 印出的是自己的記憶體位址。由於 Runnable 物件並沒有覆寫 `toString()` 方法，所以 `r2` 印出的也是記憶體位址。
![ ](/images/lambda-2.3.png)

如果將 `r1` 及 `r2` 的方法以 Lambda 表示式改寫的話，結果又會是如何?

```java
package lambda_expression_test;

import static java.lang.System.out;

class Hello2 {
    Runnable r1 = () -> out.println(this);
    Runnable r2 = () -> out.println(toString());

    public String toString() {
        return "Hello, world!";
    }
}

public class ThisDemo2 {
    public static void main(String[] args) {
        Hello2 hello = new Hello2();
        hello.r1.run(); 
        hello.r2.run();
    }
}
```
結果印出來的是兩個 Hello, world!
![ ](/images/lambda-2.2.png)

上述的例子可以證明 Lambda 表示式不是匿名類別的語法糖。而 Lambda 表示式本體中的 `this` 及 `toString()` 的對象，是來自 Lambda 的周圍環境 ( Context )，也就是 Lambda 表示式是在哪個名稱範疇 ( Scope ) ，它被定義在哪邊，就會參考到包住它的物件，以上面的例子來說就是 `Hello2` 物件。 

![ ](/images/lambda-2.4.png)

另外，在函式介面中的區域變數本身等效於 `final` 區域變數，可以省略 `final`，但不能在 Lambda 表示式不能修改區域變數的值。

```java
String[] names = {"Justin", "Monica", "Irene"};
Runnable runnable = () -> {
    for(String name : names){
        out.println(name);
    }
};
```

## 小結
* 在 Lambda 表示式中，`this` 是以詞彙範圍來決定指向的物件，也就是 Lambda 表示式被哪個物件包住，就指向誰。
* 函式介面中的區域變數等效於家 `final` 的變數，但值不可被修改。

## 參考
* Java SE 技術手冊
