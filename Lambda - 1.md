# Lambda - 1
## 語法
下面是一個常見的 Lambda 表示式。
```java
Comparator<String> byLength = (s1, s2) -> s1.length - s2.length;
```

等號左邊是 Lambda 表示式執行完之後的**目標型態 ( Target Type)**，就是最終產生的值得型別；右邊則是 **Lambda 表示式 ( Expression )**。

Lambda 的語法大致上可以分成這幾種情況：
```
|-- 沒有傳入參數
   |-- 有 return
   |-- 沒有 return
|-- 有傳入參數
   |-- 1個參數
   |-- 多個參數
```

##### 沒有傳入參數
當 Lambda 表示式不需要傳入參數，那麼左手邊的小括號 `()` 不可被省略。

* 有回傳值： `() -> "Hello!"`
* 沒有回傳值： `() -> system.out.println("Hello!")`

##### 有傳入參數
有沒有回傳值可以依照上方提到的規則去寫，比較特別的是當只有一個傳入參數的時候，是可以省略小括號的。

* 只有1個傳入參數： `s1 -> system.out.println(s1)`
* 多個傳入參數： `(s1, s2) -> system.out.println(s1 + s2)`

##### 其他
當編譯器可以推斷傳入參數型別時，可以不需要型別宣告，比較常看到的例子就是 `Collections.sort()` 方法的 `Comparator<T>`。

在定義物件比較的方法時，我們通常會去複寫 `Comparator<T>` 介面裡面的 `int compare(T o1, T o2)` 方法。

```java
Comparator<String> byLength = new Comparator<String>(){
    @Override
    public int compare(String s1, String s2){
        return s1.length - s2.length;
    }
}
Collections.sort(stringArr, byLength);
```

改用 Lambda 表示式可以寫成這樣：
```java
Comparator<String> byLength = (String s1, String s2) -> s1.length - s2.length;
Collections.sort(stringArr, byLength);
```

由於前面的 `Comparator<String>` 已經定義了後面參數的行別，所以可以省去定義參數型別的部分。
```java
Comparator<String> byLength = (s1, s2) -> s1.length - s2.length;
Collections.sort(stringArr, byLength);
```

而根據上面的程式碼，Lambda 表示其實可以再簡化，原因在於編譯器可以由 `sort` 方法的第一個參數 `stringArr` 判斷後面的參數行別。
```java
Collections.sort( stringArr,  (s1, s2) -> s1.length - s2.length });
```
我們最後看到的只會剩下傳入參數，以及方法的邏輯。

## 小結
Lambda 幫我們將匿名類別改寫成上面那樣子有什麼好處呢?通常我們使用介面宣告匿名類別，也代表我們不想將關注點放在新增實例物件、為實例物件取名字、定義方法名稱......等等這些事情上。

我們想要知道的只是這個物件裡的方法要傳入什麼參數，以及最後產出我們想要的結果，Lambda 幫我們簡化了過程。

---

## 函式介面 Functional Interface
在 JDK8 之前用現有的 interface 語法來定義函式介面，用途是作為 Lambda 表示式的目標型態。

函式介面指的是僅具有單一抽象方法的介面，許多現存的介面都是函式介面，像是 `Callable`、`Comparator` 以及下面的 `Runnable` 等，都只定一了一個方法。
```java
public interface Runnable {
    void run();
}
```

由於函式介面是僅具單一抽象方法的介面，有時候我們難以直接分辨介面是不是函式介面，且 JDK8 之後對 interface 語法做了演進，允許函式介面內擁有有實體的**預設方法 ( Default Method )**，這將使得判斷介面是不是函式介面更為困難。

不過在 JDK8 也新增了一個標註 `@FunctionalInterface`。這個標註可以幫助我們在打開介面的時候直接判斷是不是函式介面。

如果介面上使用了 `@FunctionalInterface` 來標註，但本身並非函式介面的化，編譯器會報錯。

那麼在 JDK8 之後怎麼樣才算是符合一個函式介面呢?大概可以整理成以下2點：
1. 介面只有一個抽象方法
2. 介面有多個抽象方法，那麼必須將其中一個方法加上 `defualt` 關鍵字，定義為預設方法，並賦予實體 ( 有 `{}` 內的內容 )。

```java
@FunctionalInterface
public interface testInterface {
    void println();
    void print();
}
```
上面這個例子就不是一個函式介面，多個抽象方法應定義 `default` 方法，所以把其中一個方法定義為預設方法就可以通過編譯。

需要注意的是，單純的介面方法是不能擁有實體的，但標註為 `@FunctionalInterface` 的介面中的 `default` 方法可以有實體，下面這個例子就可以通過編譯。
```java
@FunctionalInterface
public interface testInterface {
    default void println(){
        system.out.println("Hello");
    };
    void print();
}
```

## 參考
* Java SE 技術手冊
