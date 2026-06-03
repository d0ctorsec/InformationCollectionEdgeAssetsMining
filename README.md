<p>
  <a href="https://github.com/d0ctorsec/BucketHunter/stargazers"><img src="https://img.shields.io/badge/%E6%84%9F%E8%B0%A2%E7%82%B9%E4%B8%AA%E2%98%86Star%E5%92%AF-ff4d8d?style=for-the-badge&logo=github" alt="Thanks for starring"></a>
  <a href="./README.md"><img src="https://img.shields.io/badge/%E4%B8%AD%E6%96%87-README-blue?style=for-the-badge" alt="Chinese README"></a>
  <a href="./README_EN.md"><img src="https://img.shields.io/badge/English-README-black?style=for-the-badge" alt="English README"></a>
</p>

```markdown
# 参考：
1. https://cloudsec.huoxian.cn/docs/articles/aliyun/aliyun_oss#2bucket%E6%A1%B6%E7%88%86%E7%A0%B4
2. https://juejin.cn/post/6950954967435837454
3. https://websec.readthedocs.io/zh/latest/info/index.html
4. https://cloud.tencent.com/developer/article/1459303
5. https://mp.weixin.qq.com/s/LE37OcNYWC9rFH8qeiYsmA
```

# 0x00.前言

```markdown
最近有个售前项目，客户要求要能够收集到隐蔽的资产，因为以往信息收集要用到很多平台与工具交叉验证，记了很多笔记和工具都是分散在各个模块，效率极低，所以干脆就把整体的资产收集思路整理成一篇总和文章，剔除一些重复和淘汰的方法，设计一套实战收集链路。这篇文章是基于以往的笔记整理而来，如果有不对的地方请佬们提点一下。
```

```markdown
`文章整体是一套链路先后顺序已经排好了，小标题顺序靠前的是推荐使用的，每个模块选2种有效方法就行，不是很关键的项目不建议全部使用（非常耗时）。
```

# 0x01、主域名&企业信息收集

## 1.1.企业信息查询

```markdown
如果客户要求去收集子公司或母公司，需要跟客户约定好持股比例，去平台上通过股权穿透图先把子公司名称、域名收集一下。`这个目前没有很好用的工具，通过人工更靠谱一下，如果组织比较多使用Tscan、ENscan，是目前比较推荐的。
```

![image-20250711164011607](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711164011607.png?raw=1)

### 1. Tscan

```markdown
配置站点的cookie和key，输入公司名即可进行基本信息收集，这个还是比较方便的

# https://github.com/TideSec/TscanPlus/releases
```

![image-20260514220526796](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514220526796.png?raw=1)

![image-20260514220830788](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514220830788.png?raw=1)

### 2. EnScan_GO

```markdown
下载后需要配置API、cookie后使用，这里给一下我常用的命令，如果报错可以微调命令，每个人的环境和配置文件都不一样不用完全照搬，这里我们目标是一些基础信息不用太过关注。

`./enscan-v2.0.5-darwin-arm64 -n 北京金山云网络技术有限公司 -field icp,weibo,wechat,app,job,wx_app,copyright,supplier -type tyc,chinaz -timeout 30 --hold --supplier --branch

# 下载链接：https://github.com/wgpsec/ENScan_GO
```

![image-20260514215624698](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514215624698.png?raw=1)

![image-20260514220128132](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514220128132.png?raw=1)

## 1.2.ICP备案查主域名

```markdown
“1.1.企业信息查询”已经查询到了公司名和一部分备案号信息，这时候我们就访问时效性最高的工信部ICP，直接查询`公司名`可获取当前公司备案的主域名，需要点击`详情`。`时效性高是因为ICP备案会定期变更一部分，工信部作为数据来源是第一手信息。

# https://beian.miit.gov.cn/
```

![image-20250710100319990](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710100319990.png?raw=1)

## 1.3.WHOIS 历史与反查

```markdown
通过域名的历史注册信息（注册人邮箱、电话、姓名），反查该人名下的所有其他域名，找到企业被淘汰的"影子域名"，`这种域名运气好还能测绘到资产，再不济在后面的host碰撞会用到。
```

```markdown
1. 查询历史whois
企业的whois信息会因为各种原因进行变更，有时候会变更关键的（注册人邮箱、电话、姓名），通常搜索这些信息可以获取到企业注册过但是没备案的域名。这里推荐用微步，因为它隐藏了不重要的变更。

# https://x.threatbook.com/
```

![image-20260522162057934](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522162057934.png?raw=1)

```markdown
2. 反查关联域名
上一步获取到了whois中的（注册人邮箱、电话、姓名）信息后，我们可以通过这些信息反查注册过的域名，这些域名有的还没过期但是不在企业备案中，可能企业自己也忘记了。

# https://x.threatbook.com/
```

![image-20260522162612812](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522162612812.png?raw=1)

# 0x02、子域名&ip收集

```markdown
“一、主域名&企业信息收集”完成后，我们已经拿到了企业基本信息，接下来我们就基于这些信息，使用各种渠道扩展这些信息。
```

## 2.1.测绘平台被动收集

```markdown
`注：被动收集要工具和测绘平台结合使用，1是不能完全相信工具输出，2是接口调用接口可能因为程序原因缺失结果，而大部分资产都在这一步产出所以要尽可能覆盖全。
```

