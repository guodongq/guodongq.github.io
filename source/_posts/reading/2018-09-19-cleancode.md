---
layout: post
title:  "Clean Code!"
date:   2018-09-19 10:22:16 +0800
tags: ["refactor", "clean_code"]
published: true
comments: true
script: [post.js]
excerpted: |
    Clean Code ...
day_quote:
  title: The word of the day!
  description: |
    Put a very powerful message.
---
<!--more-->

# 一、整洁代码

## 1 为什么阅读这本书？
* 你是个程序员
* 你想成为更好的程序员

## 2 读完本书，获得的技能？
* 知道更多关于代码的事儿
* 辨别好代码和糟糕代码的差异
* 学会如何写出好代码
* 学会如何将糟糕代码改成好代码

## 3 写糟糕代码的理由与代价
* 理由
  + 为了快点完成而赶时间；
  + 只是单纯的为了实现结果，哪怕你的代码过程怎么样都不重要，当结果呈现出你想象中的样子，你便弃之而不顾，可能当你回头再看你写的代码时，你都忘记是怎样的思路;
  + 随便先写点代码出来，结果出现后稍后再整理;
* 代价
  + 久而久之，以你习惯写出的代码，在团队中，你可能会被嫌弃;
  + 可能会因为你的原因拖延整个项目组的时间；
  + 需求变了，你像重新来过一样再改，重复重复，这团乱麻越来越大，直到束手无策，团队生产力下降。


## 4 怎么是整洁的代码？
* Bjarne Stroustrup（C++发明者）
  + 我喜欢优雅和高效的代码，代码逻辑应当直接了当，叫缺陷难以隐藏；
  + 尽量减少依赖关系，使之便于维护；
  + 依据某种分层战略完善错误处理代码；性能调至最优，省得引诱别人做没有必要的优化，搞出一堆混乱来，整洁的代码只做好一件事。

* Ron Jeffries
  + 能通过所有的测试；
  + 没有重复代码；
  + 体现系统中的全部设计理念；
  + 包含尽量少的实体、比如类、方法、函数等。

## 5 总结

### 5.1 整洁代码符合以下目标
* 1.职责明确，没有多余；
* 2.减少依赖，便于维护；
* 3.高效。

### 5.2 整洁代码的建议
* 有意义的命名，代码应通过其字面表达含义；
* 没有重复代码；
* 全面覆盖的单元测试；
* 提供清晰、尽量少的API；
* 完美的错误处理代码，不要忽略细节；
* 性能调至最优，省得引诱别人做没规矩的优化；
* 尽量少的依赖关系，便于维护；
* 尽量少的实体，比如类、方法、函数等；
* 整洁的代码（类/函数/模块）只做好一件事；
* 整洁的代码看起来专为解决那个问题而存在；
* 整洁的代码简单直接，从不隐藏设计者的意图；
* 整洁的代码看起来像是某位特别在意她的人写的，没有改进的余地；
* 整洁的代码浅显易懂，绝不故作高深；

### 5.3 小结
每一位程序员就像是位艺术家，把写代码当做自己在雕刻一件艺术品，一定要美观到最精致，呈现给人们一种赏心悦目的景象。

# 二、有意义的命名
变量、函数、参数、类、包、文件。很多地方都需要命名，怎么样命名才能简洁明了。
## 1. 名副其实
选个好名字要花时间，但省下来的时间比花掉的多。一旦有好的命名，就换掉旧的。
### 1.1 如果名称需要注释来补充，那么说明名字没取好
```java
// 差的命名
int d;          // 消逝的时间，以日计算。

// 好的命名
int elapsedTimeInDays;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays;
```
### 1.2 选择体现本意的名称能让人更容易理解和修改代码
```java
public List<int[]> getThem() {
    List<int[]> list1 = new ArrayList<int[]>();
    for (int[] x : theList) {
        if (x[0] == 4) {
            list1.add(x);
        }
    }
    return list1;
}
```
上面代码的问题：
+ theList中是什么类型的东西
+ theList零下标条目的意义是什么
+ 值4的意义是什么
+ 我怎么使用返回的列表

问题的答案没体现在代码段中，而这本就是它们应该在的地方。比如，我们正在开发一款扫雷游戏，我们发现盘面是名为theList的单元格列表，那就将其名称改为gameBoard。

盘面上每个单元格都用一个简单数组表示。我们还发现，零下标条目是一种状态的值，而这种状态值为4表示为“已标记”。只要改为有意义的名称，代码就会得到相当程度的改进。
```java
public List<int[]> getFlaggedCells() {
    List<int[]> flaggedCells = new ArrayList<>();
    for (int[] cell : gameBoard) {
        if (cell[STAUS_VALUE] == FLAGGED) {
            flaggedCells.add(cell);
        }
    }
    return flaggedCells;
}
```
还可以更进一步，不用int数组表示单元格，而是另写一个类。该类包括一个名副其实的函数（称为isFlagged），从而掩盖住那个魔术数（就是那个4）。于是得到函数的新版本：
```java
public List<Cell> getFlaggedCells() {
    List<Cell> flaggedCells = new ArrayList<>();
    for (Cell cell : gameBoard) {
        if (cell.isFlagged()) {
            flaggedCells.add(cell);
        }
    }
    return flaggedCells;
}
```

## 2 避免误导
程序员必须避免留下掩盖代码本意的错误线索。应当避免使用与本意相悖的词。

别用accountList来指称一组账号，除非它真的是List类型（即便容器就是一个List，最好也别在名称中写出容器类型名）。如果包含账号的容器并非真是个List，就会引起错误的判断。所以，用accountGroup或bunchOfAccounts，甚至直接用accounts都会好一些。

## 3 做有意义的区分
```java
public static void copyChars(char a1[],char a2[]){
for(int i=0;i<a1.length;i++){
    a2[i]=a1[i];
    }
}
```
如果参数名称改为source和destination ，这个函数就会像样很多。

废话都是冗余的，Variable一词 永远不应当出现在变量名中;    
Table一词永远不应当出现在表名中;  
NameString 会比 Name好吗，难道Name 会是一个浮点数不成？  
如有一个Customer的类，有又一个CustomerObject的类。是不是就凌乱了。  

## 4 使用读的出来的名称
能读出来的名称更容易记忆。如果名称读不出来，讨论的时候就像个傻鸟。
```java
Date genymdhms // 生成日期，年、月、日、时、分、秒

Date generationTimestamp;
```

## 5 使用可搜索的名称
找`MAX_CLASSES_PER_STUDENT`比找数字7要容易得多。同样字母e也不是个便于搜索的变量名。因为太常见了。

