# 方式一、简单
## 下载格式

```python
 pip install xxxx -i https://pypi.tuna.tsinghua.edu.cn/simple
```


# 方式二，图形化

![image](https://github.com/xiaoxpai/rabbit-forums/assets/39144603/46f18133-46c6-4394-9eb2-1b3683597f95)


```python

import subprocess  
import sys  

def install_module(module_name):  
    try:  
        __import__(module_name)  
        # print(f"{module_name} 已经安装成功")  
    except ImportError:  
        print(f"尝试通过 pip 安装 {module_name}")  
        subprocess.check_call([sys.executable, '-m', 'pip', 'install', module_name])  
        __import__(module_name)  
        print(f"{module_name} 安装结束")  
    except subprocess.CalledProcessError as e:  
        print(f"命令执行失败，返回码：{e.returncode}")  
        # print(f"标准输出：\n{e.stdout}")  
        # print(f"标准错误输出：\n{e.stderr}")

# 使用你需要的模块名替换 "nicegui"  
install_module("nicegui")

from nicegui import ui, app

source_urls = {  
    "清华源": "https://pypi.tuna.tsinghua.edu.cn/simple",  
    "阿里云镜像源": "http://mirrors.aliyun.com/pypi/simple/",  
    "中国科学技术大学镜像源": "https://pypi.mirrors.ustc.edu.cn/simple/",  
    "豆瓣源": "http://pypi.douban.com/simple/"  
}

def set_pip_source(source_url):  
    print(f"设置 pip 源为: {source_url}")  
    subprocess.run(["pip", "config", "set", "global.index-url", source_url])  
    ui.notify(f"设置 pip 源为: {source_url}", type='positive', position="center")

def reset_pip_source():  
    print("还原 pip 默认源")  
    subprocess.run(["pip", "config", "unset", "global.index-url"]) 
    ui.notify("设置为默认源", type='positive', position="center")

ui.label("点击下面的按钮设置相应的国内pip源").style('color: red; font-size: 200%; font-weight: 300')

for k,v in source_urls.items():
    ui.button(k, on_click=lambda url=v: set_pip_source(url))
ui.button('默认设置', on_click=lambda: reset_pip_source())

ui.run(title='设置pip源', language='zh-CN')

```

- for by
  - https://www.52pojie.cn/thread-1844269-1-2.html