### 2.1.1.工具调用接口

#### 1.Tscan☆多平台付费

```markdown
这里把Tscan放到第一位的原因是它能够快速收集我们想要的信息，当然我们要更详细收集时还是要用到下面的资产测绘平台语法，不是要打攻防的不要太详细非常耗时。
```

```markdown
1. 配置好各个平台的API
`这里尽量配置全一些，虽然资产多不了多少，但是多出来的可能就是关键的入口点`,这里要通过ICP备案、主域名、证书绑定域名，查询资产信息

2. 收集域名、IP信息（ICP备案）
输入我们在“一、主域名&企业信息收集”获取到的主ICP备案号，字段选择“备案”，`一定要是主备案号，子备案号会漏下，记得勾选右边的资产测绘平台，我这里演示所以没有全部勾选`，可以看到通过ICP备案号收集到了很多信息
```

```markdown
# 下载链接：https://github.com/TideSec/TscanPlus/releases
```

![image-20260514222949152](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514222949152.png?raw=1)

![image-20260514223544146](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514223544146.png?raw=1)

```markdown
3. 收集子域名、IP（主域名）
输入我们在“一、主域名&企业信息收集”获取到的主域名，字段选“域名”，`记得勾选右边的资产测绘平台，我这里演示所以没有全部勾选`。
```

![image-20260514225322546](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514225322546.png?raw=1)

```markdown
4. 收集子域名、IP（证书）
输入我们在“一、主域名&企业信息收集”获取到的主域名，字段选择“证书”，`记得勾选右边的资产测绘平台，我这里演示所以没有全部勾选`。

`注：部分站点会使用通配符证书，另外平台在证书字段上的匹配也可能带有模糊性。所以我们查询app.com.cn会匹配上abcdefapp.com.cn这种资产，这种不是目标资产，所以查询完要人工筛选一下。`
```

![image-20260514230045555](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514230045555.png?raw=1)

#### 2.subfinder☆多平台付费

```markdown
0. subfinder配置文件位置运行以下命令可以看到
./subfinder -version

1. 配置（bevigil、censys、chaos、digitalyama、dnsdumpster、fofa、github、hunter、intelx、leakix、netlas、quake、rsecloud、shodan、zoomeyeapi）的APIkey，使用全量收集工具进行收集


2. 这里列一下我使用的命令
`./subfinder -dL domains.txt -rl 20 -all -json -o results.json

# 下载链接：https://github.com/projectdiscovery/subfinder/releases
```

![image-20260515005949513](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515005949513.png?raw=1)

![image-20260514235657334](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514235657334.png?raw=1)

![image-20260514235823414](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514235823414.png?raw=1)

### 2.1.2.资产测绘平台

#### 1.域名查找

```markdown
# Hunter 
domain.suffix="app.com.cn"
# Quake 
domain:"app.com.cn"
# fofa
host="talentsec.cn"
domain="talentsec.cn"
```

![image-20260522175222606](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522175222606.png?raw=1)

#### 2.ICP备案

```markdown
# Hunter 
icp.number="备案号"
# Quake 
icp:"备案号"
# fofa
icp="沪ICP备20019790号"
```

![image-20250710170452073](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710170452073.png?raw=1)

#### 3.icon_hash

```markdown
首先根据在测绘平台收集的结果，把icon的下载路径或测绘平台的icon_hash保存下来，后期收集边缘资产用就行
# Hunter（使用favicon的MD5值）
web.icon="MD5"
# Quake（使用favicon的MD5值）
favicon:"MD5"
# fofa（使用mmh3算法）
icon_hash="585442251"
```

![image-20260522173706545](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522173706545.png?raw=1)

#### 4. title&body查询

```markdown
1. title
# Hunter 
web.title="标题"
# Quake 
title:"标题"
# fofa
title="螣龙安科"||title="螣龙安科，专注于新一代攻击面管理"
```

![image-20250711141408323](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711141408323.png?raw=1)

```markdown
2. body
`注意body查询不能用title的关键字，这样会出现重复结果的问题

# Hunter 
web.body="内容"
# Quake 
body:"内容"
# fofa
body="螣龙安科是国内新一代主动安全领域的专精特新企业，致力于为客户提供专业的标准化产品与解决方案。"||body="螣龙安科，螣龙天眼，螣龙天眼ASM，螣龙攻击面管理系统"
```

![image-20250711141729654](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711141729654.png?raw=1)

## 2.2.爆破子域名

```markdown
原理：搜索引擎爬虫的抓取、证书透明度日志的同步、威胁情报库的更新，都是有时间差的。如果目标企业刚刚配好了一个新的子域名，此时所有的被动接口大概率都查不到它，但通过字典爆破，可以实时地将其解析出来。
```

### 1.Findomain

```markdown
0. Findomain的config需要自行下载加载，就是使用编译好的默认配置
https://github.com/Findomain/Findomain/tree/master/config_examples

1. 将我们在“一、主域名&企业信息收集”获取到的主域名存入domains.txt，配置Apikey，Findomain的配置文件可以运行，字典可以用Tscan的字典

2. 命令
./findomain --file domains.txt --wordlist subnames-9.5w.txt --config config.example.yml --resolved --output
```