单字母名称仅用于短方法中的局部变量。名称长短应与其作用域大小相对应。如果变量或常量可能在代码中多处使用，则应赋予其以便于搜索的名字。

## 6 避免将类型或作用域编进名称中
+ 匈牙利语标记法（Hungarian Notation,HN）
早期编译器不做类型检查，程序员需要用匈牙利语标记法（Hungarian Notation,HN）来帮助自己记住类型

例如szCmdLine的前缀sz代表string end of zero.以0 结尾的字符串。strPhone,代表Phone是字符串类型。

而今，大部分语言是**强类型**的，代码编辑环境都已经先进到在编译开始前就侦测到类型错误的程度！所以匈牙利语标记法和其他类似的格式编码都多余了。

匈牙利语标记法加了修改变量，函数，或类的名称或类型的难度。
```java
PhoneNumber phoneString;
//类型变化时，名称并未变化。
```
+ 成员前缀
也不必用 m_ 前缀来标明成员变量。应当把类和函数做得足够小，消除对成员前缀的需要。

+ 接口和实现
接口前导字母I被滥用。

## 7 类名应该是名词或名词短语，方法名应该是动词或者动词短语
类名如Customer、WikiPage、Account。避免使用Manager、Processor、Data或Info这样的类名

方法名如postPayment、deletePage或save。属性访问器、修改器或断言应该根据其值命名，并依Javabean标准加上get、set和is前缀。

## 8 同一概念对应统一个词
比如get、fetch、retrieve表达的含义差不多，尽量保持多个类使用同一种写法。

Controller、Manager、Driver含义相近，尽量保持一致。

## 9 不用双关语
避免将同一个单词用于不同的目的。代码作者应该尽力写出易于理解的代码。

比如add、insert、append之间的区别要搞清楚，对于方法的命名要清晰。

## 10 使用解决方案领域名称
代码是给程序员读的，用计算机领域类的术语来命名是一个很好的做法。比如AccountVisitor、JobQueue这种。

如果不能用程序员熟悉的术语来命名，就采用所涉问题的领域名称命名。

## 11 添加有意义的语境、不要添加没用的语境
如果你有命名良好的类、函数或命名空间来放置名称，给读者提供语境是最好不过了。如果没有，就用最后一招———给名称添加前缀。

比如，你有名为firstName、lastName、street、hourseNumber、city、state变量，搁一块儿的时候，很明确是一个地址。如果只是在一个方法中单独的看到一个state变量呢，你会理所当然得推断是一个地址吗？

可以添加前缀addrFirstName、addrLastName、addrState来提供语境。

假设有一个名为“加油站豪华版”(Gas Station Deluxe)的应用，在其中给每个类添加GSD前缀就不是什么好点子了。

# 三、函数
## 1 短小
函数的第一个规则就是要短小。短小才精悍，浓缩的都是精华。函数短小的好处：
+ 对于大部分大脑不是特别发达的人，10个10行的函数可能比一个100行的函数理解起来更容易。
+ 如果给每个“小函数”能起一个具有说明性的名字，那么整个流程读下来会更清晰，而且可以增加代码自解释的作用。
+ 短小的函数便于在电脑屏中完整的显示出来，不需要上下去滚动屏幕。
+ 函数二十行最佳。
+ 函数的缩进层级不应该超过二层。

## 2 只做一件事
函数应该做好一件事，做好这件事，只做这件事---单一职责原则

## 3 每个函数一个抽象层级
让代码拥有自顶向下的阅读顺序，函数中的语句都要在同一个抽象层级上。

函数中混杂不同抽象层级，让人迷惑。读者可能无法判断某个表达式是基础概念还是细节。

## 4 switch语句不可避免，使用多态隐藏到较低的抽象层
switch语句天生就是要做N件事，不幸地是我们总无法避开，不过还是能够确保每个switch语句都埋藏在较低的抽象层，而且永远不重复。

## 5 使用描述性的名称
花时间给函数取个好的具有描述性的名字是很值得的，不要害怕长名字。函数功能越集中、越短小越容易取名字，如果你发现你对你的函数很难取名字的时候，是该考虑这个函数是否需要进行拆分了。

## 6 函数参数
最理想的参数数量是零，其次是一，再次是二，应尽量避免三。有足够特殊的理由才能用三个以上参数——所以无论如何也不要这么做。

函数尽量不要有标识参数。向函数传入boolean值简直就是骇人听闻的做法。

如果函数看起来需要两个、三个或三个以上的参数，就说明其中一些参数应该封装为类了。例如，下面两个声明的差别：
```java
Circle makeCircle(double x, double y, double radius);
Circle makeCircle(Point center, double radius);
```

## 7 无副作用
副作用是一种谎言。函数承诺只做一件事，但还是会做其他被藏起来的事。

## 8 使用异常代替返回错误码
返回错误码可能导致更深层次的嵌套结构。当返回错误码时就是在要求调用者立即处理错误。如果使用异常代替返回错误码，错误处理代码就能从主路径代码中分离出来，得到简化。
```java
// 嵌套结构层次深
if (deletePage(page) == E_OK) {
    if (registry.deleteReference(page.name) == E_OK) {
        if (configKeys.deleteKey(page.name.makeKey()) == E_OK) {
            logger.log("page deleted");
        } else {
            logger.log(configKey not deleted);
        }
    } else {
        logger.log("deleteReference from registry failed");
    }
} else {
    logger.log("deleted failed");
    return E_ERROR;
}

//  使用异常处理代替返回错误码
try {
    deletePage(page);
    registry.deleteReference(page.name);
    configKeys.deleteKey(page.name.makeKey());
} catch (Exception e) {
    logger.log(e.getMessage());
}
```

## 9 错误处理就是一件事
函数应该只做一件事。错误处理就是一件事。因此，处理错误的函数不该做其他事。意味着如果关键字try在某个函数中存在，它就该是这个函数的第一个单词，而且在catch/finaly代码块后面也不该有其他内容。

## 10 Error.java 依赖磁铁
返回错误码通常意味着某处有个类或是枚举，定义了所有错误码。
```java
public enum Error {
    OK,
    INVALID,
    NO_SUCH,
    LOCKED,
    OUT_OF_RESOURCES,
    WAITING_FOR_EVENT,
}
```
这样的类就是一块依赖磁铁，其他许多类都得导入和使用它。当Error枚举修改时，所有这些其他类都需要重新编译和部署。

使用异常替代错误码，新异常就可以从异常类派生出来，无需重新编译或重新部署。

