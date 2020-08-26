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

### Expression Language
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