```markdown
# 下载链接：https://github.com/Findomain/Findomain
```

![image-20260515001750864](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515001750864.png?raw=1)

![image-20260515010639947](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515010639947.png?raw=1)

### 2.OneForAll

```markdown
0. OneForAll配置文件在当前目录“config/api.py”中配置

1. 将我们在“一、主域名&企业信息收集”获取到的主域名存入domains.txt，配置好Apikey，OneForAll支持接口获取+域名爆破，这里主要用它的字典爆破功能。

2. 命令
python oneforall.py --targets ./domains.txt --wordlist subnames-9.5w.txt --brute True run

`注：需要注意的是python的工具尽量进入虚拟环境运行，因为很多工具对于依赖库的版本要求不一样，会出现冲突的情况，虚拟环境安装依赖库就能每款工具独立环境。
```

```markdown
# 下载链接：https://github.com/shmilylty/OneForAll
```

![image-20260515004513027](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515004513027.png?raw=1)

![image-20260515004912996](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515004912996.png?raw=1)



## 2.3.证书透明度查询

```markdown
原理：为了防止CA（证书颁发机构）滥发伪造的SSL/TLS证书，对于主流公开信任CA签发、被现代浏览器信任的TLS证书，通常需要满足CT相关策略，因此很多公网子域名会在CT日志中留下记录。`这意味着，只要目标企业为它的某个子域名申请了HTTPS证书，这个子域名就不可避免地被永久公开记录在了CT日志里。
```

### 1.crt查询

```markdown
# https://crt.sh/?q=talentsec.cn
调用证书绑定接口直接查询主域名，可以获取证书历史绑定的域名信息
```

![image-20250710135029477](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710135029477.png?raw=1)

### 2.sslmate查询

```markdown
# https://sslmate.com/ct_search_api/
调用证书绑定接口直接查询主域名，可以获取证书历史绑定的域名信息。
```

![image-20250710183745615](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710183745615.png?raw=1)

## 2.4.威胁情报查询

```markdown
原理：威胁情报平台每天会处理全球海量的DNS请求、恶意样本外联请求和安全设备日志。在这些日志中，记录了大量的域名解析活动。`通过查询威胁情报平台，可以作为补充数据源，帮助发现一部分常规手段不易直接发现的子域名和关联基础设施，甚至能查出一些通过常规字典爆破或搜索引擎查不到的深度隐蔽子域名
```

### 1.微步威胁情报☆需付费

```markdown
# 微步情报社区，https://x.threatbook.cn/
搜索`主域名`可以获取到子域名信息
```

![image-20260515102718931](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515102718931.png?raw=1)

### 2.奇安信威胁情报☆需要登录

```markdown
# 奇安信 威胁情报中心	https://ti.qianxin.com/
搜索`主域名`可以获取到`CNAME`记录和关联域名，里面有子域名
```

![image-20250710144758100](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710144758100.png?raw=1)

```markdown
2.搜索`主域名`点击`关联域名`模块也可以获取子域名信息
```

![image-20250710144930616](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710144930616.png?raw=1)

### 3.360威胁情报☆需要登录

```markdown
# 360 威胁情报中心	https://ti.360.cn/domain/talentsec.cn
1.搜索`主域名`可以获取到对应的子域名信息
```

![image-20250710150425169](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710150425169.png?raw=1)

## 2.5.搜索引擎查询

```markdown
# https://www.google.com
在资产测绘和渗透测试中，利用 Google 搜索引擎（这种技术被称为 Google Hacking 或 Google Dorking ）查询目标网站，是一项极其关键的“被动信息收集”技术。

site收集，通过`site:app.com.cn -www`搜索指定的域名相关的网页排除www站点，google api比较麻烦可以用插件`Google SERP Scrapper`抓取结果
```

![image-20260515110713488](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515110713488.png?raw=1)

![image-20260514142604541](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514142604541.png?raw=1)

## 2.6.DNS历史记录查询☆需要会员

```markdown
很多业务在刚上线测试时，或者是早期阶段，往往是直接将域名解析到服务器真实物理IP上的，后来才接入的CDN。通过历史DNS记录，可以找到这些曾经存在过的旧IP或旧域名。这类“影子资产”通常无人维护、缺乏最新的安全补丁、甚至存在弱口令和未授权访问，是红队打点的绝佳突破口。
```

### 1.dnsdumpster

```markdown
# https://dnsdumpster.com
直接搜索`主域名`or`子域名`去查找DNS历史解析记录，以找到历史配置过的域名和ip
```

![image-20250710140028105](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710140028105.png?raw=1)

![image-20250710140550324](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710140550324.png?raw=1)

### 2.viewdns

```markdown
# https://viewdns.info/iphistory/?domain=talentsec.cn
```

![image-20250711150618586](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711150618586.png?raw=1)

### 3.securitytrails

```markdown
# https://securitytrails.com/domain/talentsec.cn/history/a
```

![image-20250711151918700](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711151918700.png?raw=1)

![image-20250711152522943](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711152522943.png?raw=1)

## 2.7.IP C段提取（需排除CDN）

```markdown
在企业实际运维中，除了对外服务的官网会绑定域名，还有大量的内部系统、基础设施是绝对不会绑定域名的，它们只通过IP访问。
# 路线选择一种即可
```

### 2.7.1.路线一（ASN自治系统号收集）

```markdown
`此方法只针对超大型企业，因为超大型企业通常会注册自己的ASN，ASN下挂载的所有IP段都是企业自有资产，比C段聚合更精准、覆盖更全。
```