## 11 别重复自己
重复可能是软件中一切邪恶的根源。许多原则与实践规则都是为了控制与消除重复而创建的。

## 12 结构化编程
Dijkstra认为，每个函数、函数中的每个代码块都应该有一个入口、一个出口。遵循这些规则，意味着在每个函数中只该有一个return语句，循环中不能有break或continue语句，而且永永远远不能有任何goto语句。

这个规范对于小函数帮助不大，只有在大函数中才有明显的好处。

## 13 如何写出这样的函数
跟写文章一样，先想什么就写什么，然后不断打磨。初稿也许粗陋无序，你就斟酌推敲，直至达到你心目中的样子。



# 四、注释
若编程语言足够有表达力，或者我们擅长于用这些语言来表达意图，就不那么需要注释---也许根本不需要。

注释的恰当用法是弥补我们在用代码表达意图时遭遇的失败。如果你发现自己需要写注释，再想想看是否有办法通过代码来表达。


## 1 注释不能美化糟糕的代码
少量而准确是注释比大量而毫无意义的注释更有用。大量无用的注释会打乱阅读代码时的思路，也会增加滚动屏幕的成本。

## 2 用代码来阐述
有时候，代码本身不足以解释其行为。幸运的是，很多时候我们都可以把代码写地能解释自己的行为。
```java
// Check to see if the employee is eligible for full benefits
if ((employee.flags & HOURLY_DAY) &&  (employee.age > 65))
```
改成这样怎么样？
```java
if (employee.isEligibleForFullBenefits())
```

## 3 好注释
有些注释是必须的，也是有利的。不过要记住，唯一真正的好注释是你想办法不去写的注释。
+ 法律信息
  版本及著作权声明就是有理由在每个源文件开头注释处放置的内容
+ 提供信息的注释
  ```java
  // format matched kk:mm:ss EEE, MMM dd, yyyy
  Pattern timeMatcher = Pattern.compile("\\d*:\\d*:\\d* \\w*, \\w* \\d*, \\d*");
  ```
  注释说明，该正则表达式意在匹配一个经由SimpleDateFormat.format函数利用特定格式字符串格式化的时间和日期。

  这类注释有时管用，但更好的方式是尽量利用函数名称表达信息。
+ 对意图的解释。注释可以提供某个决定背后的故事。
+ 阐释。注释可以解释某些难懂的参数或返回值的意义。
+ 警示。有时，用于警示其他程序员会出现某种后果的注释也是有用的。
+ TODO注释。TODO大多都是程序员的自我安慰、自欺欺人。
+ 放大。可以用来放大某种不合理之物的重要性。
+ 公共API中的Javadoc

## 4 坏注释
大多数注释都属此类。通常，坏注释都是糟糕代码的支撑或借口，或者对错误决策的修正，基本上等于程序员自说自话。
+ 喃喃自语
  写一些只有作者自己知道含义的注释，就是喃喃自语。

+ 多余的注释
  相比代码本身无法提供更过的信息就是多余的注释。比如没有证明代码的意义，也没有给出代码的意图或逻辑。读这种注释还不如读代码。

+ 误导性注释
  注释不够精确，和代码本身表达的含义不一致，容易误导程序员。

+ 循规式注释
  谓每个函数都要有Javadoc或每个变量都要有注释的规矩全然是愚蠢可笑的。这类注释突然让代码变得散乱，满口胡言，令人迷惑不解。

  ```java
    /**
     * @param title The title of the CD
     * @param author The author of the CD
     * @param tracks The number of tracks on the CD
     * @param durationInMinutes The duration of the CD in minutes
     */
    public void addCD(String title, String author, int tracks, int rationInMinutes) {
       CD cd = new CD();
       cd.title = title;
       cd.author = author;
       d.tracks = tracks;
       cd.duration = durationInMinutes;
       cdList.add(cd);
    }
  ```

+ 日志式注释
  有人会在每次编辑代码时，在模块开始处添加一条注释。这类注释就像是一种记录每次修改的日志。

  很久以前，在模块开始处创建并维护这些记录还算有道理。那时，我们还没有源代码控制系统可用。如今，这种冗长的记录只会让模块变得凌乱不堪，应当全部删除。  

+ 废话注释
  对显然之事喋喋不休，毫无新意。
  ```java
  
  /**
   * Default constructor
   */
   protected AnnualDateRule() {
   }

  /** The day of the month. */
   private int dayOfMonth;

  /**
   * Returns the day of the month
   * @return the day of the month
   */
   public int getDayOfMonth() {
      return dayOfMonth;
   }
  ```

+ 可怕的废话
  Javadoc也可能是废话。下列Javadoc(摘自某知名开源库)的目的是什么？ 答案：无。
  ```java
  /** The name. */
  private String name;

  /** The version */
  private String version;

  /** The licenseName */
  private String licenseName;

  /** The version. */
  private String info;
  ```
+ 能用函数或变量时就别用注释。体现了要使名称更具表达性，能表达它自身的意图。
```java
// does the module from the global list <mod> depend on the
// subsystem we are part of ?
if (smodule.getDependSubsystems().contains(subSysMod.getSubSystem()))
```
可以改成以下没有注释的版本：
```java
ArrayList moduleDependees = smodule.getDependSubsystems();
String ourSubSystem = subSysMod.getSubSystem();
if (moduleDependees.contains(ourSubSystem))
```

+ 位置标记。#######################################之类的。
有时，程序员喜欢在源代码中标记某个特别位置。例如：
```
// Actions //////////////////////////////////////////
```

+ 括号后面的注释。通过注释来表明这个括号与哪个括号是一对的。
有时，程序员会在括号后面放置特殊的注释，尽管这对于含有深度嵌套结构的长函数可能有意义，但只会给我们更愿意编写的短小、封装的函数带来混乱。如果你发现自己想标记右括号，其实应该做的是缩短函数。

+ 归属与署名。同样应该是代码控制系统应该做的事。
```
/* Added by Rick */
```
版本控制系统非常善于记住是谁在何时添加了什么。没必要用小小的签名搞脏代码。

+ 注释掉的代码。
  直接把代码注释掉是讨厌的做法。别这么干！

  ```java
    InputStreamResponse response = new InputStreamResponse();
    resonpse.setBody(formatter.getResultStream(), formatter.getByteCount());
    // InputStream resultsStream = formatter.getResultStream();
    // StreamReader reader = new StreamReader(resultStream);
    // response.setContent(reader.read(formatter.getByteCount()));
  ```

+ HTML注释
源码中包含HTML注释让人讨厌，难以阅读。

