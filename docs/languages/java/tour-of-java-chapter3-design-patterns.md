

# Tour of Java: Chapter 3 Design Patterns

## 前置知识

- 前两章中介绍的 Java 基本语法以及 OOP 相关语法以及概念。

## What are Design Patterns

- *设计模式  Design Patterns* 是一套最佳实践，描述了软件开发人员在面临一些一般性问题时使用和总结出的解决方案。
- 1994年， Erich Gamma、Richard Helm、Ralph Johnson 和 John Vlissides合著了*Design Patterns - Elements of Reusable Object-Oriented Software*（《设计模式——可复用面向对象软件的基础》），首次提出了设计模式的概念，并对大多数常见的设计模式进行了分类组织和讨论。这四位作者也被称为*四人帮 GoF, Gang of Four*。
- 合理的利用设计模式让代码组织真正的工程化，减少耦合、提升可维护性、提升复用度。
- 本章我会带大家认识一些常见的设计模式，并介绍 Java 标准库中相应的设计范例。

!!! note "OOP Principle"

```markdown
 对接口编程而不是对实现编程
 组合优先于继承。
 单一职责原则：类的职责应当尽可能的少而清晰。
 合理使用设计模式，而不要滥用。
```
##  Creational Patterns 创造型模式

创造型模式描述了如何创建对象：

1. *Builder*  构建者模式。
2. *Factory Method*  工厂方法模式。
3. *Abstract Factory*  抽象工厂模式。
4. *Singleton*  单例模式。

### Builder 构建者模式

构建者（英语： Builder）模式通常由一个*指导者 Director* 类、一个*构造者 Builder* 接口/抽象类和若干个具体构建者实现类组成。
在我们的例子中，*Builder* 提供构建其他格式文档所需要的接口：

```java
interface DocumentBuilder {
    void appendText(String para);
    void appendHeader(String header, int level);
    void appendInlineCode(String code);
    // ... More elements
    String build();  // Get build result
}
```

*Director*  管理 Markdown 的解析，并使用 *Builder*  构建转化后的文档：

```java
class MarkdownDirector {
    private final DocumentBuilder builder;
    public MarkdownDirector(DocumentBuilder builder) { this.builder = builder; }
    public String convert(String markdownText) {
        markdownText.lines().forEach(this::parseLine);
        return builder.build();
    }
    private void parseLine(String line) {
        // ...
    }
}
```

*Director* 管理 Markdown的解析，并使用 *Builder*  构建转化后的文档：

```java
// Demo implement
private void parseLine(String line) {
    if (line.startsWith("###### ")) builder.appendHeader(line.substring(7), 6);
    if (line.startsWith("##### ")) builder.appendHeader(line.substring(6), 5);
    // ...
    if (line.startsWith("# ")) builder.appendHeader(line.substring(2), 1);
    if (line.startsWith("- ")) builder.appendUnorderedList(line.substring(2));
    // ...
}
```

具体的 *Builder*  实现包含了对应具体目标文件格式的转化实现，比如：

```java
class HtmlBuilder implements DocumentBuilder {
    private StringBuilder strBuf;
    @Override public void appendText(String para) {
        strBuf.append("<p>").append(para).append("</p>\n");
    }
    @Override public void appendHeader(String header, int level) {
        strBuf.append("<h" + level + ">")
              .append(header)
              .append("</h" + level + ">\n")
    }
}
```

具体的 *Builder* 实现包含了对应具体目标文件格式的转化实现，比如：

```java
class HtmlBuilder implements DocumentBuilder {
    private StringBuilder strBuf;
    @Override public String build() {
        return strBuf.toString();
    }
}
```
作为这个格式转化系统的使用者，我们可以根据需要的文件格式提供相应的构建器：
```java
public static void main(String[] args) throws IOException {
    DocumentBuilder builder = switch (args[2]) {
        case "pdf"  -> new PDFBuilder();
        case "html" -> new HtmlBuilder();
        default     -> { System.exit(-1); yield null; }
    }
    String markdown = Files.readString(Path.of(args[0]));
    String result = new MarkdownDirector(builder).convert(markdown);
    Files.writeString(Path.of(args[1]), result);
}
```

使用建造者模式的好处在于：