#### 1.确认企业的ASN号

```markdown
我们需要找到企业的英文名称，可以在google搜索企业名称，wiki和百度百科都会显示，访问https://bgp.he.net/搜索企业英文名即可。`注意因为模糊查询只有完全匹配的才是，不要收集歪了

# https://bgp.he.net/
```

![image-20260522111455869](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522111455869.png?raw=1)

```markdown
如果没有找到企业英文名称，可以找一个确认是企业资产的IP去反查ASN号

`命令一：whois 103.92.88.7 | grep -i "origin\|netname\|descr"
`命令二：curl https://ipinfo.io/103.92.88.7 | jq '.org'
```

![image-20260522111623050](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522111623050.png?raw=1)

#### 2.获取ASN下IP段

```markdown
在网站直接搜索ASN自治系统号，点击Prefixes v4即可，也可以通过命令收集。

# https://bgp.he.net/
# whois -h whois.radb.net -- '-i origin AS38378' | grep -E "^route|^descr|^origin"
```

![image-20260522112057484](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522112057484.png?raw=1)

![image-20260522114038217](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522114038217.png?raw=1)

```markdown
`需要注意的是可以看到一个ASN号下可以存在多个组织，如果客户只是让你收集博世中国，那博世家电的IP段就要剔除掉。
```

![image-20260522112151408](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522112151408.png?raw=1)

### 2.7.2.路线二（数据合并）

```markdown
# https://github.com/EdgeSecurityTeam/Eeyes

1. 域名汇总C段
输入之前步骤汇总收集到的子域名，通过`./Eeyes-darwin -l domain.txt`提取C段资产
`支持排除掉CDN资产，注意IP出现次数需要超过3次,次数少容易收集歪。
```

![image-20250711171614581](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711171614581.png?raw=1)

```markdown
2. IP汇总C段
然后使用Tscan，输入之前步骤汇总收集到的IP（去重后），提取出现次数3以上的C段地址。
```

![image-20260515134007355](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515134007355.png?raw=1)



## 2.8.IP资产收集

```markdown
# 路线选择一种即可
```

### 2.8.1.路线一（测绘平台）

```markdown
路线一属于信息来源教单一的原因所以需要组合收集，路线二可以直接调用现成的资产测绘平台能力，这里举例我们直接将C段给到空间测绘，列出存活的IP资产，并且可以看到反查域名、归属等信息。
```

```markdown
推荐使用：
# hunter https://hunter.qianxin.com/
# quake https://quake.360.net/quake/#/
# fofa https://fofa.info/
# 微步 https://x.threatbook.com/v5/survey?q=ip%3D%22122.194.76.0%2F24%22
```

![image-20260519215634936](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260519215634936.png?raw=1)

![image-20260519220123946](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260519220123946.png?raw=1)

### 2.8.2.路线二（工具协同）

#### 1.C段资产存活探测

```markdown
将上面列出来的C段资产进行存活探测，先扫描精简端口，确认存活的IP。
`这一步很关键需要先确认哪些资产存活了，才能进行后续的IP反查和全端口扫描，主要目的是节省时间
```

![image-20260515135058632](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515135058632.png?raw=1)

#### 2.IP归属确认

```markdown
因为这部分资产很容易收集歪，所以在C段资产存活探测后，需要确认资产归属，可以看是不是企业专线、解析的域名是不是目前所属。`注：这里查归属时顺便可以反查域名，新的IP也可能找到更多域名。

• 剔除：明显的 CDN IP。
• 剔除：第三方公共服务IP。
• 保留 ：目标企业自己名字注册的 ASN 段、专线段。
• 保留 ：无法明确归属，但在前期收集中出现了多个目标核心子域名的云服务器网段。
```

```markdown
https://tool.chinaz.com/same
https://dns.aizhan.com/
https://x.threatbook.com/v5/batchQuery
https://site.ip138.com/
https://dns.aizhan.com/
https://www.yougetsignal.com/tools/web-sites-on-web-server/
```

![image-20260515135651132](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515135651132.png?raw=1)

#### 3.全端口扫描

```java
对上面保留的IP进行全端口扫描，最后输出的结果就是IP+端口资产需要和之前收集到资产整合，后续的指纹识别这里就不演示了。
```

![image-20260515140136476](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515140136476.png?raw=1)



## 2.9.JS文件查询（耗时）

```markdown
将上面收集到的所有URL作为输入，通过工具爬取网站前端JS文件，从JS文件中提取URL，再通过精简URL筛选出子域名。
```

#### 1.URLFinder

```markdown
1. 这里演示URLFinder爬取一个站点，若要使用建议多个站点同时提取。
■ 突出优点：在提取出API路径后，可以结合配置的字典对这些路径进行Fuzz或者状态码探测，看看这些从JS里抓出来的接口到底能不能真正访问。