+ 非本地信息。
假如你一定要写注释，请确保它描述了离它最近的代码。别在本地注释的上下文环境中包含出系统级的信息。
下面的例子，除了可怕的冗余之外，还包含了系统级的默认端口信息。但是这个函数完全没控制那个所谓的默认值。假如那个值更改了，无法担保这个注释也会跟着修改

  ```java
  /**
   * port on which fitnesse would run. Default to <b>8082</b>
   * @param fitnessePort
   */
  public void setFitnessePort(int fitnessPort) {
      this.fitnessePort = fitnessePort;
  }
  ```

+ 信息过多
别在注释中添加有趣的历史性话题或者无关的细节描述。


+ 不明显的联系
注释及其描述的代码之间的联系应该是显而易见。如果你不嫌麻烦要写注释，至少让读者能看着注释和代码，并且理解注释所谈何物。

注释应该起着辅助的作用，而不应该“喧宾夺主”。好的注释在于提供有用信息、或者方便程序员获取有用信息。而坏的信息在于冗余，甚至是错误，一般它们对提高对系统的认识不会提供任何帮助，反而会分散注意力。

# 五、格式
## 1.目的
- 代码格式关乎沟通，而沟通是开发者的头等大事。
- 代码风格&可读性影响到可维护性&扩展性。



## 2.垂直格式
### 2.1 Java源代码文件有多大？（以如下代码为例，多数为200行，不超过500行）
- 短文件通常比长文件更容易理解。

### 2.2 垂直方向的区隔（空白行）
以如下代码为例，每个协程之间，都有空白行隔开。每个空白行都是一条线索，标识出新的独立概念。
```
//GBASEUP results
go func() {
   res, err := ReqProForResults(fmt.Sprintf(ProUrl, PaasProAddress, GBASE_INDEX_UP))
   upResults <- Ack{Result: res, Error: err}
}()

//GBASEUP results
go func() {
   res, err := ReqProForResults(fmt.Sprintf(ProUrl, PaasProAddress, GBASE_INDEX_NODE_STATUS))
   statusResults <- Ack{Result: res, Error: err}
}()

//cluster results
go func() {
   res, err := ReqProForResults(fmt.Sprintf(ProUrl, PaasProAddress, GBASE_INDEX_CLUSTER))
   ClusterResults <- Ack{Result: res, Error: err}
}()
```
### 2.3 垂直方向的靠近
关系密切的概念应该相互靠近（除非有很好的理由，否则不要把关系密切的概念放到不同的文件中）
```
upResults := make(chan Ack)
statusResults := make(chan Ack)

ClusterResults := make(chan Ack)
runtime.GOMAXPROCS(runtime.NumCPU())
```
### 2.4 垂直距离
- 局部变量声明应尽可能靠近其使用位置
- 实例变量(Java)应在类的顶部声明（设计良好的类中，实例变量至少是被大多数方法使用）
- 相关函数，存在调用关系或相似操作应放在一起，相关性越强，彼此距离越短

### 2.5 垂直顺序
自上向下展示函数调用依赖顺序，且调用者尽可能放在被调用者上方。



## 3. 横向格式
- 一行代码应该有多宽？（60个字符以下占70%以上）
- 尽力保持代码行短小；
- 遵循无需拖动滚动条到右边的原则。（考虑到显示器越来越宽，推荐上限120个字符）
```
public Map deleteJobs(@PathParam("jobName") String jobName,
```


### 3.1 水平方向的区隔与靠近
```
int a = 1;

System.out.println("abc");
```
乘法因子之间不加空格，加减法用空格隔开，因为加减法优先级较低。（但多数格式化工具都会漠视运算符优先级）

### 3.2 水平对齐
```
type Resource struct {
   GClusterMem         string  `json:"gclustermem"`
   GNodeMem            string  `json:"gnodemem"`
   SyncServerMem       float64 `json:"syncservermem"`
   NodeCpuPercent      float64 `json:"nodecpupercent"`
   NodeMemPercent      float64 `json:"nodemempercent"`
   NodeDiskUsed        float64 `json:"nodediskused"`
   NodeDiskTotal       float64 `json:"nodedisktotal"`
   NodeDiskUsedPercent float64 `json:"nodediskusedpercent"`
}
```
### 3.3 缩进
- 大多数的类声明不缩进；
- 类中的方法相对类缩进一个层级；
- 方法的实现相对方法声明缩进一个层级......


//违反缩进规则示例
```
public String render() throws Exception {return ""; }
```
//作者的建议
```
public String render() throws Exception {
	return "";
}
```
### 3.4 团队规则
一组开发者应当认同一种格式风格（团队说了算）



# 六、对象与数据结构
##　1. 数据抽象
当我们构建一个实体类时,会为变量设置为private,再为变量提供set/get方法,想一想为什么要这么做?实际上,即使变量都是私有,我们通过变量的set/get方法操作变量时,实现仍然被曝光了,那为何不直接将变量设置为public,然后直接操作变量呢?
```
public interface Vehicle {
	double getFuelTankCapacityInGallons();
	double getGallonsOfGasoline();
}


public interface Vehicle {
	double getPercentFuelRemaining();
}
```
隐藏实现关乎抽象！类并不简单地用getter/setter将变量推向外部，而是曝露抽象接口，以便用户无需了解数据的实现就能操作数据本体。傻乐着乱加getter/setter，是最坏的选择。

## 2. 数据、对象的反对称性
```
public class Square {
    public Point topLeft;
    public double side;
}
public class Rectangle {
    public Point topLeft;
    public double height;
    public double width;
}
public class Circle {
    public Point topLeft;
    public double radius;
}

public class Geometry {
    public double PI = 3.1415926;
    public double area(Object shape) throws NoSuchShapeException
    {   if(shape instanceof Square) {
            Square s = (Square)shape;
            return s.side * s.side;
        }
        else if(shape instanceof Rectangle) {
            Rectangle r = (Rectangle)shape;
            return r.width * r.height;
        }
        else if(shape instanceof Circle) {
            Circle c = (Circle)shape;
            return PI * c.radius * c.radius;
        }
        throw new NoSuchShapeException();
    }
}
```
考虑：
- 1.如果给Geometry类添加一个primeter()函数会怎样？（形状类根本不受影响）
- 2.如果添加一个新形状会怎样？（修改Geometry类中的所有函数）
```
public class Square implements Shape {
    private Point topLeft;
    private double side;

    public double area() {
        return side * side;
    }
}

public class Rectangle implements Shape {
    private Point topLeft;
    private double height;
    private double width;

    public double area() {
        return height * width;
    }
}

public class Circle implements Shape {
    private Point topLeft;
    private double radius;
    public double PI = 3.1415926;

    public double area() {
        return PI * radius*radius;
    }
}
```