1. 分离 *指导者* 和 *构建者* 的具体实现。*指导者* 无需关心具体结果对象的具体内部表示形式、实现方法和组成顺序。
2. 提升模块化，有利于合作开发，也有利于快速定位错误。

在以下情况使用建造者模式：

1. 复杂对象的创建*流程*和*具体内部实现、数据表示*是分离的。

Java内最知名的构建者实例就是`java.lang.StringBuilder`，它提供了构建大型字符串所需的所有方法：

- `append(*)` `insert(int, *)` `replace(int, int, String)`
- `length()` `charAt(int)` `substring(int, int)`
- `toString()`

### Abstract Factory

考虑 IntelliJ IDEA 和现在绝大多数 GUI 编辑器/ IDE ，它们都支持换主题皮肤的功能。我们可以为每一套皮肤都提供一整套 GUI 实现，但是显然这个工作量就过大了。

我们发现，GUI 一般都由一些基本的元素组成，如按钮、菜单、文本框、文本区等。这些基本元素被称为*组件 Widget*。它们可能会有不同的渲染实现，但是 GUI 的布局总是相同的。因此我们可以分离这两者。

不同的皮肤实际上是提供了不同实现的组件的类，这个类被称为抽象工厂 （ 英语：Abstract Factory） 。

```java
class Button    { /* ... */ }
class TextField { /* ... */ }
class TextArea  { /* ... */ }
class Toolbar   { /* ... */ }

// Abstract Factory
interface Theme {
    Button    createButton();
    TextField createTextField();
    TextArea  createTextArea();
    Toolbar   createToolbar();
}
```

```java
class DarkButton    extends Button    { /* ... */ }
class DarkTextField extends TextField { /* ... */ }
class DarkTextArea  extends TextArea  { /* ... */ }
class DarkToolbar   extends Toolbar   { /* ... */ }

class DarkTheme implements Theme {
    @Override public Button    createButton()    { return new DarkButton(); }
    @Override public TextField createTextField() { return new DarkTextField(); }
    @Override public TextArea  createTextArea()  { return new DarkTextArea(); }
    @Override public Toolbar   createToolbar()   { return new DarkToolbar(); }
}
```

```java
class LightButton    extends Button    { /* ... */ }
class LightTextField extends TextField { /* ... */ }
class LightTextArea  extends TextArea  { /* ... */ }
class LightToolbar   extends Toolbar   { /* ... */ }

class LightTheme implements Theme {
    @Override public Button    createButton()    { return new LightButton(); }
    @Override public TextField createTextField() { return new LightTextField(); }
    @Override public TextArea  createTextArea()  { return new LightTextArea(); }
    @Override public Toolbar   createToolbar()   { return new LightToolbar(); }
}
```

```java
class GUI {
    private final Theme theme;
    public GUI(Theme theme) {
        this.theme = theme;
        build();
    }

    private void build() {
        var btn1 = theme.createButton();
        btn1.setGeometry(10, 10, 30, 10);
        btn1.setText("This is a button");
    }
}
```

应用抽象工厂模式可以：

1. 隔离具体实现类。
2. 更换具体实现类的家族，并保证这些实现类的行为一致性。

应当使用抽象工厂模式，当：

1. 你的系统和它所以依赖的组件的创建、组合和内部表示是独立的。
2. 你的系统需要更换组件家族，而组件家族被设计为需要一起使用。

Java Swing 是 Java 提供的跨平台 GUI 库，它就提供了和我们例子很类似的更换主题，或者叫 *Java Swing Look&Feel (L&F)* 的功能，这是抽象工厂模式的最经典例子。



### Factory Method 工厂方法

考虑一个需要数据库连接的应用。对不同数据库的连接是由不同类来实现和处理的，但是具体的数据库类型一般不是编译期决定的，而是由运行时的配置文件等控制。

通过工厂方法（英语： Factory Method），可以将创建具体数据库连接的责任转移给工厂方法内部。其得名，就是因为它负责“制造”对象，就如同一个工厂一样。



```java
interface DatabaseConnection {
    void open();
    Result executeSQL(String sql);
    void close();
}

class MySqlConnection    implements DatabaseConnection { /* ... */ }
class SqliteConnection   implements DatabaseConnection { /* ... */ }
class PostgresConnection implements DatabaseConnection { /* ... */ }
```