命令：
`./URLFinder-macos-arm64 -u https://www.talentsec.cn/ -s all -m 2`
```

```markdown
# 下载链接：https://github.com/pingc0y/URLFinder
```

![image-20250710193348603](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710193348603.png?raw=1)

#### 2.JSFinder

```markdown
1. JSFinder同样可以爬取js文件并提取出域名
■ 突出优点：支持深度爬取。当它在一个JS里发现了一个新的URL时，可以继续去请求那个新的URL，看看能不能找到更多的JS文件。

命令：
`python JSFinder.py -u https://www.talentsec.cn/`
```

```markdown
# 下载链接：https://github.com/Threezh1/JSFinder
```

![image-20250711095243612](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711095243612.png?raw=1)

#### 3.FindSomething☆单个网站

```markdown
1. 以google插件的形式引入
■ 突出优点：支持人工触发DOM节点加载JS，最全但是因为依靠人工触发DOM没法批量爬取，可以用在日常渗透中。
```

```markdown
# 下载链接：https://github.com/momosecurity/FindSomething
```

![](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711094929647.png?raw=1)

## 2.10.目录扫描

```markdown
目录爬取的目的，是为了发现目标网站上那些没有被正常链接公开，但却真实存在并可以直接访问的敏感路径和文件。

1. 目录这里我们主要使用后台页面的字典就行了，尽量挂上代理池有些路径会触发WAF规则，线程也尽量小一些
```

```markdown
# 下载链接：https://github.com/TideSec/TscanPlus/releases
```

![image-20260515145923699](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515145923699.png?raw=1)

![image-20260515150041247](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515150041247.png?raw=1)

```markdown
比如这个站点“/index”和“/admin”可以看做两个站，不止可以作为后台登录页面泄露，也可以作为url资产区分开
```

![image-20250711114145139](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711114145139.png?raw=1)

![image-20250711114318160](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711114318160.png?raw=1)

## 2.11.Host头碰撞（资产无法访问使用）

```markdown
当域名的DNS解析已经失效，但Web服务器、反向代理、负载均衡、CDN回源配置中仍保留着基于Host的虚拟主机路由时，手动将域名指向候选IP后，仍有机会命中这些配置并访问到目标服务。`这也是为什么直接访问IP没有转发，直接访问域名也没有指向，只有域名和IP都具备，才能访问网站的原因。

第一步：收集反向代理服务器的ip
第二步：收集解析异常的域名，能解析到内网中的域名
第三步：手动把域名解析为某个ip，采用笛卡尔积碰撞，两两匹配，直到域名能访问出某个系统
```

#### 1.HostCollision

```markdown
从无法访问的域名，和所有ip进行碰撞测试`java -jar HostCollision.jar -ipFilePath ip.txt  -hostFilePath host.txt`,碰撞成功后可以这样构造请求，填写真实host，下面演示了配置正确host和不配置host的区别。
```

```markdown
# 下载链接：https://github.com/pmiaowu/HostCollision
```

![image-20250711133826918](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711133826918.png?raw=1)

![image-20260515180645244](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515180645244.png?raw=1)

![image-20260515180122831](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515180122831.png?raw=1)

#### 2.Tscan

```markdown
Tscan也新增了host碰撞的功能，只需要将IP和域名粘贴进去运行即可
```

![image-20260515180948072](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515180948072.png?raw=1)

# 0x03、新媒体账号&APP

## 3.1.公众号&小程序&服务号

```markdown
本来有个神器“新榜”可以采集历史公众号数据，但是这家公司转型为B2B了，个人用户无法使用了，所以这里微信直接搜索目标公司名称一样可以收集。

搜索：金光纸业（中国）投资有限公司
```

![image-20260518133144273](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260518133144273.png?raw=1)

## 3.2.APP

```markdown
1. 获取APP名称
直接搜索企业名称即可，企业在发布应用时会绑定开发者为企业。记得切换应用商店看看，有时候测试应用会只在个别应用商店上架。
```

```markdown
# 点点数据：https://www.diandian.com/
# 七脉数据：https://www.qimai.cn/account/setting
```

![image-20260518134539530](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260518134539530.png?raw=1)

![image-20260518140149901](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260518140149901.png?raw=1)

![image-20260518140253691](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260518140253691.png?raw=1)

# 0x04、敏感信息

## 4.1.google hacking

```markdown
`小技巧：可以把下面的语法做成一个skill，不用手动替换，把资产给模型整理新的语法。
```

### 1.管理后台搜索

```markdown
site设置为主域名和IP，可以迅速把目标企业暴露在公网的各种OA系统、CMS后台、测试环境管理端全部揪出来。