考虑：
- 1.如果添加一个primeter()函数会怎样？（所有形状的类都要做修改）
- 2.如果添加一个新形状会怎样？（现有的函数不会受到影响）

`总结： 过程式代码难以添加数据结构，因为必须修改所有函数。面向对象代码难以添加新函数，因为必须修改所有类。所以需要根据系统不同的需求采取不同的编程策略。`



## 3. The Law of Demeter (得墨忒耳定律/迪米特法则)
也叫做“最少知识原则”，是一种开发软件的设计原理。指一个对象应当对其他的对象有尽可能少的了解，方法不应调用由任何函数返回的对象的方法。

一个简单例子是，人可以命令一条狗叫，但是不应该直接指挥狗的嘴来叫。应该由狗去指挥控制它的嘴如何叫。
```
public class Mouth {
    public void bark() {
        System.out.println("wang wang!");
    }
}


public class Dog {
    private Mouth mouth;

    public Mouth getMouth() {
        return mouth;
    }
}


public class Human {
    public void command(Dog dog) {
        dog.getMouth().bark();
    }
}


public class Mouth {
    public void bark() {
        System.out.println("wang wang!");
    }
}

public class Dog {
    private Mouth mouth;

    private Mouth getMouth() {
        return mouth;
    }

	public void bark() {
		mouth.bark();
	}
}

public class Human {
    public void command(Dog dog) {
        dog.bark();
    }
}
```

迪米特法则的初衷在于降低类之间的耦合。由于每个类尽量减少对其他类的依赖，因此，很容易使得系统的功能模块功能独立，相互之间不存在（或很少有）依赖关系。


# 七、错误处理

why：

> 简言之，可能会出错，当错误发生时，程序员就有责任确保代码正常工作。

----

how：

## 7.1 使用异常而非返回码

通过返回码指示错误，必需在调用函数或者方法后立即检查错误。而错误检查常常会被遗漏，而导致更严重的错误，如空指针异常等。

正确的做法是遇到错误时抛出异常，然后在调用代码是决定捕获异常。

举个反例，这个代码真心搞不懂在干嘛：

```go
func SecHandle(w http.ResponseWriter, r *http.Request) {
	if r.Method != http.MethodPost {
		log.WithField("security", "os auth deliver").Errorf("method not allowed : %s", r.Method)
		w.WriteHeader(http.StatusOK)
		w.Write([]byte(fmt.Sprintf("{\"result\":\"unknown method : %s\"}", r.Method)))
		return
	}

	buf, err := ioutil.ReadAll(r.Body)
	defer r.Body.Close()
	if err != nil {
		log.WithField("security", "os auth deliver").Error("read request body failed, errors: %v", err)
		w.WriteHeader(http.StatusOK)
		w.Write([]byte("{\"result\":\"read request body failed\"}"))
		return
	}

	var sec Security
	jsonErr := json.Unmarshal(buf, &sec)
	if jsonErr != nil {
		log.WithField("security", "os auth deliver").Error("parse request body failed, errors: %v", err)
		w.WriteHeader(http.StatusOK)
		w.Write([]byte("{\"result\":\"parse request body failed\"}"))
		return
	}

	clearPassword, err := base64.StdEncoding.DecodeString(sec.Password)
	if err != nil {
		log.WithField("security", "os password deliver").Error("decode password failed: %v", err)
		w.WriteHeader(http.StatusOK)
		w.Write([]byte("{\"result\":\"decode password failed\"}"))
		return
	}

	conf := ssh.ClientConfig{
		User: sec.Username,
		Auth: []ssh.AuthMethod{ssh.Password(string(clearPassword))},
		HostKeyCallback: func(hostname string, remote net.Addr, key ssh.PublicKey) error {
			return nil
		},
		Timeout: time.Second,
	}

	client, err := ssh.Dial("tcp", fmt.Sprintf("%s:%s", sec.IP, "22"), &conf)
	if err != nil && strings.Contains(err.Error(), "unable to authenticate") {
		log.WithField("security", "os auth deliver").
			Errorf("invalid password or username(%s) for host:%s, error : %v", sec.Username, sec.IP, err)
		w.WriteHeader(http.StatusOK)
		w.Write([]byte("{\"result\":\"invalid password/username\"}"))
		return
	}
	defer client.Close()

	nodeSecMap.Store(sec.IP, sec)
	w.WriteHeader(http.StatusOK)
	w.Write([]byte("{\"result\":\"ok\"}"))
	log.WithField("security", "os password deliver").Warnf("deliver auth of host:%s successfully", sec.IP)
}
```


## 7.2 先写try-catch-finally语句

try-catch-finally语句中的try部分代码是一个随时可以取消的过程，就像是一个事务，通过catch部分接住，将程序维持在一个可以持续运行的状态。

**`try-catch-finally`能够帮你定义代码的用户期望什么，无论try代码执行过程如何都一样**

如下面的示例, 无论`collect(register)`方法发生什么，prometheus都能够看到正确的metrics格式。

```java
@Override
protected synchronized void doGet(HttpServletRequest req, HttpServletResponse resp) throws IOException {
    final CollectorRegistry registry = new CollectorRegistry(true);
    resp.setStatus(HttpServletResponse.SC_OK);
    resp.setContentType(TextFormat.CONTENT_TYPE_004);

    increaseAndRegisterCounter(registry, totalScraped);
    long start = System.currentTimeMillis();
    try {
        Collector.getCollector().collect(registry);
    } catch (RuntimeException e) {
        increaseAndRegisterCounter(registry, errors);
        logger.error("scrape gbase metrics failed, cause : " + e.getMessage(), e);
    }
    setAndRegisterGauge(registry, duration, System.currentTimeMillis() - start);
    logger.warn("@@@prometheus scrape at " + fullDataStr(start));

    ...
}
```

## 7.3 使用不可控异常(unchecked exception)

可控异常(checked exception)的问题：

使用可控异常违反了开放/闭合原则，如果你在方法中抛出可控异常，而catch语句在多个层级以上，则在catch语句和抛出异常的方法之间每个方法签名上都要声明该可控异常。也就意味着对较低层级的业务逻辑的修改将会波及到高层级的代码。

## 7.4 给出异常发生的环境说明

每一个抛出的异常都应该有环境说明，以便判断错误的业务环境。

需要注意的是java的错误栈一般只能够得到堆栈跟踪，以定位到代码，故障定位也需要明确的业务环境。

