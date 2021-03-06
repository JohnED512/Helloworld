# WSKEY
> 配置后可转换生成有效期为 `16` 个小时的 `pt_key` ，非常稳定，用了以后再也不用月月喊人更新账号了\
> WSKEY是移动端APP特有的值，有效期为1年相当于永久，注意手动注销、修改密码会导致其失效\
> 项目已配置定时任务但默认被注释，如需使用自行取消注释，每天更新3次较为稳妥

> 转换脚本位于 **utils/UpdateCookie.js**，是本项目中唯一的加密脚本，为了防止该脚本被滥用已对其中关于**获取签名**部分的代码进行了局部加密\
> 本项目转换脚本为**本地转换**，没有加密转换部分的代码，其它任何通过在线获取签名的WSKEY转换脚本均存在账号泄露的风险\
> 作者郑重承诺该脚本没有任何上传行为，可以自行抓包验证，随时接受检验

- 目前推荐定时

```
<自定义分钟> 1,9,17 * * * sleep $((${RANDOM} % 56)) && task cookie update >/dev/null 2>&1
```

## 配置方法

?> 1. 建议通过面板中的 `编辑配置 - 账号配置` 进行编辑，有格式检测防止配置出错\
  2. 注意 `pt_pin` 和 `ws_key` 填入的是对应的值，不要把格式和标点符号带进去

- 编辑位于 **config** 目录下的 **account.json** 配置文件

  ```json
  [
    {
      "pt_pin": "jd_1234567",
      "ws_key": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "remarks": "张三",
      "config": {
        "ep": {}
      }
    },
    {
      "pt_pin": "jd_abcdefg",
      "ws_key": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "remarks": "李四",
      "config": {
        "ep": {}
      }
    },
    {
      "pt_pin": "",
      "ws_key": "",
      "remarks": "",
      "config": {
        "ep": {}
      }
    }
  ]
  ```

  > `remarks` 是通知备注，会在适配脚本的推送消息中将用户名昵称换成备注名称\
  > `ep` 是设备信息，对于目前来说可填可不填没有实际意义，一般出现在请求的**Body**中

  > `pt_pin` 是用户名，目前在APP的部分请求中已被加密可能无法通过抓包有效获取，可前往账号设置进行查看\
  > 如果用户名含有中文汉字需转换成 `UrlEncode`，面板自带转换工具，入口：`账号配置 - URL编码/解码`

***

## 协助抓取
> 此方法旨在为**帮助他人抓取WSKEY**，需要一台有**公网**的 Linux 环境，基于 [AnyProxy - 由阿里巴巴集团发布的一款开源抓包工具](https://github.com/alibaba/anyproxy)\
> 如果是本地操作借助一些专业工具即可无需使用此方法，例如安卓：Httpcanary，IOS：Stream、Thor 等App

### 安装 Nodejs 和 npm 环境 <!-- {docsify-ignore} -->
> 如果已安装则忽略该步骤

  - 安装 Nodejs

    ```bash
    curl -sL https://deb.nodesource.com/setup_17.x | bash -
    ```
    > 由于 NodeSource 没有国内源，下载速度可能较慢

    ```bash
    apt-get install -y nodejs 或 yum install -y nodejs
    ```
    > 根据自己的操作系统使用对应包管理工具进行安装，该命令会附带安装 **npm**
  
  - npm 切换国内源

    ```bash
    npm config set registry https://registry.npm.taobao.org
    ```
    > 如果没有国际互联网环境建议切换为国内淘宝源

### 安装并配置 AnyProxy <!-- {docsify-ignore} -->

  - 安装

    ```bash
    npm install -g anyproxy
    ```

  - 生成证书

    ```bash
    anyproxy-ca
    ```
    > 一路回车

  - 启动服务

    ```bash
    anyproxy
    ```
    > `Ctrl + C` 停止运行

### 食用方法 <!-- {docsify-ignore} -->

  - 客户端（对方）操作

    ```
    一共三步，请认真阅读自己设备对应操作系统的教程！
    
    1. 安装证书
    注意：用手机自带的或已安装的浏览器APP打开不要通过微信，IOS统一用Safari
    浏览器打开 "http://<ip>:8002" 左侧 RootCA - Download 下载证书文件
    
    IOS：下载后弹窗点击允许，如果没有自动弹窗那么就从下载历史里点一下，然后打开系统设置 - 已下载描述文件 - 安装，安装完成后转到通用 - 关于本机 - 证书信任设置 - 针对根证书启用完全信任 - 启用 AnyProxy
    安卓：从本地文件管理或浏览器下载记录中点击已下载的证书文件 (rootCA.crt)，会自动 弹出系统自带的证书安装器（可能会选择打开程序），名称为AnyProxy或随便起个，类型保持默认用于Wi-Fi
    
    1. 配置代理
    IOS：打开系统设置 - 无线局域网 - 已连接Wi-Fi右边感叹号 - 下划至底部配置代理
    安卓：与IOS类似，长按已连接的Wi-Fi进入配置，各手机OS界面不同，已知某为手机可能不支持IP需填域名

    主机名： "<ip>" 端口："8001" ，没有用户名和密码
    
    1. 打开京东APP
    如果之前打开过就从后台关掉打开一个新的，如果前两步操作正确APP会正常联网否则就加载不出来内容，然后点击 "我的" 随便划一划差不多就抓到了
    
    抓到后关闭代理即可，证书删不删无所谓
    ```
    > 通过专业软件连接代理也是可以的，运营商网络也支持配置代理，不过最方便的还是通过Wi-Fi连接，因为对方不一定懂这些

  - 服务提供者（你）操作

  > 打开 [http://\<ip\>:8002](http://<ip>:8002 ':disabled') 进入 AnyProxy 面板查看关于 `api.m.jd.com` 域名的 **POST** 请求，`wskey`会出现在 **Cookies** 中

  ?> 面板有内容说明代理连接正常，如果请求中没有 `Header` 内容或者客户端断网说明证书安装异常
