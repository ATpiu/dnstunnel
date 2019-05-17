# Ice Dns Tunnel Backdoor (DNS 隧道 渗透&后门)
本作品使用了DNS隧道技术，且是跨平台的（go语言），生成器使用了易语言，做界面简单粗暴。目前编译成了 windows 64位 和 linux64位的两个版本。功能和操作相同<br>

本作品是我花费了2天半的时间写的，遵循了标准的DNS MX协议，在极端网络屏蔽情况下，内网渗透变得复杂，我们只能把通信包装成dns协议才能继续内网渗透。本作品利用了超长域名分段传输数据<br><br>

**优势：**<br>
1】使用了aes对称加密算法，传输过程即使人工手动检测到DNS异常传输也难以解码<br>
2】传输协议稳定，客户端&服务端严格遵循DNS MX协议，且传输过程分段打包传输<br>
3】使用了http扩展协议，可以通过http post的方式优雅关停服务。避免手动hook隐藏以后 找不到进程无法结束（例如linux上使用 curl 即可优雅关停，下文有使用说明）<br>
4】跨平台，多平台<br>


**劣势：**<br>
1】没有做相关的rookit hook 进程可见。在此推荐1个linux效果不错 兼容性很强的hook。可以自己手动hook。已经测试了可用。<br>
另一方面考虑是系统兼容性问题所以没有整合到代码中，还是自己make自己手动hook稳定性比较好。<br>
https://github.com/gianlucaborello/libprocesshider （使用了  ld preloader 技术）<br><br>

2】体积略大，因为是使用golang写的，在没压缩的情况下已经比较大了，可以自行加壳压缩。目前渗透工作，文件体积大小不是瓶颈<br>
3】只使用了dns隧道，其他隧道没有做，因为目前做工具只想做专精，不想整合太多隧道，恶意行为太多容易被一些极端防火墙告警。
后续其他隧道预计独立项目开发。尽量不做整合<br>
4】传输略慢，因为dns隧道对数据包的大小有严格要求，所以只能分段传输，最后还要合包。效率不及普通TCP。<br>
命令回显需要等待一些时间。命令发送禁止超长命令。<br>

**使用场景：**<br>
内网极端环境下的渗透（防火墙只允许dns协议），以及拿到root权限后，配合hook隐藏技术，下后门。<br>

**用途：**<br>
可以使用此工具 远程执行shell命令<br>


## 目录结构
client 目录 -> 目录里的_test文件是一些很重要的测试 <br>
server_main.go.b  ->  是服务端的main文件<br>
client.main.go  ->  是客户端的main文件<br>
generator 目录 -> 是使用易语言写的生成器，只是生成方便一些，给菜鸟使用。大牛可以自行改代码编译<br><br>
generator.exe -> 是易语言编译好的生成器<br>


## 使用说明
1、使用生成器generator.exe 配置服务端端口 和 客户端连接的地址 设置aes密钥（16位）<br>
2、服务端启动、客户端启动。在服务端控制台上发送shell命令。稍作等待即可回显<br>
3、优雅关停： 配置http服务端口，， 向指定地址 /off 发送http post请求，即可关闭程序<br>
例如linux :  curl "http://127.0.0.1:8080/off" -X POST -d "aes密钥"   <br>
密钥必须记好 否则不能优雅关停。<br>
优雅关停场景：在hook进程或者内核hook的情况下，很难找到这个进程，销毁变得麻烦。使用优雅关停 http请求后自动销毁关闭，省心省力。<br>
4、在服务端使用过程中， /e 可以直接退出程序  /up 可以返回上层菜单<br>
5、该工具灵活运用，做好hook的情况下，可以在内网畅通无阻。<br>

## 更新计划
1、添加win平台的自动 hook（感谢提建议）,linux平台依然建议使用上面的工具手动hook。<br>
2、添加端口复用<br>

## 回答疑问
1、服务器只有web对外，无法通过转发等手段连接桌面3389，regeorg又被阻断（疑似检测到来了socket代理的缘故吧），这样的网络结构还有什么办法把3389转发出来么。<br>

答：可以通过端口复用拦截，走web服务，思路和隧道一样，也可以叫https http隧道，将3389的数据包转发出来。等上面2个功能更新好后，后续我这边会研究一下。类似于国外软件 reduh <br>


## 写给使用者
【免责声明：无恶意代码，仅作隧道技术研究，用于非法用途自负】<br><br>

写的比较仓促，基础测试已经做过，基本命令可以执行，在windows环境上测试成功，linux还没来得及测试。有BUG可以提出。另外重申，不考虑整合入太多功能，而尽量是分散开。<br>

如果有急迫需要什么工具的可以提issues 或者 联系我 。可以给我提供一些新思路。谢谢<br>


## 写给golang爱好者

该程序大量使用了chan管道，编程难点在利用dns协议，数据大小有限，格式固定的情况下进行分包，组包。<br>

### End