# 注使用通配符*可能会收集到其他资产，建议有确认的资产IP的话使用精确的IP
(site:app.com.cn OR site:122.194.76.10) (intitle:"管理" OR intitle:"后台" OR intitle:"登录" OR intitle:"admin" OR intitle:"login" OR intitle:"dashboard" OR intitle:"system" OR intitle:"portal" OR intitle:"manage" OR inurl:admin OR inurl:login OR inurl:manage OR inurl:dashboard)
```

### 2.未授权接口搜索

```markdown
可以找到因为企业配置不当，而产生的未授权访问或敏感信息泄露的常见接口、监控面板和调试页面。
# 注使用通配符*可能会收集到其他资产，建议有确认的资产IP的话使用精确的IP
(site:app.com.cn OR site:122.194.76.10) (inurl:swagger OR inurl:api-docs OR intitle:"Swagger UI" OR inurl:graphql OR inurl:graphiql OR inurl:altair OR inurl:v2/api-docs OR inurl:v3/api-docs OR inurl:postman OR inurl:doc.html)
```

![image-20260519182823014](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260519182823014.png?raw=1)

### 3.微服务与Java 中间件未授权

```markdown
针对Java生态的定制语句，寻找SpringBoot监控端点、阿里Druid数据库连接池面板、微服务注册与配置中心等。
# 注使用通配符*可能会收集到其他资产，建议有确认的资产IP的话使用精确的IP
(site:app.com.cn OR site:122.194.76.10) (inurl:actuator OR inurl:druid OR inurl:nacos OR intitle:"Eureka" OR intitle:"Spring Boot Admin" OR inurl:jolokia OR inurl:dubbo OR inurl:weblogic OR inurl:jmx-console OR intitle:"Tomcat" OR intitle:"Welcome to JBoss")
```

### 4.运维监控

```markdown
匹配运维基础设施的特征页面，如Kibana (日志)、Zabbix (监控)、Jenkins (持续集成/发版)、GitLab (源码仓库)，这些系统往往部署在内网，内网系统安全性一般较低，一旦公网暴露大概率有突破口。

# 注使用通配符*可能会收集到其他资产，建议有确认的资产IP的话使用精确的IP
1. (site:app.com.cn OR site:122.194.76.10) (intitle:"Kibana" OR inurl:kibana OR intitle:"Zabbix" OR inurl:jenkins OR intitle:"Dashboard [Jenkins]" OR intitle:"GitLab" OR intitle:"Gitea" OR inurl:harbor OR inurl:solr OR inurl:phpmyadmin OR intitle:"phpMyAdmin" OR inurl:adminer OR intitle:"Hadoop" OR intitle:"Apache Flink" OR intitle:"Spark" OR intitle:"RabbitMQ Management" OR inurl:flower)

2. (site:app.com.cn OR site:122.194.76.10) (inurl:"/phpinfo.php" OR intitle:"phpinfo()" OR inurl:"/server-status" OR inurl:"/server-info" OR intitle:"Apache Status")
```

### 5.测试环境

```markdown
匹配URL路径中包含开发/测试特征词的页面。此类环境一般用于上线前测试，通常没有接入WAF，且可能存在为了方便调试而遗留的硬编码密码或后门接口，也是突破口之一。

(site:app.com.cn OR site:yicongfound.org OR site:122.194.76.10) (inurl:test OR inurl:dev OR inurl:staging OR inurl:demo OR inurl:uat OR inurl:beta OR inurl:sandbox OR inurl:pre OR inurl:local)
```

### 6.敏感文件

```markdown
强制Google只返回指定格式的文件，企业运维中不可避免会存放一些未授权访问的文档，尤其是存储桶经常会放一些未授权文档，被爬虫抓取后直接暴露。
# 注使用通配符*可能会收集到其他资产，建议有确认的资产IP的话使用精确的IP
1. (site:app.com.cn OR site:122.194.76.10) (ext:pdf OR ext:doc OR ext:docx OR ext:xls OR ext:xlsx OR ext:ppt OR ext:pptx OR ext:csv OR ext:txt)

2. (site:app.com.cn OR site:122.194.76.10) ((ext:xls OR ext:xlsx OR ext:csv OR ext:txt) (intext:"密码" OR intext:"账号" OR intext:"通讯录" OR intext:"联系方式" OR intext:"机密" OR intext:"内部" OR intext:"confidential" OR intext:"credentials" OR intext:"token"))

3. (site:app.com.cn OR site:122.194.76.10) (ext:sql OR ext:tar.gz OR ext:zip OR ext:rar OR ext:bak OR ext:7z OR ext:dump OR ext:db OR ext:sqlite OR ext:gz OR ext:bz2 OR ext:tar)

4. (site:app.com.cn OR site:122.194.76.10) (ext:env OR ext:ini OR ext:conf OR ext:config OR ext:xml OR ext:properties OR ext:yml OR ext:yaml OR ext:json OR ext:cfg OR ext:toml)

5. (site:app.com.cn OR site:122.194.76.10) (ext:pem OR ext:crt OR ext:key OR ext:p12 OR ext:pub OR ext:ovpn OR ext:jks OR ext:keystore OR intext:"BEGIN RSA PRIVATE KEY" OR intext:"BEGIN OPENSSH PRIVATE KEY")

6. (site:app.com.cn OR site:122.194.76.10) (ext:log OR inurl:log OR inurl:logs OR (ext:txt (intext:"Exception" OR intext:"Error")))

7. (site:app.com.cn OR site:122.194.76.10) (inurl:.git OR inurl:.svn OR inurl:.DS_Store OR inurl:.hg OR inurl:.vscode OR inurl:.idea)
```

### 7.目录遍历

```markdown
当Web服务器没有配置默认首页且允许列出目录时，就会出现这个标题，可以直接浏览服务器上的文件结构。
# 注使用通配符*可能会收集到其他资产，建议有确认的资产IP的话使用精确的IP
(site:app.com.cn OR site:122.194.76.10) (intitle:"index of" OR intitle:"Directory Listing For")
```

### 8. 报错页面

```markdown
匹配各类网站在错误时特有的报错文本格式，作用较少主要用于确认目标数据库信息。