```java
class DatabaseConnectionFactory {
    private String type;
    public DatabaseConnectionFactory(String type) { this.type == type; }

    public DatabaseConnection createConnection() {
        return switch(type) {
            case "mysql"    -> new MySqlConnection();
            case "sqlite"   -> new SqliteConnection();
            case "postgres" -> new PostgresConnection();
            default         -> null;
        }
    }
}
```

```java
public static main(String[] args) {
    var factory = new DatabaseConnectionFactory(args[0]);
    var conn = factory.createConnection();
    conn.open();
    conn.executeSQL("SELECT * FROM user");
    conn.close();
}
```

工厂方法通常也可以是静态的`static`：

```java
class DatabaseConnectionFactory {
    public static DatabaseConnection createConnection(String type) {
        return switch(type) {
            case "mysql"    -> new MySqlConnection();
            case "sqlite"   -> new SqliteConnection();
            case "postgres" -> new PostgresConnection();
            default         -> null;
        }
    }
}
var conn = DatabaseConnectionFactory.createConnection(args[0]);
```

工厂方法是 Java 中应用最广泛的设计模式之一。

1. 消除对具体实现类的编译期绑定，进而强化了面向接口编程
2. 工厂方法实际上和构造器的功能非常相似，这个设计模式有时还被称作*虚拟构造器 virtual constructor*。工厂方法命名和语法都更加自由，可以作为更灵活、语义更明显的构造器使用。

Java 中工厂方法的应用随处可见：

- 各个接口中的`of`系列静态方法几乎都是静态工厂方法：
  - `Set.of(...)` `Map.of(...)` `List.of(...)`
  - `Year.of()` `Mouth.of()` `DayOfWeek.of()`
  - `Path.of()`

- `java.util.concurrent.Executors`提供了若干工厂方法来实例化满足不同线程调度需求的执行器实现。
- `java.sql.DriverManager`是Java标准库对本节例子的具体体现。
  - `java.sql.DriverManager#getConnection(String url)`通过解析 URL 中的协议名，自动在类路径中寻找符合要求的数据库驱动类，然后通过数据库驱动建立连接 。





### Singleton 单例

单例（英语： Singleton）模式说明某些类只应当存在一个实例。比如OS只应该有一个窗口管理器，也只应该有一个（逻辑上的）文件系统。对 Java 程序来说，Java 运行时环境也应该是唯一的。事实也是如此，`java.lang.Runtime` 类的确是一个单例，需要使用 `java.lang.Runtime#getRuntime()` 获取这个对象。

```java
class Singleton {
    private static INSTANCE;                 // 1.
    private Singleton() { /* ... */ }        // 2.

    public static Singleton getInstance() {  // 3.
        if(INSTANCE == null) {               // 4.
            INSTANCE = new Singleton();
        }
        return INSTANCE;
    }
}
```

1. 用私有静态成员域保存单例对象
2. 将构造器设置为`private`，这可以防止他人新建其他的对象来破坏单例
3. 如果单例需要在并发环境下使用，可以加入`synchronized`关键字来保证这个函数同时只能有一个线程进入执行
4. 一般采用懒初始化（lazy initialization）的方法，即，在真正需要单例的时候再创建。





## Structural Patterns 结构型模式

### Adapter 适配器

适配器（英语：Adapter）模式，顾名思义，是通过 *Adapter* 类将两个不兼容的接口连接到一起，如下面的例子：

```java
interface AppleLightning { /* ... */ }
interface UsbTypeC       { /* ... */ }

class TypeC2Lightning implements AppleLightning {
    private final UsbTypeC usb;
    public TypeC2Lightning(UsbTypeC usb) { this.usb = usb; }
    // Lightning methods
}
```

Java标准库中最常用的适配器，就是连接`Reader` `Writer`与`InputStream` `OutputStream`的`InputStreamReader` `OutputStreamWriter`。

- `Reader` `Writer`：用于读取**字符流**。
- `InputStream` `OutputStream`：用于读取**字节流**。
- `InputStreamReader` `OutputStreamWriter`：适配器类，用于处理原始**字节流**，通过字符集编码等信息转化为**字符流**。

