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
- Template
- render_template_string
- render
```python
#1、flask:
flask.render_template_string第一个参数、 
Environment.from_string第一个参数
flask.render_template第一个参数指向的文件内容、
Environment.get_template第一个参数指向的文件内容、
jinja2.Template继承jinja2.BaseLoader类并作为loader参数传递给jinja2.Environment的模板加载器,查看实现的模板加载逻辑是否安全

2、Django: 
django.template.Template类构造函数的第一个参数、django.template.loader.get_template_from_string函数的第一个参数、django.template.loader.render_to_string函数第一个参数指向的文件内容、
django.shortcuts.render函数第二个参数指向的文件内容、
django.template.loader.get_template函数第一个参数指向的文件内容


3、Tonado: 
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
- types
```python
import types
types.FileType("/etc/passwd").read()
```
- Flask
	- send_file
	- send_from_directory
	- send_static_file（？）
  
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
- xml.sax.parseString