## 7.5 依调用者需要定义异常

异常是用来被捕获的，所以定义异常最需要考虑的是它们如何被捕获，而不应该按照错误来源或者错误类型等来定义异常。

反例：
```java
ACMEPort port = new ACMEPort(12);

try {
    port.open();
} catch (DeviceResponseException e) {
    reportPortError(e);
    logger.log("Device response exception", e);
} catch (ATM1212UnlockedException e) {
    reportPortError(e);
    logger.log("Unlocked exception", e);
} catch (GMXError e) {
    reportPortError(e);
    logger.log("Device response exception", e);
} finally {
    ...
}
```

修改后：

```java
LocalPort port = new LocalPort(12);

try {
    port.open();
} catch (PortDeviceFailure e) {
    reportPortError(e);
    logger.log(e.getMessage(), e);
} finally {
    ....
}


public class LocalPort{
    private ACMEPort innerPort;
    
    public LocalPort(int portNumber) {
        this.innerPort = new ACMEPort(12);
    }
    
    public void open() {
        try {
	    innerPort.open();
	} catch (DeviceResponseException e) {
            throw new PortDeviceFailure(e);
        } catch (ATM1212UnlockedException e) {
	    throw new PortDeviceFailure(e);
        } catch (GMXError e) {
            throw new PortDeviceFailure(e);
        }
    }
    
}
```

## 7.6 定义常规流程

特例模式：
 
> 即创建一个类或者配置一个对象，用来处理特例。

即通过特例模式处理边界问题，将边缘地带的算法与核心算法分离开来，核心算法不负责异常和边缘检测。

反例：

```java
try {
    MealExpences expences = expencesReportDao.getMaels(employee.getID());
    total += expences.getTotal();
} catch (MealExpencesNotFoundException e) {
    total += getMaelPerDiem();
}
```

正例：java 8的Optional。

## 7.7 别返回null值


反例：

```java
final Span nodeRoot = findNodeRoot(entry.getValue());

if (null == nodeRoot) {
    throw new ConvertingZipkinSpanException("root span of endpoint [" + entry.getKey() + "] not found");
}
```

可以采用Optional.of('default_value')，Collections.emptyList()等方法提到null值。

## 7.8 别传递null值

这个比返回null值更糟糕，后果也跟严重。

如果传递了null值，可以使用PreCondition。如：

```java
private List<SpanBo> getSpans(TransactionEventKey key, List<byte[]> hBaseFamilyList) {
    Preconditions.checkArgument(hBaseFamilyList != null, "hbase Family List may not be null.");

    final Get get = new Get(rowKeyDistributor.getDistributedKey(key.getBytes()));

    return hbaseTemplate.get(HBaseTables.TRACES, get, spanMapper);
}
```

## 7.9 小节

将错误处理独立在主逻辑之外，独立看待。

## 补充

### [java的异常分类](http://www.importnew.com/26613.html)

![java Throwable](../pics/994bd262fec543853cd99fe680e857cc.png)

checked exception: `Error`和`RuntimeException`，编译期间不会提示和发现。

unchecked exception: 除checked exception之外所有Throwable子类，编译期间就能够发现。

### [java异常的最佳实践](http://www.importnew.com/26775.html)

- 在Finally块中清理资源或者使用try-with-resource语句
- 抛出异常是指定具体的异常，不要抛出Exception，RuntimeException等抽象异常
- 抛出异常是尽量包含能够定位到较为详细的业务点的描述信息
- 首先捕获最具体的异常，当有多个catch块中，按照捕获顺序只有第一个匹配到的catch块才能执行
- 不要捕获Throwable
- 不要生吞异常，即捕获异常后do nothing
- 不要记录并抛出异常，会导致记录多次日志
- 包装异常时不要抛弃原始的异常，定要把原始的异常设置为传入自定义异常，否则，丢失了原始的异常信息会让错误的分析变得困难。


# 八、Boundaries

Somehow we must integrate foreign code (third-party package or open source code, e.g.) with our own, this chapter we look at practice and tech to keep the boundaries of our software clean.

## 8.1 Using Third-Party Code

Providers strive for broad applicability for wide users, but users always focus on their own needs.

For example, if our application build a `java.util.Map` object and pass it around, and want none receivers would delete elements of it, but `java.util.Map` has a `clear()` method. 

We are not suggesting that every use of `java.util.Map` be encapsulated to passing between third-party code and your own, and also suggesting not to pass `java.util.Map` instance around your system. If you use a boundary interface like `Map`, keep it inside the class and ensure it be encapsulated.  Avoid returning it from, or accepting it as an argument to public APIs.

## 8.2 Exploring and Learning Boundaries

It may be clear job to write tests for the third-party code we use.we could write some tests to explore our understanding of the third-party code, so called *learning tests*.

## 8.3 Learning `log4j`

> coding-> debug -> error -> learning -> coding -> ... -> debug -> correct

## 8.4 Learning Tests Are Better Than Free

The learning tests were precise experiments that helped increase our understanding:

- The learning tests were precise experiments that helped increase our understanding.
- Learning tests verify that the third-party packages we are using work the way we expect them to.
- A clean boundary should be supported by a set of outbound tests that exercise the interface the same way the production code does. 

## 8.5 Using Code That Does Not Yet Exist

Why we use code does not exsists?

- There are often places in the code beyond our knowledge
- The other side of the boundary is unknowable
- We choose to look no farther than the boundary

To keep from being blocked, we defined our own interface we wished. So that the interace is under our control, and  helps keep our own code more readable and focused on what it is trying to accomplish. 

If nessary, an adapter should be introduced to interact with API beyond the boundary.

## 8.6 Clean Boundaries

Boundaries means change is out of control. 

When facing boundaries, special care must be taken to protect and make sure future change is not too costly. 

So we should avoid letting too much of our code know about the third-party particulars, and make sure our own system depend on our own code. 

In order to approch this, manage third-party boundaries by having very few places, wrap them or use an adapter to convert from perfact interface.


# 九、Unit Tests

## 9.1 The Three Laws of TDD

- You may not write production code until you have written a failing unit test.
  (测试先行，除非写到无法通过的测试，否则不要写生产代码)
- You may not write more of a unit test than is sufficient to fail, and not compiling is failing
  (一旦测试代码失败，就需要停止测试代码的编写，即使是单元测试代码的编译失败)
- You may not write more production code than is sufficient to pass the currently failing test. 
  (一旦一个失败的单元测试运行通过，就需要返回测试代码的编写)

## 9.2 Keeping Tests Clean

