### 前因

写CTF题的时候需要使用`python2`版本运行`lsb.py`，在此记录使用已经废弃的`python2`版本时需要注意的事项。

题目链接：[图片中的英文](https://ctf.bugku.com/challenges/detail/id/362.html)，盲水印得到I LOV3 CHINA，但没有用处，作者给出了第二步的压缩包和`hint`。

> hint: 1.`sifu` 2.`wbstego4` 3.图片里的英文，加密`lsb` 4.最后的英文是全小写无空格
>
> 链接：https://pan.baidu.com/s/1camASeUJ7ETGrWEO-PZihQ 提取码：pcat

压缩包里有五张图片，结合题目描述，需要从中破解出压缩包的密码

这是游戏`sifu`里的东西，能认出其中有智和义，其实是把传统的仁义礼智信改成了人义尊智诚，这里的顺序是人尊智义诚，不过这五个字所有的排列组合都不是密码。

> 题目描述是：当你拥有足够的力量，你是会选择抹杀掉你的仇人，还是将其饶恕，有没有信心在一天之内将仇人们解决吗？而且仇人拥有5种不同能力，你能破解他们吗。(图片里的英文都是key)

所以压缩包的密码是**木火水金土**，分别对应五个关卡反派的能力。

解压之后有两张图片，用`wbstego`解密`bmp`得到`key`：**虾仁猪心**。

解压缩包得到`flag.png`，是疯狂动物城里的一幕，结合题目意思可以知道需要找出这张图对应的字幕
是`You know you love me`。根据作者提示全换成小写并去掉空格，得到正确的密码`youknowyouloveme`。
下载`lsb`工具 https://github.com/livz/cloacked-pixel，运行`python2 lsb.py extract flag.png flag.txt youknowyouloveme`失败，提示缺少模块。

### python2安装pip

`Kali Linux`自带`Python 2.7`，因此可以省略安装`Python`，直接用命令行下载 `get-pip.py`：

```bash
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py
```

也可以访问 https://bootstrap.pypa.io/pip/2.7/get-pip.py，手动保存为 `get-pip2.py`。

使用`python2`来运行下载的安装脚本：

```bash
sudo python2 get-pip2.py
```

### pip2安装所需模块

尝试直接使用以下命令进行安装，但安装失败。

```bash
sudo python2 -m pip install numpy
```

一种笨方法是访问 https://pypi.org/project/numpy/1.16.6/#files 搜索`numpy-1.16.6-cp27-cp27mu-manylinux1_x86_64.whl`，并进行下载安装。

```bash
$sudo python2 -m pip install numpy-1.16.6-cp27-cp27mu-manylinux1_x86_64.whl
DEPRECATION: Python 2.7 reached the end of its life on January 1st, 2020. Please upgrade your Python as Python 2.7 is no longer maintained. pip 21.0 will drop support for Python 2.7 in January 2021. More details about Python 2 support in pip can be found at https://pip.pypa.io/en/latest/development/release-process/#python-2-support pip 21.0 will remove support for this functionality.                             
Processing ./numpy-1.16.6-cp27-cp27mu-manylinux1_x86_64.whl
Installing collected packages: numpy
Successfully installed numpy-1.16.6
```

另一种方法是`pip2`切换清华源：

```bash
$ pip2 config set global.index-url https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple
DEPRECATION: Python 2.7 reached the end of its life on January 1st, 2020. Please upgrade your Python as Python 2.7 is no longer maintained. pip 21.0 will drop support for Python 2.7 in January 2021. More details about Python 2 support in pip can be found at https://pip.pypa.io/en/latest/development/release-process/#python-2-support pip 21.0 will remove support for this functionality.                                  
Writing to /home/tyd/.config/pip/pip.conf
```

运行以下命令能看到换源成功。

```bash
$ pip3 config list
global.index-url='https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple'
install.trusted-host='https://pypi.tuna.tsinghua.edu.cn/simple/'
                                                                                    
$ pip2 config list
DEPRECATION: Python 2.7 reached the end of its life on January 1st, 2020. Please upgrade your Python as Python 2.7 is no longer maintained. pip 21.0 will drop support for Python 2.7 in January 2021. More details about Python 2 support in pip can be found at https://pip.pypa.io/en/latest/development/release-process/#python-2-support pip 21.0 will remove support for this functionality.                                  
global.index-url='https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple'
install.trusted-host='https://pypi.tuna.tsinghua.edu.cn/simple/'
```

用`pip2`安装`numpy`和`matplotlib`。

```bash
pip2 install numpy
pip2 install matplotlib
```

运行`python2 lsb.py`，提示`ImportError: No module named _tkinter, please install the python-tk package`。这个错误表明系统中缺少`_tkinter`模块，而这个模块通常与`Tkinter`图形用户界面（GUI）库相关联。我们需要安装`python-tk`包来解决这个问题。

```bash
sudo apt-get install python-tk
```

运行以下命令进行验证，如果没有报错，说明`python-tk`包已经成功安装。

```bash
python2 -c "import _tkinter"
```

运行`python2 lsb.py`继续报错`ImportError: No module named PIL`。说明`python2`缺少`Pillow`模块。

```bash
pip2 install Pillow
```

验证是否安装成功。

```
python2 -c "import PIL"
```

运行`python2 lsb.py`继续报错`ImportError: No module named Crypto`，说明`python2`需要安装`pycryptodome`模块。

```bash
pip2 install pycryptodome
```

验证是否安装成功。

```bash
python2 -c "import Crypto"
```

终于运行`python2 lsb.py`成功！

```bash
┌──(tyd㉿kali)-[~/CTFTools/cloacked-pixel]
└─$ python2 lsb.py extract flag.png flag.txt youknowyouloveme
[+] Image size: 1885x849 pixels.
[+] Written extracted data to flag.txt.
```

输出`flag.txt`即可。

```bash
$ cat flag.txt 
bugku{Ni_You_dUi_xi@ng_m@_doge} 
```

