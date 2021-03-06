## 第一节 HVV行动概述

> 2022/5/10周二 18：30-21：30 https://emr.h5.xeknow.com/sl/3QEX6I

### 1.1 HVV行动概述

由公安部统一组织的网络安全实战攻防演习，一般在每年的四月至七月。

？？？？？？？？？？？？？？



### 1.2 红队攻击手段

- 开源情报采集
- ？？？

红队攻击手法发生了转变：

- 0day攻击
- 社工钓鱼
- 多源低频攻击：绕过限制访问频率、封禁IP等手段
- 人员和管理漏洞：
- ？？？？？？



### 1.3 防守方演习规则

HVV2021防守方规则：

> - 加分规则
>   $$
>   防护值=10000\times(
>   	\begin{cases}
>   		0.8,防守方未扣分\\
>   		\frac{基础得分}{扣分}，防守方被扣分
>   	\end{cases}
>   	+\frac{附加分\times0.2}{3000}
>   )\\
>   \begin{align}
>   	其中&基础得分=\begin{cases}
>   		非正常防守\\
>   		系统或网络被控:\begin{cases}
>   			权限被控(20\sim100)\\
>   			网络边界被突破(1000\sim5000)\\
>   			目标系统被控(5000\sim10000)
>   		\end{cases}
>   	\end{cases}\times\begin{cases}
>   		监测发现(25\%)\\
>   		分析研判(15\%)\\
>   		应急处置(25\%)\\
>   		通报预警(15\%)\\
>   		协调联动(10\%)\\
>   		追踪溯源(10\%)
>   	\end{cases}\times(80\%)\\
>   	&附加分=\begin{cases}
>   		零日漏洞的发现和处置(\le1500)\\
>   		上报非法攻击线索,例如超出攻击时间
>   	\end{cases}
>   \end{align}
>   $$
>   
>
> - 减分规则
>
>   - 系统或网络被控
>
>     | 名称                     | 减分           | 名称                         | 减分         |
>     | ------------------------ | -------------- | ---------------------------- | ------------ |
>     | 获取参演单位域名控制权限 | 50~100         | 获取数据库账户密码           | 50~100       |
>     | 获取PC/移动终端权限      | 20~50/台       | 获取网络设备权限             | 50~300       |
>     | 获取邮箱权限             | 20/个,500~1000 | 获取工业互联网系统权限       | 指挥部研判   |
>     | 获取办公自动化系统权限   | 200~500        | 获取物联网设备管控平台权限   | ~200         |
>     | 获取SSO/4A权限           | ~300           | 获取安全设备权限             | 50~200       |
>     | 获取域控系统权限         | ~200           | 获取Web应用权限              | 20~100       |
>     | 获取堡垒机/运维机权限    | ~200           | 获取服务器主机权限           | 50~100       |
>     | 获取云管理平台权限       | ~200+10/台主机 | 获取其他系统/服务器/设备权限 | 参考目标系统 |
>     | 获取大数据系统权限       | 数据量与重要性 |                              |              |
>
>   - 网络边界被突破
>
>     | 名称                     | 减分  | 名称           | 减分       |
>     | ------------------------ | ----- | -------------- | ---------- |
>     | 被攻入逻辑隔离业务内网   | ~1000 | 进入核心生产网 | ~5000      |
>     | 被攻入逻辑强隔离业务内网 | ~2000 | 其他情况       | 指挥部研判 |
>
>   - 目标系统被控
>
>     | 名称       | 减分    | 名称       | 减分       |
>     | ---------- | ------- | ---------- | ---------- |
>     | 互联网区   | 5000/个 | 核心生产网 | 10000      |
>     | 业务内网区 | 7000    | 其他情况   | 指挥部研判 |
>
>   - 非正常防守
>
>     封端口、网站不可用等，且被发现并提交确切证据，每30分钟减10分，超过5小时，每30分钟减20分

### 1.4 HVV防守工作内容介绍

![image-20220515153908382](HVV行动.assets/image-20220515153908382.png)









## 第四节 主流安全设备

> 2022/5/15周日 14：00-17：00 https://emr.h5.xeknow.com/sl/3LrDq

防火墙规则：从上而下匹配

| 序号 | 原区域 | 目的区域 | 原地址      | 目的地址      | 服务    | 策略 | 命中数 |
| ---- | ------ | -------- | ----------- | ------------- | ------- | ---- | ------ |
| 1    | DMZ    | proxy    | 10.0.0.1    | 10.10.10.1    | RDP3389 | 允许 |        |
| 2    | DMZ    | proxy    | 10.0.0.1/24 | 10.10.10.1/24 | RDP3389 | 允许 |        |