# 环境介绍
* 当前环境是一个**精简版 Kali 容器**（kali-rolling 基础镜像），预装了基础工具链（git、curl/wget、python3+pip、jq、file、unzip/zip、nc、ssh/scp、dig、ss/ip、ripgrep/fd、tmux 等）和一个编码 Agent CLI，**渗透工具（nmap/sqlmap 等）没有预装**
* 当前目录是解题工作空间，可以用于保存命令执行日志、较大的扫描结果等

# 按需安装（重要）
* 遇到 `command not found` 时不要放弃，直接安装后重试（免密 sudo 已配置，Kali 仓库工具非常齐全）：
  * 渗透工具：`sudo apt update && sudo apt install -y <pkg>`，例如 nmap、sqlmap、nikto、gobuster、impacket-*、chisel、netexec 等
  * Python 工具：`pip3 install <pkg> --break-system-packages`（python3 和 pip 已预装）
  * Node 工具：`npm install -g <pkg>`
* 安装前先评估耗时，任务时间预算有限：小工具直接装；明显要装很久的（带大量依赖的），优先换更轻的等价工具或手写脚本
* 单次下载/安装卡住超过一两分钟就果断换方案，不要反复重试

# 长时间运行的命令
* 需要持续运行或跨阶段共享的交互式命令，放到 **tmux** 会话中运行（tmux 已预装），最后输出结论时说清楚 tmux 会话信息
  * 例如：持续运行的 HTTP 服务、nc 监听反弹 Shell、长时间扫描

# 反弹 Shell / 数据外带 / OOB
* **重要**：你当前的对外 IP 是 **未填写**
* 你在容器里监听的端口（容器与宿主机共享网络栈），都可以通过 **未填写** IP 访问。反弹 Shell、数据外带、XSS/SSRF/XXE 接收平台等操作都使用该 IP
* 该项未配置时，不要依赖外部回调类利用方式，改用直接回显的方式获取结果
