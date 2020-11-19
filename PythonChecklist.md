## Python Checklist

### Code & Command Execution
- eval
- execfile
- compile
- map
- input
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

### SSTI (jinja2, Mako, Tornado, Django)
- Template
- render_template_string
- render

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
