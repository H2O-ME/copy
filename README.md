<div align="center">
  <img width="100" height="100" src="https://img.icons8.com/metro/100/console.png" alt="console"/>
  <h1>E-control 远程控制系统</h1>
  <p>基于电子邮件的远程控制工具</p>
  
  ![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
  ![Email](https://img.shields.io/badge/Email-D14836?style=flat&logo=gmail&logoColor=white)
  ![Windows](https://img.shields.io/badge/Windows-0078D6?style=flat&logo=windows&logoColor=white)
  ![License](https://img.shields.io/badge/License-AFN-blue.svg)
  [![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/H2O-ME/qsgz)
</div>

## 系统概述

这是一个基于电子邮件的远程控制系统，email_controller.py在受控端长期运行，用于监控指定的电子邮件帐户中的传入命令。系统使用POP3进行命令接收，使用SMTP进行结果传输，形成了一个双向通信通道，可以通过防火墙和网络限制进行远程控制。

## 系统架构

```mermaid
graph TD
    A[程序启动] --> B[初始化配置]
    B --> C[发送启动通知]
    C --> D[主循环开始]
    D --> E{连接到POP3服务器?}
    E -->|成功| F[获取邮件列表]
    E -->|失败| D
    F --> G{有新邮件?}
    G -->|是| H[获取最新邮件]
    G -->|否| D
    H --> I[解析邮件内容]
    I --> J{Base64解码内容}
    J -->|成功| K[获取命令内容]
    J -->|失败| L[记录解码错误]
    L --> D
    K --> M{命令类型判断}
    
    %% 命令处理分支
    M -->|cmd+| N[执行系统命令]
    N --> O[创建子进程]
    O --> P{需要管理员权限?}
    P -->|是| Q[尝试提权执行]
    P -->|否| R[普通权限执行]
    Q --> S{提权成功?}
    S -->|是| T[执行命令]
    S -->|否| U[记录权限错误]
    
    M -->|website+| V[打开网站]
    V --> W[验证URL格式]
    W --> X[调用默认浏览器打开]
    
    M -->|list_desktop| Y[获取桌面文件列表]
    M -->|list_folder| Z[获取指定文件夹内容]
    M -->|send_file| AA[发送指定文件]
    
    %% 结果处理
    T --> AB[收集命令输出]
    R --> AB
    X --> AB
    Y --> AB
    Z --> AB
    AA --> AB
    
    %% 错误处理
    U --> AC
    
    %% 结果发送
    AB --> AC[格式化结果]
    AC --> AD[连接SMTP服务器]
    AD --> AE{连接成功?}
    AE -->|是| AF[发送结果邮件]
    AE -->|否| AG[记录发送失败]
    AG --> D
    AF --> D
    
    %% 超时处理
    N -->|超时| AH[终止超时进程]
    AH --> AI[记录超时错误]
    AI --> AC
    
    %% 循环控制
    D -->|检查间隔| AJ[等待CHECK_INTERVAL秒]
    AJ --> D
    
    %% 异常处理
    E -->|异常| AK[记录连接错误]
    AK --> AJ
    F -->|异常| AL[记录获取邮件错误]
    AL --> AJ
```
### 核心模块架构
本系统分为四个主要功能模块，每个模块负责远程控制作的特定方面。
<img width="1614" height="802" alt="image" src="https://github.com/user-attachments/assets/9093501e-7d11-4a10-8b7e-862d0f3f2976" />

### 命令格式结构
<img width="1531" height="811" alt="image" src="https://github.com/user-attachments/assets/61eeb938-9580-4238-84d3-b6ffd2b3265c" />

### 命令类别和处理
<img width="1242" height="804" alt="image" src="https://github.com/user-attachments/assets/da953939-54ea-4856-9cd0-b2245ccbff18" />

### 设备命令过滤
设备命令过滤机制允许在多设备情况下将命令发送到特定设备。
<img width="1416" height="614" alt="image" src="https://github.com/user-attachments/assets/21373456-db00-45be-9fe1-4b63946c0792" />

### 错误处理流程
<img width="1105" height="811" alt="image" src="https://github.com/user-attachments/assets/23514e3d-8f5e-4939-9cc7-d81ed79e57ba" />


## 部署
### Python环境要求
该系统需要 Python 3.6+ 。
### 安装所需库
```python
pip install psutil pillow
```
### 电子邮件通信配置
系统实现双协议通信层，使用 POP3 进行命令接收，SMTP 进行结果传输。
* `EMAIL_ACCOUNT`：用于受控设备的电子邮件地址
![Python](https://img.shields.io/badge/email__controller.py%3A32-blue)
* `EMAIL_PASSWORD`：受控设备的电子邮件的SMTP/POP3 授权码
![Python](https://img.shields.io/badge/email__controller.py%3A33-blue)
* `RESULT_EMAIL`：控制端电子邮件地址
![Python](https://img.shields.io/badge/email__controller.py%3A45-blue)
* `POP3_SERVER`：受控端邮箱POP3服务器地址（例如，“pop.yeah.net”）
![Python](https://img.shields.io/badge/email__controller.py%3A34-blue)
* `POP3_PORT`：POP3 服务端口（通常为 110）
![Python](https://img.shields.io/badge/email__controller.py%3A35-blue)
* `SMTP_SERVER`：受控端邮件SMTP服务器主机名（例如，“smtp.yeah.net”）
![Python](https://img.shields.io/badge/email__controller.py%3A43-blue)
* `SMTP_PORT`：SMTP 服务端口（通常为 25 ）
![Python](https://img.shields.io/badge/email__controller.py%3A44-blue)
* `CHECK_INTERVAL`：电子邮件轮询间隔（默认为300秒）
![Python](https://img.shields.io/badge/email__controller.py%3A36-blue)
  <img width="815" height="806" alt="image" src="https://github.com/user-attachments/assets/87fddf60-9cba-431f-95c0-a6b83a71de8a" />

### 运行程序
#### 1.通过python运行
导航到包含Python文件的目录，然后按住Shift键并右键单击空白处，选择“在此处打开PowerShell窗口”。在PowerShell窗口中，输入以下命令：
```powershell
python email_controller.py
```
#### 2.使用PyInstaller打包成exe文件运行
导航到包含Python文件的目录，然后按住Shift键并右键单击空白处，选择“在此处打开PowerShell窗口”。在PowerShell窗口中，输入以下命令：
```powershell
pyinstaller -F -w  email_controller.py
```
在dist目录下可以找到生成的email_controller.exe，双击运行。
## 使用
### 命令格式（https://copypy.netlify.app/）
设备定向命令（需指定设备名）
```email
to+设备名+cmd+命令- 执行系统命令
to+设备名+website+网址- 访问网站（无需加 https://）
to+设备名+poweroff- 关闭程序运行
to+设备名+file+list- 列出桌面文件
to+设备名+file+文件名- 发送桌面文件
to+设备名+file+文件夹名- 列出文件夹内容
to+设备名+file+文件夹名+文件名- 发送文件夹中的文件
to+设备名+a- 快捷指令：显示错误弹窗
to+设备名+b- 快捷指令：播放《Never Gonna Give You Up》
to+设备名+screen- 发送当前屏幕截屏
to+设备名+remove- 启动自毁程序
```
广播命令（无需指定设备名，单设备情况下将更加简单）
```email
cmd+命令- 执行系统命令
website+网址- 访问网站
poweroff- 关闭程序运行
file+list- 列出桌面文件
file+文件名- 发送桌面文件
file+文件夹名- 列出文件夹内容
file+文件夹名+文件名- 发送文件夹中的文件
a- 快捷指令：显示错误弹窗
b- 快捷指令：播放《Never Gonna Give You Up》
screen- 发送当前屏幕截屏
remove- 启动自毁程序
```
### 网站命令（website+）示例使用

网站命令使用具有自动协议处理功能的模块在默认浏览器中打开 URL。`webbrowser`
通过RESULT_EMAIL向EMAIL_ACCOUNT发送邮件，主题设置为空，正文内容如下
| 命令格式 | 描述 | 例 |
| --- | --- | --- |
| `website+<url>` | 打开网站（广播） | `website+example.com` |
| `to+<device>+website+<url>` | 打开网站（有针对性） | `to+DESKTOP-ABC+website+example.com` |
### 注意事项
* 设备名中不能包含符号+
* 命令须区分大小写
* 不支持命令嵌套
* 需要管理员权限的命令会自动触发UAC弹窗
* 因邮件附件限制，发送文件单个文件大小限制：20MB，不同的邮箱服务商附件大小限制不同，可根据您的情况调整.这个20MB的限制是硬编码在源代码中的。如果需要发送更大的文件，需要修改源代码中的以下数值。
```python
if os.path.getsize(file_path) > 20 * 1024 * 1024:  # 20MB限制
```
* 使用POP3与SMTP认证须注意授权码有效期
* ⚠CHECK_INTERVAL不能过小，轮询间隔过小可能会被提示“系统流量限制”风控
## 📄 许可证

本项目使用 [AFN-License 2.0](LICENSE) 授权。

---

<div align="center">
    <p>© 2025 THW 版权所有</p>
</div>