```java
var fileInput = new FileInputStream("input.txt");
var fileReader = new InputStreamReader(fileInput, "UTF-8");
var charBuf = new char[100];
var charCount = fileReader.read(charBuf, 0, 100);
var str = new String(charBuf);
```

适配器模式的使用场景比较明显，写法也比较直接，因此介绍的篇幅较短。但是要注意，请不要滥用适配器，否则会对类型系统带来混乱，增加无用代码的数量。



### Decorator 装饰器

装饰器（英语： Decorator）模式在暑培的 python 课程中有涉及到（ python 语言有对装饰器函数的语法支持）。装饰器和适配器都是*包装*对象的模式，但是装饰器的重要区别在于，它是将接口包装到*同一个*接口，只不过装饰上增强的功能性。而适配器是包装为另一个不兼容的接口。

考虑 VSCode 的代码区，是在最朴素的文本区上，加入了缩略图、滚动条等组件，而且缩略图这种组件实际上在配置中是可以移除的。需要添加额外功能并支持动态添加/移除时，就应当使用装饰器模式。



```java
class Component { /* ... */ }
class TextArea extends Component { /* ... */ }

class ThumbnailDecorator extends TextArea {
    private final TextArea internal;
    public ThumbnailDecorator(TextArea textArea) { internal = textArea;}
}

class ScrollBarDecorator extends Component {
    private final Component internal;
    public ScrollBarDecorator(Component comp) { internal = comp;}
}
```

```java
class GUIFactory {
    public static Component createCodeArea(boolean enableThumbnail) {
        var textArea = new TextArea();
        return enableThumbnail ? new ScrollBarDecorator(new ThumbnailDecorator(textArea))
                               : new ScrollBarDecorator(textArea);
    }
}
```

`java.io`包的设计处处体现了装饰器模式。我们以输入流为例：

- `java.io.InputStream` 是通用的基类，基本功能就是`read`读取一个字节或者字节数组
- 终端流，即真正产生数据的流：
  - `java.io.ByteArrayInputStream` 从字节数组中读。
  - `java.io.FileInputStream` 从文件中读。
  - `java.io.PipedInputStream` 从连接到的另一个输出流中读。

- 装饰器流，即为现有流添加额外功能的流：
  - `java.io.BufferedInputStream` 为现有流加个缓冲区以提升性能。
  - `java.util.zip.CheckedInputStream` 边读边算CRC32等校验码。
  - `java.util.zip.Inflater/DeflaterInputStream` 解压缩/压缩原始数据。
  - `java.security.DigestInputStream` 边读边算MD5/SHA-1等消息摘要。
  - `javax.crypto.CipherInputStream` 将原始数据通过AES/RSA等密钥算法加密/解密。

```java
var fileInput = new FileInputStream("data.gzip");
var checkedInput = new CheckedInputStream(fileInput, new CRC32());
var gzipInput = new GZIPInputStream(checkedInput);

var data = gzipInput.readAllBytes();
var checksum = checkedInput.getChecksum().getValue();
assert(checksum == 1234567890L);
```

装饰器模式带来的好处有：

1. 保证*单一职责原则*。可以看到，上面我介绍的一大堆输入流子类都可以用简短的一句话描述它们的全部功能。
2. 提供比继承更多的灵活性。我们可以任意组合装饰器流来加任何我们想使用的功能。

但是也有一些小缺点需要注意：

1. 可能会留下一大堆散件，比如获取校验码你仍然需要保留 `checkedInput`。

### Flyweight 享元

考虑 Minecraft，一个（最初）使用 Java 编写的、全球最知名的沙盒游戏之一。Minecraft 的地形由一大堆方块组成，而这些方块都带有自己的数据，比如位置、方向、材质、碰撞箱、变种与其他信息（如铁砧损坏程度等）。我们很容易给出下面的简单设计：

```java
abstract class Block {
    protected int x, y, z, facing;
    protected int hardness, toolLevel, resistance;
    protected Image texture;
    protected AABB aabb;
    public Block( /* ... */ ) { /* ... */ }

    public abstract void onTick();
    public abstract void onClick(Player player, int button);
    public abstract void onDestroy(Entity destroyer);
    // ...
}
```