(site:app.com.cn OR site:yicongfound.org OR site:122.194.76.10) (intext:"Fatal error:" OR intext:"Warning: mysql_connect()" OR intext:"stack trace:" OR intext:"Syntax error" OR intext:"java.lang.NullPointerException" OR intext:"SQL syntax" OR intext:"Exception in thread")
```

### 9.寻找第三方协作平台/看板泄露

```markdown
指定目标为第三方云端协作网站，并在后面跟上目标企业的核心高辨识度名字。从第三方网站中提取出出与目标企业相关的开发代码或测试账号。

# 注：以下语法搜索范围较大，关键字要及时调整，尽量精确
(site:trello.com OR site:atlassian.net OR site:notion.so OR site:shimo.im OR site:yuque.com OR site:processon.com OR site:github.com) ("yicongfound" OR "金光纸业" OR "sinarmas")
```

## 4.2.网盘搜索

```markdown
开发、运维或外包人员为了工作方便，经常会将公司内部的源代码、网络拓扑图、VPN 账号或交接文档打包上传到百度网盘、阿里云盘，或者记在语雀等云端笔记上。

# 凌风云：https://www.lingfengyun.com/
# 懒盘：https://www.lzpanx.com/
# 大力盘：https://www.dalipan.com/
```

```markdown
1. 寻找网络拓扑与IT架构
 金光纸业 拓扑
 金光集团 架构
 APP中国 IP规划
 sinarmas 拓扑
 yicongfound 架构图
 
2. 寻找系统交接与配置文档
 金光纸业 VPN
 金光集团 堡垒机
 APP中国 密码
 sinarmas 账号交接
 yicongfound 配置文档
 
3. 寻找核心代码与备份数据
 金光纸业 源码
 APP中国 数据库备份
 sinarmas sql备份
 yicongfound 代码
 
4. 寻找内部行政/通讯录泄露（社工利器）
 金光纸业 通讯录
 金光集团 花名册
 APP中国 组织架构
 sinarmas 员工名单
```

![image-20260519234939022](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260519234939022.png?raw=1)

![image-20260520232223400](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260520232223400.png?raw=1)

## 4.3.邮箱收集

```markdown
邮箱收集直接调用三方接口即可，一般都是通过邮箱域名搜索，所以在这之前我们可以从“一、主域名&企业信息收集”确认企业邮箱的主域名是什么，这些网站会在全网爬取或枚举邮箱信息，不用我们自己去花时间确认，而且数据源够的话收集的数量是很可观的。

# https://app.snov.io/domain-search?name=app.com.cn&tab=emails
# https://phonebook.cz/
# http://www.skymem.info/
# https://hunter.io/dashboard
# https://www.email-format.com/i/search/
# telegram社工库（就不列举了有法律风险，google搜一下一堆）
```

![image-20260520232737788](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260520232737788.png?raw=1)

![image-20260520233326221](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260520233326221.png?raw=1)

## 4.4.网站源码获取

### 1. 指纹识别

```markdown
可以通过Tscan、Wappalyzer识别产品的指纹信息

# Tscan：https://github.com/TideSec/TscanPlus/releases
# Wappalyzer： https://chromewebstore.google.com/detail/wappalyzer-technology-pro/gppongmhjkpfnbhagpmjfkannfbllamg?hl=zh-CN&utm_source=ext_sidebar
```

![image-20260528100339175](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528100339175.png?raw=1)

![image-20260528094848203](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528094848203.png?raw=1)

### 2. 开源产品

```markdown
• 开源产品GitHub、gitee直接clone下载下来审计

# https://github.com/
# https://gitee.com/
```

![image-20260528104919491](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528104919491.png?raw=1)

### 3.商业产品

```markdown
• 商业产品：闲鱼、深网、A网购买，有法律风险部分链接我就不列，可以搜到
	
# https://www.huzhan.com/
# https://28xin.com/
# https://bbs.bcb5.com/
# https://darkforums.su/
```

![image-20260528111615305](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528111615305.png?raw=1)

![image-20260528112037293](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528112037293.png?raw=1)

### 4.魔改/自研系统 

```markdown
# 实战技巧：
■ 搜到结果后，点进仓库看commit历史，很多人发现泄露后删了文件但历史还在
■ 看这个开发者的其他仓库，一个人泄露一次大概率还有其他泄露
■ 注意fork的仓库，原仓库删了但fork还在
```

```markdown
1. 找到网站特征
■ 页面底部版权信息："Powered by XXX" / "Copyright 2023 某某科技"
■ 独特的JS/CSS路径：/static/js/chunk-2d0a1f3.js
■ 独特的API路径：/api/v2/internal/xxx
■ 页面title或meta标签中的系统名
■ 登录页面的独特文案/图片路径
```

```markdown
2. 拿特征去GitHub/grep.app搜
# 搜版权信息
"某某科技" OR "Powered by XXX"
# 搜独特的API路径（最有效，因为API路径是代码里写死的）
"/api/v2/internal/dashboard"
# 搜独特的JS文件名或路径结构
"chunk-2d0a1f3" OR "/static/admin/js/"
# 搜页面中独特的中文文案
"欢迎使用XX管理平台"
```

```markdown
3. 扫描目录找源码泄露
通过目录扫描有时候能扫描到开发者遗漏的源码备份或配置文件，部分配置文件可以恢复源码。原理就是目录扫描配合工具dump源码，可以参考我之前看到的一篇文章：
# https://juejin.cn/post/6950954967435837454
■ git源码泄露
■ svn源码泄露
■ hg源码泄漏
■ 网站备份压缩文件
■ WEB-INF/web.xml 泄露
■ DS_Store 文件泄露
■ SWP 文件泄露
■ CVS泄露
■ Bzr泄露

