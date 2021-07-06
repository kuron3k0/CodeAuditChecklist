## Python Checklist

### Code & Command Execution
- eval
- execfile
```python
# python2.X only
# user_request_data_file.data 文件内容为__import__('os').system('id')
execfile("./user_request_data_file.data") 
```
- compile
- map
- input
```python
# python2.x only
# input 函数接收标准输入的内容，然后当作 python 代码解释执行
input("plz input data:") # input __import__('os').system('id')
```
- os.system
- os.popen
- os.popen2
- os.popen3
- os.popen4
- os.execl
- os.execle
- os.execlp
- os.execlpe
- os.execv
- os.execve
- os.execvp
- os.execvpe
- os.spawnl
- os.spawnle
- os.spawnlp
- os.spawnlpe
- os.spawnv
- os.spawnve
- os.spawnvp
- os.spawnvpe
- subprocess.call
- subprocess.check_call
- subprocess.check_output
- subprocess.Popen
- commands.getstatusoutput
- commands.getoutput
- commands.getstatus
- glob.glob
- linecache.getline
- io.open
- popen2.popen2
- popen2.popen3
- popen2.popen4
- timeit.timeit
```python
import timeit
timeit.timeit("__import__('os').system('id')", number=1000000)
```
- timeit.repeat
- sys.call_tracing
- code.interact
- code.compile_command
- codeop.compile_command
- pty.spawn
- posixfile.open
- posixfile.fileopen
- platform.popen
- importlib.import_module
- importlib.\_\_import\_\_
- Image.open
```python
# 来自 https://xz.aliyun.com/t/44
from PIL import Image
def get_img_size(filepath=""):
    '''获取图片长宽'''
    if filepath:
        img = Image.open(filepath)
        img.load()
        return img.size
    return (0, 0)
```
- types.FunctionType
```python
(types.FunctionType(marshal.loads(base64.b64decode(code_serialized)), globals(), ''))()
```
- asyncio
```python
import asyncio
# 如下函数同理：
# asyncio.get_event_loop().subprocess_exec
# asyncio.get_event_loop().subprocess_shell
asyncio.create_subprocess_shell(cmd)
```
- profile
```python
import profile
profile.run("__import__('os').system('id')")
profile.runctx("__import__('os').system('id')")
```
- cProfile
```python
import cProfile
cProfile.run("__import__('os').system('id')")
cProfile.runctx("__import__('os').system('id')", None, None)
```


### SSTI (jinja2, Mako, Tornado, Django)
```python
# 1、flask:
flask.render_template_string第一个参数、 
Environment.from_string第一个参数
flask.render_template第一个参数指向的文件内容、
Environment.get_template第一个参数指向的文件内容、
jinja2.Template继承jinja2.BaseLoader类并作为loader参数传递给jinja2.Environment的模板加载器,查看实现的模板加载逻辑是否安全

# 2、Django: 
django.template.Template类构造函数的第一个参数、django.template.loader.get_template_from_string函数的第一个参数、django.template.loader.render_to_string函数第一个参数指向的文件内容、
django.shortcuts.render函数第二个参数指向的文件内容、
django.template.loader.get_template函数第一个参数指向的文件内容


# 3、Tonado: 
tornado.template.Template类第一个参数
tornado.template.Loader类第一个参数指向的路径
tornado.web.RequestHandler类第一个参数指向的文件内容
tornado.web.RequestHandler类render函数第一个参数指向的文件内容
tornado.web.UIModule类render_string函数第一个参数指向的文件内容

```

### File
- open
- shutil.copyfileobj
- shutil.copyfile
- shutil.copy
- shutil.copy2
- shutil.move
- shutil.make_archive
- dircache.listdir
- dircache.opendir
- os.remove
- os.open
- os.pipe
- os.listdir
- os.access
- os.symlink
- os.walk
- os.fwalk
- os.rename
- os.renames
- os.replace
- os.rmdir
- os.scandir
- os.removedirs
- os.unlink
- os.chdir
- os.link
- os.mkdir
- os.makedirs
- os.readlink
- types
```python
import types
types.FileType("/etc/passwd").read()
```
- Flask
	- send_file
	- send_from_directory
	- send_static_file（？）
- distutils.file_util.copy_file
- distutils.file_util.move_file
- distutils.file_util.write_file
  
### Deserialization
- pickle.load
- pickle.loads
- cPickle.load
- cPickle.loads
- yaml.load
- shelve（本质上还是pickle）
```python
import shelve
import os
class eee(object):
    def __reduce__(self):
        return (os.system,('calc',))

with shelve.open('test.db') as db:
    db['a'] = eee()
    
with shelve.open('test.db') as db:
    db['a']
    
```

### CRLF
- httplib
- urllib
- urllib2

### SSRF
- httplib
- http.client（python3将`httplib`改名为`http.client`）
- urllib
- urllib2
- urllib3（python3）
- pycurl

### XXE
- python2.x

