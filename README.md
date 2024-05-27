# MacOS 下源码部署

1、配置好 python3.9-3.11 环境，执行命令 `brew install python@3.10` 继续执行

```sh
export PATH="/usr/local/opt/python3.10/bin:$PATH"

source ~/.bash_profile

source ~/.zshrc
```

2、创建虚拟环境 `python3.10 -m venv venv`

3、激活虚拟环境 `source ./venv/bin/activate`

4、安装依赖 `pip3 install -r requirements.txt`

5、安装 torch `pip3 install torch==2.1.2 torchaudio==2.1.2`

6、执行 `python3.10 app.py` 启动(默认从 modelscope 下载模型，不可以使用代理)，将自动打开浏览器窗口，默认地址 `http://127.0.0.1:8888`

# Linux 下源码部署

1、配置好 python3.9-3.11 环境

2、创建空目录 `/data/chattts` 执行命令

```
cd /data/chattts

git clone https://github.com/tenadolanter/ChatTTS.git .
```

3、创建虚拟环境 `python3.10 -m venv venv`

4、激活虚拟环境 `source ./venv/bin/activate`

5、安装依赖 `pip3 install -r requirements.txt`

6、如果不需要 CUDA 加速，执行 `pip3 install torch==2.1.2 torchaudio==2.1.2`，如果需要 CUDA 加速，执行如下代码，另需安装 CUDA11.8+ ToolKit，请自行搜索安装方法

```sh
pip install torch==2.1.2 torchaudio==2.1.2 --index-url https://download.pytorch.org/whl/cu118

pip install nvidia-cublas-cu11 nvidia-cudnn-cu11
```

7、执行 `python3.10 app.py` 启动，将自动打开浏览器窗口，默认地址 `http://127.0.0.1:8888`

# 使用 API 请求

请求方法:POST

请求地址: http://127.0.0.1:9966/tts

请求参数:

text:str 必须， 要合成语音的文字

voice:int 可选， 决定音色的数字， 2222 | 7869 | 6653 | 4099 | 5099，可选其一，或者任意传入将随机使用音色

prompt: str 可选，设定 笑声、停顿，例如 [oral_2][laugh_0][break_6]

返回:json 数据

code=0 成功，filename=wav 文件名，url=可下载的 wav 网址

code=1 失败，msg=错误原因

```python
import requests

res=requests.post('http://127.0.0.1:9966/tts',data={"text":"你好啊亲爱的朋友。[laugh]","voice":2222,"prompt":'[oral_2][laugh_0][break_6]'})
print(res.json())

#成功
{code:0,msg:'ok',filename:1.wav,url:http://${location.host}/static/wavs/1.wav}

#error
{code:1,msg:"error"}

```

# 源码安装注意

源码部署启动后，会先从 modelscope 下载模型，但 modelscope 缺少 `spk_stat.pt`，会报错，请点击链接 https://huggingface.co/2Noise/ChatTTS/blob/main/asset/spk_stat.pt 下载 `spk_stat.pt`，将该文件复制到 `项目目录/models/pzc163/chatTTS/asset/` 文件夹内