```java
class LogBlock extends Block {
    private int woodType;
    // Implementation of abstract methods ...
}
class AnvilBlock extends Block {
    private int damageType;
    // Implementation of abstract methods ...
}
class Cobblestone extends Block { /* ... */ }
class Bedrock extends Block { /* ... */ }
```

```java
class World {
    private List<Block> blocks = new ArrayList<Block>();
    
    Block getBlockAt(int x, int y, int y) {
        // ...
    }
}
```

但是这种方法显然存在较大的性能问题：

有很多数据对于相同种类的方块都是重复的，没有必要每个位置都单独存一遍。对于 Minecraft 这种，世界大小上限约为$6\times 10^6 \times 6\times 10^6  \times 400 = 1.44 \times 10^{16}$(1京4400兆)个`Block`的巨型对象，这些重复数据对于内存和硬盘空间来说都是不可接受的。

我们可以通过分离重复的数据和不重复的数据，或称为固有状态（英语： intrinsic）和 外围状态 （英语：extrinsic），通过分离存储两种状态的对象，来减少运行时内存占用。比如，方块的材质、硬度、爆炸抗性等信息是固有状态，方块的位置、朝向等信息是外围状态。

为了区分外围状态，一种方法是为方块的更新或事件处理函数加入外围状态参数：

```java
abstract class Block {
    public abstract void onTick(ExtrinsicBlockState states);
    // ...
}
```

另一种方式是直接为外围状态添加公开的`set`方法：

```java
abstract class Block {
    void setX(int x) { this.x = x; }
    void setY(int y) { this.y = y; }
    void setZ(int z) { this.z = z; }
    // ...
}
```

然后通过工厂类和享元对象池等技术储存、构建不同固有状态的方块的享元（英语： Flyweight）：

```java
class BlockFactory {
    private static Block createBlockInstance(int blockId) {
        return switch(blockId) {
            case 0 -> new Cobblestone();
            case 1 -> new AnvilBlock();
            // ...
        }
    }
}
```

这样，`World`就不需要存储整个`Block`了，而只需要`blockId`指定享元对象，并在具体操作之前设置外围状态即可。

```java
class World {
    private int[] blocks;
    private BlockFactory factory = new BlockFactory();
    public void tick() {
        // for xyz...
            var block = factory.getBlockInstance(blocks[idx]);
            block.setX(x); block.setY(y); block.setZ(z);
            block.onTick();
        // end for
    }
}
```

Java  中的享元实例是基本整数数据类型的包装类的`valueOf`方法：

```java
public final class Integer extends Number implements Comparable<Integer>, Constable, ConstantDesc {
    private static class IntegerCache {
        static final Integer[] cache;
    }

    @IntrinsicCandidate
    public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
}
```

如果基本类型的值在缓存范围内，就返回缓存的对象引用。对于`Integer`，根据 Java 语言规范，默认缓存$[-128, 127]$的整数。



### Proxy 代理

考虑远程过程调用（Remote Procedure Call, RPC），我们需要在本地调用远程代码。但是显然我们不可能`new`一个远程对象，我们不知道它具体的实现，更不知道怎么管理别的机器的内存。这时我们就需要一个代理 （英语： Proxy）类作为本地的占位符，代替本地代码发送远程过程调用请求。这样我们可以和具体实现解耦合，甚至可以无缝在本地实现和远程代码实现之间转换。

```java
interface UserInfoRepo {
    List<String> getAllUserName();
}

class RPCUserInfoRepo implements UserInfoRepo {
    private final String httpEndpoint;
    public RPCUserInfoRepo(String endpoint) { httpEndpoint = endpoint; }
    @Override public List<String> getAllUserName() {
        // 1. Send HTTP Request
        // 2. Parse JSON Result
        // 3. Return List<String>
    }
}

class DBUserInfoRepo implements UserInfoRepo {
    private final Connection db;
    public DBUserInfoRepo(Connection db) { this.db = db; }
    @Override public List<String> getAllUserName() {
        // 1. sql: SELECT name FROM user
        // 2. Return List<String>
    }
}
```