Having dirty tests is equivalent to, if not worse than, having no tests. 

- Where does the problem come from: 

Tests must change as the production code evolves. From release to release the cost of maintaining team’s test suite rose. In the end they were forced to discard the test suite entirely.

Without a test suite they could not ensure that changes to one part of their system did not break other parts of their system, then the production code begin to rot. It was their decision to allow the tests to be messy that was the seed of that failure.

The condlusion is: test code is just as important as production code. It requires thought, design, and care. It must be kept as clean as production code. 

- The benefits of test code：

It is unit tests that keep production code flexible, maintainable, and reusable. If you have tests, you do not fear making changes to the code! The higher your test coverage, the less your fear. Tests enable all the -ilities, because tests enable change.

## 9.3 Clean Tests

What makes a clean test? Three things. Readability, readability, and readability.
What makes tests readable? The same thing that makes all code readable: **clarity, simplicity, and density of expression.**

```java
// USER --- A --- v1
//           \
//            \
//             v2
@Test
public void build_topology_with_one_span_of_two_virtual_node() throws Exception {
    //given
    List<SpanBo> spanBos = build_fake_SpanBo_with_two_virtual_node();

    //when
    Mockito.when(serviceTypeRegistryService.findServiceType((short) 1002)).thenReturn(new MockServiceType(true));
    Mockito.when(serviceTypeRegistryService.findServiceType((short) 1003)).thenReturn(new MockServiceType(false));

    Topology topology = useCase.createTopology(spanBos);

    //then
    checkNodes(topology.getNodes, 4);
    checkLinks(topology.getLinks, 3);
    checkDetail(topology);
}
```

The BUILD-OPERATE-CHECK pattern is made obvious by the structure of these tests. Every unit test should clearly split into these tree parts:

- the first part builds up the test data
- the second part operates on that test data
- the third part checks that the operation yielded the expected results.

### 9.3.1 Domain-Specific Testing Language

Rather than using the APIs that programmers use to manipulate the system, we build up a set of functions and utilities that make use of those APIs and that make the tests more convenient to write and easier to read. These functions and utilities become a specialized API used by the tests. They are a testing language that programmers use to help themselves to write their tests and to help those who must read those tests later. Such as `checkNodes()` and `checkLinks()`.

### 9.3.2 A Dual Standard

The code within the testing API does have a different standard than production code. It must still be **simple, succinct(简洁), and expressive(富有表达力), but it need not be as efficient as production code**.

There are things that you might never do in a production environment that are perfectly fine in a test environment.

## 9.4 One Assert per Test

There is a school(流派) of thought that says every test function in a JUnit test should have one and only one assert statement.

```java
public void testGetPageHierarchyAsXml() throws Exception {
    givenPages("PageOne", "PageOne.ChildOne", "PageTwo");

    whenRequestIsIssued("root", "type:pages");

    thenResponseShouldBeXML();
}

public void testGetPageHierarchyHasRightTags() throws Exception {
    givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
    
    whenRequestIsIssued("root", "type:pages");
    
    thenResponseShouldContain(
        "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
    );
}
```

We can use the TEMPLATE METHOD pattern and putting the given/when parts in the base class, and the then parts in different derivatives. Or we could create a completely separate test class and put the given and when parts in the `@Before` function. 

The auther thinks that single assert rule is a good guideline, the best thing we can say is that the number of asserts in a test ought to be minimized.

### 9.4.1 Single Concept per Test

A better rule is that we want to test a single concept in each test function. 

```java
/**
 * Miscellaneous tests for the addMonths() method.
 */
public void testAddMonths() {
    SerialDate d1 = SerialDate.createInstance(31, 5, 2004);
    
    SerialDate d2 = SerialDate.addMonths(1, d1);
    assertEquals(30, d2.getDayOfMonth());
    assertEquals(6, d2.getMonth());
    assertEquals(2004, d2.getYYYY());
    
    SerialDate d3 = SerialDate.addMonths(2, d1);
    assertEquals(31, d3.getDayOfMonth());
    assertEquals(7, d3.getMonth());
    assertEquals(2004, d3.getYYYY());
    
    SerialDate d4 = SerialDate.addMonths(1, SerialDate.addMonths(1, d1));
    assertEquals(30, d4.getDayOfMonth());
    assertEquals(7, d4.getMonth());
    assertEquals(2004, d4.getYYYY());
}
```

So probably the best rule is that one should minimize the number of asserts per concept and test just one concept per test function.

## 9.5 F.I.R.S.T.

- **Fast** Tests should be fast. 
- **Independent** Tests should not depend on each other. 
- **Repeatable** Tests should be repeatable in any environment.
- **Self-Validating** The tests should have a boolean output.
- **Timely** The tests need to be written in a timely fashion.

## 9.6 Conclusion

Tests are as important to the health of a project as the production code is. Perhaps they are even more important, because tests preserve and enhance the flexibility, maintainability, and reusability of the production code. 
 
Keep your tests clean.


# 十、Classes

## 10.1 类的组织

- 类应该从一组变量的列表开始
- 公有静态变量应该放在前面，然后是私有静态变量，以及私有实例变量，很少有公有实例变量
- 公有方法应该在变量列表之后
- 私有工具方法放在其调用者之后
- 尽量保证变量和方法的私有性

```java
@Service
public class ZipkinSpanAsyncConsumer implements StorageAdapters.SpanConsumer {
    private final static long WAIT_TIME = 30L;
    private final static String ZIPKIN_AGENT = "zipkin_agent";

    private final static Logger logger = LoggerFactory.getLogger(ZipkinSpanAsyncConsumer.class);

    @Value("${collector.zipkin.aysncWorker.queue.size:10240}")
    private int zipkinWorkerQueueSize;


    @Autowired
    private ZipkinTraceDao zipkinTraceDao;

    @Autowired
    @Qualifier("zipkinTraceEventConsumer")
    private EventConsumer consumer;

    private TransactionEventCache<TransactionEventKey, TransactionEventValue> cache;

    private ThreadPoolExecutor workers;

    private BlockingQueue<Runnable> blockingQueue;

    @PostConstruct
    public void init() {
        this.blockingQueue = new LinkedBlockingQueue<>(this.zipkinWorkerQueueSize);
        this.workers = new ThreadPoolExecutor(
                1,
                1,
                0,
                TimeUnit.SECONDS,
                this.blockingQueue,
                new PinpointThreadFactory("zipkin async worker"));

        Executors.newSingleThreadScheduledExecutor()
                .scheduleAtFixedRate(() -> {
                    logger.info("zipkin async workers queue size: [{}]", this.blockingQueue.size());
                }, ONE_MINUTE_MS, ONE_MINUTE_MS, TimeUnit.MILLISECONDS);
    }

    @Override
    public void accept(List<Span> spans) {
        for (Span span : spans) {
            workers.submit(() -> {
                // 1), insert zipkin span to temporary table
                zipkinTraceDao.insert(span);

                // 2), put root span into eventBus
                if (null == span.parentId) {
                    putToEventBus(span);
                }
            });
        }
    }

    private void putToEventBus(Span span) {
        if (null == cache) {
            cache = new TransactionEventCache<>(consumer);
        }

        Optional<Long> acceptTime = Optional.ofNullable(span.timestamp);
        final long localTime = System.currentTimeMillis();

        TransactionEventKey key = new TransactionEventKey(ZIPKIN_AGENT, acceptTime.orElse(localTime), span.traceId);
        TransactionEventValue value = new TransactionEventValue(key, ZIPKIN_AGENT, acceptTime.orElse(localTime));
        cache.put(key, value, WAIT_TIME, TimeUnit.SECONDS);
    }
}
```