.git/HEAD
.git/config
.git/index
.git/logs/HEAD
.git/refs/heads/master
.git/refs/heads/main
.git/COMMIT_EDITMSG
.git/description
.git/packed-refs
.svn/entries
.svn/wc.db
.svn/all-wcprops
.svn/props
.svn/tmp
.hg/store/00manifest.i
.hg/store/00changelog.i
.hg/dirstate
.hg/requires
.hg/hgrc
.bzr/README
.bzr/branch/last-revision
.bzr/checkout/dirstate
.bzr/repository/pack-names
CVS/Root
CVS/Entries
CVS/Repository
WEB-INF/web.xml
WEB-INF/classes/application.yml
WEB-INF/classes/application.properties
WEB-INF/classes/database.properties
WEB-INF/classes/config.properties
WEB-INF/classes/spring-mvc.xml
WEB-INF/classes/db.properties
.DS_Store
.DS_Store?
._DS_Store
.index.php.swp
.config.php.swp
.database.yml.swp
.env.swp
index.php.swp
config.php.swp
www.zip
www.tar.gz
www.rar
www.7z
web.zip
web.tar.gz
web.rar
site.zip
site.tar.gz
backup.zip
backup.tar.gz
backup.rar
bak.zip
bak.tar.gz
code.zip
code.tar.gz
src.zip
src.tar.gz
dist.zip
htdocs.zip
htdocs.tar.gz
wwwroot.zip
wwwroot.tar.gz
wwwroot.rar
public.zip
public.tar.gz
database.sql
db.sql
dump.sql
data.sql
backup.sql
mysql.sql
.env
.env.bak
.env.local
.env.production
.env.development
config.yml
config.yaml
config.json
config.php.bak
config.inc.php.bak
database.yml
application.yml
application.properties
settings.py
wp-config.php.bak
```



## 4.5.深网数据泄露

```markdown
1. IntelX 聚合搜索引擎
这个网站好用的地方在于，他把深网、A网上历史泄露事件的数据都下载下来了数据量很大，泄露主角一般是与目标有供应关系的三方网站，很多账密之类的都能使用，缺点是太贵了让公司报销。

搜索：app.com.cn、@app.com.cn、企业名
# https://intelx.io/
```

![image-20260528123220419](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528123220419.png?raw=1)

```markdown
2. 深网
搜索：app.com.cn、@app.com.cn、企业名
# https://darkforums.su/
# https://pwnforums.st/
# https://www.demonforums.net/

3. A网
xss.is
KillSec
darknetarmy
长安不夜城
中文A网交易市场
RansomHouse
```

![image-20260528133024298](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528133024298.png?raw=1)

## 4.6.云存储桶枚举

```markdown
原理：企业在云上的存储桶（阿里云OSS/AWS S3/腾讯云COS/华为云OBS）经常因权限配置错误导致未授权访问。存储桶名称全球唯一，通过企业关键字+常见后缀拼接桶名进行探测，可以发现企业遗忘或配置不当的云存储资产。

# 注：写到这里本来讲一下思路又觉得不太负责，感觉把存储桶枚举流程整理成了工具，已经传到github供大家使用
# https://github.com/d0ctorsec/BucketHunter
```

```markdown
1. 生成桶名称字典
从前期收集的子域名和企业关键字中提取特征，生成桶名字典。进入交互式终端选择第一步生成桶字典，依照流程填写以下信息即可
`./BucketHunter 

	• 企业关键字（公司简称、产品名、项目代号）
	• 前期收集的子域名列表
```

![image-20260602134401499](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260602134401499.png?raw=1)

```markdown
2. 多云存储桶扫描
使用程序会调用开源项目S3Scanner对生成的字典进行多云平台批量扫描，能看到收集到很多存在可读对象和未授权写的桶，因为是模糊匹配大部分会歪需要让AI结合资产分析一遍关联度。

`在交互式终端选择第二步，执行扫描即可
```

![image-20260602134738849](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260602134738849.png?raw=1)

![image-20260528144231746](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528144231746.png?raw=1)

```markdown
3. AI研判分析
`第二步完成后可以让AI分析结果，剔除掉明显不符合的部分,分析前需要填写必要信息以让AI精度提高，最后人工跟进AI结果筛选验证一下就行,演示的客户域名比较通用，AI也能综合分析个概率。
```

![image-20260602135222516](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260602135222516.png?raw=1)

![image-20260602135336246](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260602135336246.png?raw=1)

![image-20260528144503357](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528144503357.png?raw=1)