| kind                                                         | sax            | etree          | minidom        | pulldom        | xmlrpc         |
| ------------------------------------------------------------ | -------------- | -------------- | -------------- | -------------- | -------------- |
| billion laughs                                               | **Vulnerable** | **Vulnerable** | **Vulnerable** | **Vulnerable** | **Vulnerable** |
| quadratic blowup                                             | **Vulnerable** | **Vulnerable** | **Vulnerable** | **Vulnerable** | **Vulnerable** |
| external entity expansion                                    | **Vulnerable** | Safe (1)       | Safe (2)       | **Vulnerable** | Safe (3)       |
| [DTD](https://en.wikipedia.org/wiki/Document_type_definition)  retrieval | **Vulnerable** | Safe           | Safe           | **Vulnerable** | Safe           |
| decompression bomb                                           | Safe           | Safe           | Safe           | Safe           | **Vulnerable** |

1. [`xml.etree.ElementTree`](xml.etree.elementtree.html#module-xml.etree.ElementTree) doesn't expand external  entities and raises a ParserError when an entity occurs. 
2. [`xml.dom.minidom`](xml.dom.minidom.html#module-xml.dom.minidom) doesn't expand external entities and  simply returns the unexpanded entity verbatim. 
3. [`xmlrpclib`](xmlrpclib.html#module-xmlrpclib) doesn't expand external entities and omits  them. 


- python3.x

| kind                                                         | sax            | etree          | minidom        | pulldom        | xmlrpc         |
| ------------------------------------------------------------ | -------------- | -------------- | -------------- | -------------- | -------------- |
| billion laughs                                               | **Vulnerable** | **Vulnerable** | **Vulnerable** | **Vulnerable** | **Vulnerable** |
| quadratic blowup                                             | **Vulnerable** | **Vulnerable** | **Vulnerable** | **Vulnerable** | **Vulnerable** |
| external entity expansion                                    | Safe (4)       | Safe    (1)    | Safe    (2)    | Safe (4)       | Safe    (3)    |
| [DTD](https://en.wikipedia.org/wiki/Document_type_definition) retrieval | Safe (4)       | Safe           | Safe           | Safe (4)       | Safe           |
| decompression bomb                                           | Safe           | Safe           | Safe           | Safe           | **Vulnerable** |

1. [`xml.etree.ElementTree`](mk:@MSITStore:D:\software\Python\Python38\Doc\python383.chm::/library/xml.etree.elementtree.html#module-xml.etree.ElementTree) doesn't expand external entities and raises a `ParserError` when an entity occurs.
2. [`xml.dom.minidom`](mk:@MSITStore:D:\software\Python\Python38\Doc\python383.chm::/library/xml.dom.minidom.html#module-xml.dom.minidom) doesn't expand external entities and simply returns the unexpanded entity verbatim.
3. `xmlrpclib` doesn't expand external entities and omits them.
4. Since Python 3.7.1, external general entities are no longer processed by default.


### XSS
- autoescape
```python
{% autoescape off %}
    {{ title }}
{% end autoescape %}

# 模板引擎选项设置禁用自动转义导致模板输出数据不会被转义处理，有风险
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        ...,
        'OPTIONS': {'autoescape': False}
    }
]

# render函数通过设置上下文变量autoescape=False禁用引擎自动转义
# 本次输出用户不可信数据不会被自动转义
render(request, "index.html", {"my_name": "<script>alert(123);</script>", "autoescape": False})

# 同上设置Context的autoescape属性为False
template = Template("My name is {{ my_name }}.")
context = Context({"my_name": "<script>alert(123);</script>"}, autoescape=False)
template.render(context)


# render_to_string 渲染函数上下文设置autoescape=False
render_to_string("index.html", {"my_name": "<script>alert(123);</script>", "autoescape": False})

# 这种属性输出没有被单或者双引号包围，可以导致额外的属性注入
# 进而导致XSS, id='123 onerror=javascript:alert(123) '
<img id={{ id }}></div>


# 直接输出到script标签内的语句django无能为力，没法防XSS
# name='alert(123)'
<script>var name = {{ name }};</script>

```
- safe
```python
# safe filter 处理过的输出参数不会被转义可导致问题
{{ title | safe | escape }}
```
- safeseq
```python
# safeseq filter 处理过的输出参数不会被转义可导致问题
{{ title_list | safeseq | join:", " }}
```
- mark_safe
```python
# mark_safe 标记过的输出内容不会被转义
mark_safe("<b>%s</b>" % title)
```
- SafeString
```python
# SafeString 标记过的输出不会被转义
SafeString("<b>%s</b>" % title)
```
- is_safe
```python
# 注册带 is_safe=True 属性的过滤器，输出结果不会被转义
@register.filter(is_safe=True)
def myfilter(value):
    return value
{{ title | myfilter }}
```
- html_safe
```python
# html_safe 装饰器定义封装__str__的__html__类方法，
# 导致__str__输出的内容不会被转义
@html_safe
class RawHtml(str):
    def __str__(self):
        return data
```

### Redirect
- redirect
```python
return redirect(url)
```
- make_response
```python
return make_response(("", 302, {"Location":url}))
```