## 10.2 类应该短小

关于类的第一条规则应该是短小。

类名应当描述其权责，如`ZipkinSpanAsyncConsumer`。如果您感到无法为某个类命名，说明它还是太大。此外类名越含糊，说明其职责越不明确，如带有processor，manager等的类。

### 10.2.1 单一权责原则

内容： 类或者模块应该有且只有一条被修改的理由。

单一职责原则是OO设计中最为重要的概念之一，但是也是最容易被破坏的类的设计原则。下面的示例就是一个职责过多的类：

![一个权责过多的类](../pics/2018-10-26_085430.png)

OO的目标之一：系统应该由很多短小的类而不是少量巨大的类组成，每个类都封装者一个权责，并且只有一个被修改的原因，并与少数其他类一起协作达成期望的系统行为。

### 10.2.2 内聚

类应该只有少量的实例变量。类中的每一个方法都应该操作一个或者多个实例变量。如果类中的每一个变量都被每一个方法所使用，这个类就拥有最大的内聚性。内聚性高意味着类中的方法和变量。

内聚性高意味着类中的方法和变量相互依、相互结合成的一个逻辑整体。如果有时候需要为一组子集方法而增加实例变量的时候，就可以将这些实例和变量拆分到新的类里，以便让类更加聚合。

### 10.2.3 保存内聚性就会得到许多短小的类

## 10.3 为了修改而组织

对于多数系统，修改将一直持续。

如果类中出现了只与一小部分功能有关的私有方法，意味着存在改进的空间。

```java
public class Sql {
    public Sql(String table, Column[] columns)
    public String create()
    public String insert(Object[] fields)
    public String selectAll()
    public String findByKey(String keyColumn, String keyValue)
    public String select(Column column, String pattern)
    public String select(Criteria criteria)
    public String preparedInsert()
    private String columnList(Column[] columns)
    private String valuesList(Object[] fields, final Column[] columns)
    private String selectWithCriteria(String criteria)
    private String placeholderList(Column[] columns)
}
```

重构后：

```java
abstract public class Sql {
    public Sql(String table, Column[] columns)
    abstract public String generate();
}
```

```java
public class CreateSql extends Sql {
    public CreateSql(String table, Column[] columns)
    @Override 
    public String generate()
}

public class SelectSql extends Sql {
    public SelectSql(String table, Column[] columns)
    @Override 
    public String generate()
}

public class InsertSql extends Sql {
    public InsertSql(String table, Column[] columns, Object[] fields)
    @Override 
    public String generate()
    private String valuesList(Object[] fields, final Column[] columns)
}

public class SelectWithCriteriaSql extends Sql {
    public SelectWithCriteriaSql(
    String table, Column[] columns, Criteria criteria)
    @Override 
    public String generate()
}

public class SelectWithMatchSql extends Sql {
    public SelectWithMatchSql(
    String table, Column[] columns, Column column, String pattern)
    @Override 
    public String generate()
}

public class FindByKeySql extends Sql
    public FindByKeySql(
    String table, Column[] columns, String keyColumn, String keyValue)
    @Override 
    public String generate()
}

public class PreparedInsertSql extends Sql {
    public PreparedInsertSql(String table, Column[] columns)
    @Override 
    public String generate() {
    private String placeholderList(Column[] columns)
}

public class Where {
    public Where(String criteria)
    public String generate()
}

public class ColumnList {
    public ColumnList(Column[] columns)
    public String generate()
}
```

上面的示例，通过子类化的手段，是的重构后的Sql类对于新功能开放，而不是修改原来繁杂的Sql类。
重构后的Sql支持了面向对象的开放-闭合原则：**类应该对扩展开放，对修改闭合**。


------------------------------
# 十一、系统

*** 待补充 ***
------------------------------
# 十二、迭进
通过迭进设计达到整洁目的
简单设计的四条规则：
## 12.1  运行所有测试
-  只要系统可测试，就会导向保持类短小而且目的单一的设计方案。
-  遵循SRP(单一职责原则）的类，测试起来较为简单。
-  测试编写的越多，就越能持续走向编写较易测试的业务代码。
-  确保系统完全可测试能帮助我们创建更好的设计。
-  紧耦合的代码难以编写测试，编写测试越多，就越会遵循DIP（依赖倒置原则）之类规则，使用依赖注入，接口和抽象等工具尽可能减少耦合，系统就会更贴近OO低耦合度，高内聚度的目标。
-  编写测试导致更好的设计。

## 12.2 不可重复
- 重复是拥有良好设计系统的大敌。
- 要想创建整洁的系统，需要有消除重复的意愿。
- 模板方法模式一种移除高层级重复的通用技巧。


## 12.3 表达了程序员的意图
- 软件项目的主要成本在于长期维护。
- 提高表达力的方法：
  - 选用好类名和好函数名  保存类和函数的尺寸短小    
  - 采用标准命名法来表达（COMMAND / VISITOR）  
  - 编写良好的单元测试（测试的主要目的之一就是通过实例起到文档的作用，读到测试的人应该能很快理解某个类是做什么的）

- 尊重自己的手艺，用心是最珍贵的资源。
- 个人理解：对代码要有敬畏心。

## 12.4 尽可能减少类和方法的数量
- 为了保持类和函数短小，我们可能会创造出太多的细小类和方法。
- 类和方法的数量太多，有时是由毫无意义的教条主义导致的。
- 我们的目标是在保持函数和类短小的同时，保持整个系统的短小精悍。