但是真正让代理模式在 Java 发光发热的是真正的*动态性*。 Java 允许在运行时改变字节码，创造新的类，换句话说，*运行时编译代码*。比如上例中的`RPCUserInfoRepo`和`DBUserInfoRepo`都不需要我们手动编写，我们只要声明：我们需要使用一个用户信息仓库，支持框架会自动生成、创建实现类并实例化对象供我们使用。

我们这次使用[ **Spring Data JPA** ](https://spring.io/projects/spring-data-jpa)作为例子。[ **Spring** ](https://spring.io/)是 Java 最流行的后端框架的集合， **Spring Data** 是其关系型数据库支持，[ **JPA** ](https://jakarta.ee/specifications/persistence/3.0/)是 Java/Jakarta Persistence API 的缩写，是持久化层的 API 描述。



我们声明一个`User`类：

```java
@Entity
@Data
public class User {
  @Id
  @GeneratedValue(strategy=GenerationType.AUTO)
  private Long id;
  @NonNull private String nickName;
  @NonNull private String email;
}
```

我们声明我们需要的数据仓库的接口，比如我们需要寻找对应昵称的所有用户：

```java
@Repository
public interface UserRepository extends CrudRepository<User, Long> {
    List<User> findAllByNickName(String nickName);
}
```

[**CrudRepository** ](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/CrudRepository.html) 也内置了大量常用函数，包括 `findAll` `save` `delete` `count`等。

```java
@SpringBootApplication
public class AccessingDataJpaApplication {
    public static void main(String[] args) {
        SpringApplication.run(AccessingDataJpaApplication.class);
    }
    @Bean
    public CommandLineRunner demo(UserRepository repo) {
        return (args) -> {
            repo.save(new User("xsun2001", "xcx19@mails.tsinghua.edu.cn"));
            var userList = repo.findAll();
        };
    }
}
```

我们做了如下事情：

1. 定义了数据格式`User`类;
2. 定义了我们想要的查询、更新方法`UserRepository`;
3. 直接使用`UserRepository`进行数据操作;
4. 让 Spring 启动程序。

但是 Spring 替我们做的事情包括但不限于：

1. 自动发现`User`，根据`User`内部的注解和成员变量，自动生成数据库表生成语句，如有必要，自动合并、迁移、重整已有数据表中的数据;
2. 自动发现`UserRepository`，根据方法名称、参数、返回值等信息，自动解析生成 SQL 语句，自动处理数据库连接和 SQL 执行，自动组织数据库的执行结果，生成本节所说的**Proxy**类;
3. 自动发现`@Bean public CommandLineRunner demo(UserRepository repo)`，根据参数得知我们想要一个`UserRepository`实现，因此实例化代理类并注入。根据返回值类型判断需要运行返回结果。



*动态代理*配合*依赖注入*是究极解耦合方案。因此 Java 自己也不知道实现代码到底放在哪里，直到真正需要的时候才会自动生成。

可以从Spring Data JPA的例子看到，我们只关心*接口*和*数据*的设计与使用，消除了我们手动操作、处理数据库的繁琐代码。

再加上依赖注入，我们甚至都没有`new`过任何相关的类，全都交给**Spring**处理。



## Behavioral Patterns 行为模式

行为模式（英语：Behavioral Patterns ）告诉我们如何让系统中的对象协作，比如：

- *Template Method*  模版方法模式
- *Iterator*  迭代器模式
- *Visitor*  访问者模式
- *Strategy*  策略模式

行为模式实际上还有很多，但是其中有些模式可以认为只是把比较直观的做法赋予了个名字，没有本质上的提升。以下挑选了几个最为常用、最有代表性的行为模式进行讲解。

行为模式描述的更多的是实现细节，因此后续描述时会直接介绍  Java 标准库中的例子而不介绍过于抽象的表达。

### Template Method 模板方法

模版方法（英语：Template Method）模式可以为方法比较多的接口提供一组共享的默认实现，来简化子类派生的过程。Java 的集合框架也大量应用了这个模式，典型的命名特征是`Inter`接口和`AbstractInter`抽象类来提供模板实现。



比如`List`接口有27个需要实现的方法。但实际上，一个最简单的、不可变的列表只需要实现一个`get(int)`方法即可。这种列表实际上用途也非常广泛，比如在整个程序中传递支持的文件类型列表信息等。`AbstractList`就为这种用途提供了除了`get(int)`之外的所有接口的模版实现。

`AbstractList<E>`为不同功能的列表提供了不同的重载方式：

- 不可变的列表只需要实现`get(int)` `size()`;
- 可变的列表需要实现`get(int)` `set(int, E)`；
- 可以扩展大小的列表还应该实现`add(int, E)` `remove(int)`。

其余的包括`addAll`、 `removeAll` 、`iterator`、 `subList` 、`contains`等其他方法全都可以由上述基本方法派生出来，并在模版方法里提供具体实现。

### Iterator 迭代器

迭代器 （英语：Iterator）相信大家或多或少都听说过。Iterator提供了顺序访问一组*聚合*对象的通用方法，而无需关心内部存储实现，比如用数组还是用哈希表等。

Java 集合框架提供了`java.util.Iterator`用于描述集合元素迭代器：

```java
package java.util;
public interface Iterator<E> {
    boolean hasNext();
    E next();
    default void remove() { throw new UnsupportedOperationException("remove"); }
    default void forEachRemaining(Consumer<? super E> action) {
        Objects.requireNonNull(action);
        while (hasNext())
            action.accept(next());
    }
}
```

它的接口都很简洁明了：

- `hasNext()` 是否还有下一个元素（如果有）;
- `next()` 获取下一个元素;
- `remove()` 删除当前元素（默认不支持删除）;
- `forEachRemaining(Consumer<? super E> action)` 对后续所有元素应用`action`。

`Iterator`的基本使用可以直接参考`forEachRemaining`的实现：

```java
var iter = getIterator();
while(iter.hasNext()) {
    var obj = iter.next();
    // do something with obj
}
```

`java.lang.Iterable<T>`接口描述了可以被迭代的聚合对象：

```java
public interface Iterable<T> {
    Iterator<T> iterator();

    default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) { action.accept(t); }
    }
    default Spliterator<T> spliterator() {
        return Spliterators.spliteratorUnknownSize(iterator(), 0);
    }
}
```

- `java.lang.Iterable<T>`只有一个接口函数：`iterator`，返回一个迭代器`Iterator<T>`。
- `forEach`方法的实现展现了`Iterable`的另一种用法：
  - 除了使用`iterator()`方法获取迭代器并按照常规方法访问，Java支持*for-each*循环来遍历可迭代对象：
  - `for (T t : iterable) { /* do something with t */ }`
- `spliterator()`提供了*可并发的*迭代器实现。



Java 集合框架和标准库的很多类都是可迭代的：

- 集合框架本身就是元素的集合，因此自然都是可迭代的;
- `java.nio.file.Path`是自身的可迭代对象，便于分离目录结构;
- `java.nio.file.DirectoryStream<T>`是`T`的可迭代对象，用于获取目录下的所有文件和子目录信息。

### Vistor 访问者

访问者（英语：Visitor）模式也是遍历一组对象所用的设计模式，但是与迭代器不同的是，访问者一般用于访问层次化、结构化对象，并且子对象的类型或者处理方式可能不同。

最经典的例子就是文件目录访问者：`java.nio.file.FileVisitor`。

```java
package java.nio.file;

public interface FileVisitor<T> {
    FileVisitResult preVisitDirectory(T dir, BasicFileAttributes attrs)
        throws IOException;   // 进入目录之前
    FileVisitResult visitFile(T file, BasicFileAttributes attrs)
        throws IOException;   // 处理文件
    FileVisitResult visitFileFailed(T file, IOException exc)
        throws IOException;   // 处理文件错误
    FileVisitResult postVisitDirectory(T dir, IOException exc)
        throws IOException;   // 目录遍历完成
}
```



`FileVisitResult`是一个枚举类型，用于控制后续访问目录的过程，比如继续遍历文件，或者在找到所需要的文件后退出等等：

```java
package java.nio.file;

public enum FileVisitResult {
    CONTINUE,      // 继续遍历
    TERMINATE,     // 终止遍历
    SKIP_SUBTREE,  // 继续遍历，但是跳过子目录下的所有文件/目录
    SKIP_SIBLINGS; // 继续遍历，但是跳过同目录下的其他文件/目录
}
```

有时我们可能只需要某个方法的实现，这时就是*模版方法*体现作用的时候了。Java提供了`java.nio.file.SimpleFileVisitor<T>`作为`FileVisitor<T>`的空实现。实现的方式大概是没有错误的时候就`CONTINUE`，有异常就抛出异常终止整个过程。



使用`java.nio.file.Files#walkFileTree`遍历文件树：

```java
var start = Path.of(".");

Files.walkFileTree(start, new SimpleFileVisitor<Path>() {
    // ... Implementations
}
```

比如我们来统计目录下java源代码文件的数量，就可以：

```java
class FileCounter<T extends Path> extends SimpleFileVisitor<T> {
    private int count = 0;
    public int getCount() { return count; }

    private final String ext;
    public FileCounter(String ext) { this.ext = ext; }

    @Override
    public FileVisitResult visitFile(T file, BasicFileAttributes attrs) throws IOException {
        if (file.endsWith(ext)) ++count;
        return FileVisitResult.CONTINUE;
    }
    @Override
    public FileVisitResult visitFileFailed(T file, IOException exc) throws IOException
        { return FileVisitResult.CONTINUE; }
    @Override
    public FileVisitResult postVisitDirectory(T dir, IOException exc) throws IOException
        { return FileVisitResult.CONTINUE; }
}



public static void main(String[] args) throws IOException{
    var visitor = new FileCounter<Path>(args[1]);

    Files.walkFileTree(Path.of(args[0]), visitor);

    System.out.println(
        "Counts of " + args[1] + " file in " + args[0] + " = " + visitor.getCount()
    );
```



### Strategy 策略

一个功能可能有多种不同的算法实现。抽象出*算法*实例的模式称为策略（英语：Strategy）模式。*Strategy*可以在运行时调配不同的算法实现，这对使用功能的客户端来说是无缝的。这个模式最经典的例子是对数组排序使用的不同*排序器 Comparator*。

```java
@FunctionalInterface
public interface Comparator<T> {
    int compare(T o1, T o2);
}
```



比如我们要根据用户的年龄进行排序，可以定义这样的排序器：

```java
@Data
class User {
    private String name;
    private int age;
}

public class UserComparator implements Comparator<User> {
    int compare(User o1, User o2) {
        return Integer.compare(o1.getAge(), o2.getAge());
    }
}
```

使用`Arrays#sort`排序数组，使用`Collections#sort`排序列表。它们均可以接受一个排序器作为第二个参数：

```java
User[] userArray = fetchUsers();

Arrays.sort(userArray, new UserComparator());
```

由于`Comparator`是一个*函数式接口*`@FunctionalInterface`，因此可以使用 Lambda 表达式：

```java
Arrays.sort(
    userArray,
    (var o1, var o2) -> Integer.compare(o1.getAge(), o2.getAge())
);
```

`Comparator`还提供了一些工厂方法，用于产生各种各样的比较器实现：

- `reversed()`：创建当前比较器的反转版;
- `thenComparing()`系列：当前比较器如果相等，那么再用提供的比较器或键值比较;
- `comparing()`系列：提取对象中的某个键进行比较。



比如我们要提供一个先根据年龄排序，再根据名字的长度排序，长度相同的忽略大小写进行排序的比较器，我们可以使用：

```java
var comparator = Comparator<User>
        .comparingInt(User::getAge)
        .thenComparing(User::getName, 
            Comparator<>.comparingInt(String::length)
                        .thenComparing(String.CASE_INSENSITIVE_ORDER);
        )
```



## Recap

设计模式是面向对象编程的重要一环。上面讲的内容可能确实较多也比较难以理解，但是这些知识都不限于Java，其他面向对象编程语言都可以应用，而且对各位的OOP课程应该有比较大的帮助。

最后一点：

**不要首先考虑设计模式，要首先考虑具体要实现的功能。** 与其在象牙塔上分析到底怎么实现比较好，不如先用具体的类写一写。在软件系统逐步演进、复杂度逐渐提高的过程中，再关注系统中耦合程度高、扩展困难、设计臃肿的地方，然后运用合适设计模式进行重构。



## 资源链接

Spring
https://spring.io/

ing Data JPA
https://spring.io/projects/spring-data-jpa

JPA
https://jakarta.ee/specifications/persistence/3.0/

CrudRepository
https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/CrudRepository.html