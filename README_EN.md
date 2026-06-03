

<p>
  <a href="https://github.com/d0ctorsec/BucketHunter/stargazers"><img src="https://img.shields.io/badge/%E8%A7%89%E5%BE%97%E5%86%99%E7%9A%84%E5%A5%BD%E7%BB%99%E4%B8%AA%E2%98%86Star%E5%92%AF-ff4d8d?style=for-the-badge&logo=github" alt="Thanks for starring"></a>&nbsp;&nbsp;&nbsp;
  <a href="./README.md"><img src="https://img.shields.io/badge/Chinese-README-blue?style=for-the-badge" alt="Chinese README"></a>&nbsp;&nbsp;&nbsp;
  <a href="./README_EN.md"><img src="https://img.shields.io/badge/English-README-black?style=for-the-badge" alt="English README"></a>
</p>

![InformationCollectionEdgeAssetsMining](https://socialify.git.ci/d0ctorsec/InformationCollectionEdgeAssetsMining/image?forks=1&language=1&name=1&owner=1&stargazers=1&theme=Light)

```markdown
# References
1. https://cloudsec.huoxian.cn/docs/articles/aliyun/aliyun_oss#2bucket%E6%A1%B6%E7%88%86%E7%A0%B4
2. https://juejin.cn/post/6950954967435837454
3. https://websec.readthedocs.io/zh/latest/info/index.html
4. https://cloud.tencent.com/developer/article/1459303
5. https://mp.weixin.qq.com/s/LE37OcNYWC9rFH8qeiYsmA
```

# 0x00. Preface

**Before You Begin: I recently worked on a pre-sales project where the client wanted a workflow capable of uncovering hidden assets. Traditional information gathering usually requires cross-validating many platforms and tools, while notes and utilities tend to be scattered across different modules, which makes the process extremely inefficient. So I decided to reorganize the overall asset-discovery workflow into a single comprehensive article, remove repetitive or outdated methods, and build a practical end-to-end collection chain. This article is compiled from my previous notes and experience. If you notice anything inaccurate, feel free to point it out. Since the article has many heading levels, readers who prefer a better reading experience can download the [Markdown version](https://github.com/d0ctorsec/InformationCollectionEdgeAssetsMining).**

```markdown
`The workflow in this article is already ordered by priority. Earlier subsections are generally the more recommended options. In most modules, choosing one or two effective methods is enough. Unless the target is especially important, it is usually unnecessary to use every single technique because doing so is very time-consuming.
```

# 0x01. Root Domain and Enterprise Profiling

## 1.1. Enterprise Information Lookup

```markdown
If the client wants subsidiaries or parent companies included in scope, first agree on the shareholding threshold with them. Then use enterprise-information platforms and equity penetration graphs to collect subsidiary names and domains. `There is no perfect tool for this step yet, so manual review is still more reliable. For larger organizations, Tscan and ENScan are currently the most practical options.
```

![image-20250711164011607](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711164011607.png?raw=1)

### 1. Tscan

```markdown
Configure the target site's cookies and API keys, then enter the company name to collect basic information. This is a convenient starting point.

# https://github.com/TideSec/TscanPlus/releases
```

![image-20260514220526796](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514220526796.png?raw=1)

![image-20260514220830788](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514220830788.png?raw=1)

### 2. EnScan_GO

```markdown
After downloading the tool, configure the required API keys and cookies before use. Below is one of the commands I commonly use. If it fails, adjust it to fit your own environment and configuration file. The goal here is simply to collect baseline enterprise information, so there is no need to over-tune the command.

`./enscan-v2.0.5-darwin-arm64 -n Beijing Jinshan Cloud Network Technology Co., Ltd. -field icp,weibo,wechat,app,job,wx_app,copyright,supplier -type tyc,chinaz -timeout 30 --hold --supplier --branch

# Download: https://github.com/wgpsec/ENScan_GO
```

![image-20260514215624698](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514215624698.png?raw=1)

![image-20260514220128132](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514220128132.png?raw=1)

## 1.2. Use ICP Filing to Identify the Root Domain

```markdown
After completing `1.1. Enterprise Information Lookup`, you should already have the company name and part of the filing information. The next step is to query the official MIIT ICP filing platform directly with the `company name` to retrieve the currently registered root domain. You need to click `Details`. `This data source is highly time-sensitive because ICP filings change over time, and MIIT is the primary source of that information.

# https://beian.miit.gov.cn/
```

![image-20250710100319990](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710100319990.png?raw=1)

## 1.3. Historical WHOIS and Reverse Lookup

```markdown
By reviewing historical domain registration data such as registrant email addresses, phone numbers, and names, you can pivot to other domains registered under the same identity and uncover retired or forgotten "shadow domains." If you are lucky, some of these domains will still lead to active assets. Even if they do not, they may still be useful later during Host header collision testing.
```

```markdown
1. Query Historical WHOIS
A company's WHOIS data often changes over time, and sometimes the key fields change as well, such as the registrant email address, phone number, or name. Searching on those fields can reveal domains that were once registered by the company but never appeared in current filings. ThreatBook is recommended here because it suppresses less important changes and makes the useful pivots easier to spot.

# https://x.threatbook.com/
```

![image-20260522162057934](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522162057934.png?raw=1)

```markdown
2. Pivot to Related Domains
Once you obtain WHOIS data such as the registrant's email address, phone number, and name, use those values to pivot into additional registered domains. Some of these domains may still be active even though they no longer appear in the company's current filings, and the organization itself may have forgotten about them.

# https://x.threatbook.com/
```

![image-20260522162612812](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522162612812.png?raw=1)

# 0x02. Subdomain and IP Collection

```markdown
After completing `1. Root Domain and Enterprise Profiling`, you should already have the company's baseline information. The next step is to expand outward from those initial clues through a mix of passive and active discovery channels.
```

## 2.1. Passive Collection via Asset Search Platforms

```markdown
`Note: Passive collection should combine local tooling with external asset search platforms. Tool output should never be trusted blindly, and API-based collection can miss results for implementation reasons. Since most assets are discovered at this stage, coverage should be as broad as possible.
```

### 2.1.1. API-Driven Tooling

#### 1. Tscan (Paid Multi-Platform Sources)

```markdown
Tscan is listed first because it can quickly gather the information we usually need. When deeper collection is required, however, you should still fall back to the query syntax of the major asset search platforms. Unless the scenario really demands exhaustive coverage, there is usually no need to go overly deep here because it becomes very time-consuming.
```

```markdown
1. Configure the APIs of each platform
`Try to configure as many data sources as possible. Even if they add only a small number of assets, one of those extra results could still be the critical entry point.` Query asset data through ICP filings, the root domain, and certificate-bound domains.

2. Collect domains and IPs via ICP filing
Enter the main ICP filing number obtained during `1. Root Domain and Enterprise Profiling`, then select `Registration` as the query field. `Make sure to use the primary ICP filing number rather than a sub-filing number, and remember to enable the asset search platforms on the right. They are not all selected in this demo.` This usually returns a substantial amount of information tied to the filing number.
```

```markdown
# Download: https://github.com/TideSec/TscanPlus/releases
```

![image-20260514222949152](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514222949152.png?raw=1)

![image-20260514223544146](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514223544146.png?raw=1)

```markdown
3. Collect subdomains and IPs via the root domain
Enter the root domain obtained during `1. Root Domain and Enterprise Profiling`, then select `Domain` as the query field. `Remember to enable the asset search platforms on the right. They are not all selected in this demo.`
```

![image-20260514225322546](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514225322546.png?raw=1)

```markdown
4. Collect subdomains and IPs via certificates
Enter the root domain obtained during `1. Root Domain and Enterprise Profiling`, then select `Certificate` as the query field. `Remember to enable the asset search platforms on the right. They are not all selected in this demo.`

`Note: Some sites use wildcard certificates, and certificate matching on asset platforms can be fuzzy. For example, searching for app.com.cn may also return assets such as abcdefapp.com.cn. Those are not target assets, so the results still require manual review.`
```

![image-20260514230045555](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514230045555.png?raw=1)

#### 2. subfinder (Paid Multi-Platform Sources)

```markdown
0. You can locate the subfinder configuration file with the following command:
./subfinder -version

1. Configure the API keys for sources such as bevigil, censys, chaos, digitalyama, DNSDumpster, FOFA, GitHub, Hunter, IntelX, LeakIX, Netlas, Quake, rsecloud, Shodan, and ZoomEye, then run subfinder in full-collection mode.


2. Example command:
`./subfinder -dL domains.txt -rl 20 -all -json -o results.json

# Download: https://github.com/projectdiscovery/subfinder/releases
```

![image-20260515005949513](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515005949513.png?raw=1)

![image-20260514235657334](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514235657334.png?raw=1)

![image-20260514235823414](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514235823414.png?raw=1)

### 2.1.2. Asset Search Platforms

#### 1. Domain Search

```markdown
# Hunter 
domain.suffix="app.com.cn"
# Quake 
domain:"app.com.cn"
# FOFA
host="talentsec.cn"
domain="talentsec.cn"
```

![image-20260522175222606](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522175222606.png?raw=1)

#### 2. ICP Filing

```markdown
# Hunter 
icp.number="备案号"
# Quake 
icp:"Registration number"
# FOFA
icp="沪ICP备20019790号"
```

![image-20250710170452073](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710170452073.png?raw=1)

#### 3. `icon_hash`

```markdown
Based on the results collected from asset search platforms, save either the icon download path or the platform-provided `icon_hash` value for later use when pivoting to additional edge assets.
# Hunter (using favicon’s MD5 value)
web.icon="MD5"
# Quake (using favicon’s MD5 value)
favicon:"MD5"
# FOFA (using mmh3 algorithm)
icon_hash="585442251"
```

![image-20260522173706545](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522173706545.png?raw=1)

#### 4. `title` and `body` Queries

```markdown
1. title
# Hunter 
web.title="标题"
# Quake 
title:"标题"
# FOFA
title="螣龙安科"||title="螣龙安科，专注于新一代攻击面管理"
```

![image-20250711141408323](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711141408323.png?raw=1)

```markdown
2. body
`Note that the body query cannot use the title keyword, which will cause duplicate results.

# Hunter 
web.body="内容"
# Quake 
body:"内容"
# FOFA
body="螣龙安科是国内新一代主动安全领域的专精特新企业，致力于为客户提供专业的标准化产品与解决方案。"||body="螣龙安科，螣龙天眼，螣龙天眼ASM，螣龙攻击面管理系统"
```

![image-20250711141729654](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711141729654.png?raw=1)

## 2.2. Subdomain Brute-Forcing

```markdown
Principle: Search-engine crawlers, certificate transparency logs, and threat-intelligence feeds all have a time lag. If the target organization has just brought a new subdomain online, passive sources may not see it yet. Wordlist-based brute-forcing can often resolve it in near real time.
```

### 1. Findomain

```markdown
0. Download and load the Findomain configuration file manually. In practice, the compiled default configuration is usually sufficient.
https://github.com/Findomain/Findomain/tree/master/config_examples

1. Save the main domain name we obtained in "1. Main domain name & enterprise information collection" into domains.txt, configure Apikey, Findomain's configuration file can be run, and the dictionary can use Tscan's dictionary

2. Command
./findomain --file domains.txt --wordlist subnames-9.5w.txt --config config.example.yml --resolved --output
```

```markdown
# Download: https://github.com/Findomain/Findomain
```

![image-20260515001750864](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515001750864.png?raw=1)

![image-20260515010639947](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515010639947.png?raw=1)

### 2. OneForAll

```markdown
0. Configure the OneForAll API settings in `config/api.py`.

1. Save the main domain name we obtained in "1. Main domain name & enterprise information collection" into domains.txt, configure Apikey, OneForAll supports interface acquisition + domain name blasting, and its dictionary blasting function is mainly used here.

2. Command
python oneforall.py --targets ./domains.txt --wordlist subnames-9.5w.txt --brute True run

`Note: Python tools should be run in isolated virtual environments whenever possible. Many of them require different dependency versions, and conflicts are common. A virtual environment keeps each tool self-contained.`
```

```markdown
# Download: https://github.com/shmilylty/OneForAll
```

![image-20260515004513027](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515004513027.png?raw=1)

![image-20260515004912996](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515004912996.png?raw=1)



## 2.3. Certificate Transparency Search

```markdown
Principle: To reduce abuse by certificate authorities, TLS certificates issued by mainstream trusted CAs are generally subject to Certificate Transparency policies. As a result, many public-facing subdomains leave permanent records in CT logs. `That means that if the target organization has ever requested an HTTPS certificate for a subdomain, there is a good chance that subdomain has already been exposed through CT data.`
```

### 1. `crt.sh`

```markdown
# https://crt.sh/?q=talentsec.cn
Call the certificate binding interface to directly query the primary domain name to obtain domain name information bound to the certificate history.
```

![image-20250710135029477](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710135029477.png?raw=1)

### 2. SSLMate

```markdown
# https://sslmate.com/ct_search_api/
Call the certificate binding interface to directly query the primary domain name to obtain the domain name information bound to the certificate history.
```

![image-20250710183745615](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710183745615.png?raw=1)

## 2.4. Threat Intelligence Search

```markdown
Principle: Threat-intelligence platforms process massive volumes of DNS activity, malware callback traffic, and security-device telemetry every day. Those datasets contain a large amount of domain-resolution activity. `As a supplementary data source, threat-intelligence searches can reveal subdomains and related infrastructure that are difficult to uncover with conventional methods, and sometimes even expose deeply hidden subdomains that do not show up in brute-force or search-engine results.`
```

### 1. ThreatBook (Paid)

```markdown
# ThreatBook Community, https://x.threatbook.cn/
Search for `root domain` to get subdomain information
```

![image-20260515102718931](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515102718931.png?raw=1)

### 2. Qianxin Threat Intelligence (Login Required)

```markdown
# Qianxin Threat Intelligence Center https://ti.qianxin.com/
Searching for the `root domain` can return `CNAME` records and associated domains, which often include useful subdomains.
```

![image-20250710144758100](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710144758100.png?raw=1)

```markdown
2. Search for the `root domain`, then open the `Associated Domains` section to extract more subdomain information.
```

![image-20250710144930616](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710144930616.png?raw=1)

### 3. 360 Threat Intelligence (Login Required)

```markdown
# 360 Threat Intelligence Center https://ti.360.cn/domain/talentsec.cn
1. Search for the `root domain` to retrieve the corresponding subdomain information.
```

![image-20250710150425169](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710150425169.png?raw=1)

## 2.5. Search Engine Queries

```markdown
# https://www.google.com
In asset mapping and penetration testing, using the Google search engine (a technique called Google Hacking or Google Dorking) to query target websites is an extremely critical "passive information collection" technique.

Use `site:app.com.cn -www` to search pages related to the target domain while excluding the `www` host. Google API integration is cumbersome, so a browser extension such as `Google SERP Scrapper` can be used to collect results.
```

![image-20260515110713488](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515110713488.png?raw=1)

![image-20260514142604541](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260514142604541.png?raw=1)

## 2.6. Historical DNS Queries (Paid Membership Required)

```markdown
During testing or in the early stages of deployment, services are often pointed directly at the origin server before a CDN is introduced. Historical DNS records can help you recover those old IP addresses and deprecated domain names. Such "shadow assets" are often unmaintained, missing security patches, and sometimes still exposed with weak credentials or unauthorized access, making them valuable entry points during offensive assessments.
```

### 1. DNSDumpster

```markdown
# https://dnsdumpster.com
Search either the `root domain` or a `subdomain` directly to review historical DNS records and identify previously configured domains and IP addresses.
```

![image-20250710140028105](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710140028105.png?raw=1)

![image-20250710140550324](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710140550324.png?raw=1)

### 2. ViewDNS

```markdown
# https://viewdns.info/iphistory/?domain=talentsec.cn
```

![image-20250711150618586](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711150618586.png?raw=1)

### 3. SecurityTrails

```markdown
# https://securitytrails.com/domain/talentsec.cn/history/a
```

![image-20250711151918700](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711151918700.png?raw=1)

![image-20250711152522943](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711152522943.png?raw=1)

## 2.7. Extract Candidate /24 Ranges (Exclude CDN Addresses)

```markdown
In the actual operation and maintenance of enterprises, in addition to the official website for external services that is bound to domain names, there are also a large number of internal systems and infrastructure that are never bound to domain names and are only accessed through IP.
# Just choose one route
```

### 2.7.1. Route One: ASN-Based Collection

```markdown
`This method is best suited to very large enterprises, because they often operate their own ASNs. IP ranges announced under those ASNs are usually self-owned assets, making this approach more accurate and more complete than simple /24 aggregation.
```

#### 1. Confirm the company’s ASN number

```markdown
First identify the company's English name. You can usually find it by searching the company on Google, Wikipedia, or Baidu Baike. Then search that English name on `https://bgp.he.net/`. `Be careful with fuzzy matches here. Only keep entries that are clearly and exactly associated with the target.`

# https://bgp.he.net/
```

![image-20260522111455869](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522111455869.png?raw=1)

```markdown
If you cannot identify the company's English name directly, start with an IP that is already confirmed to belong to the target and use it to pivot into the ASN.

`Command 1: whois 103.92.88.7 | grep -i "origin\|netname\|descr"
`Command 2: curl https://ipinfo.io/103.92.88.7 | jq '.org'`
```

![image-20260522111623050](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522111623050.png?raw=1)

#### 2. Enumerate IP Prefixes Under the ASN

```markdown
Search the ASN directly on the website and open `Prefixes v4`, or enumerate the prefixes with command-line queries.

# https://bgp.he.net/
# whois -h whois.radb.net -- '-i origin AS38378' | grep -E "^route|^descr|^origin"
```

![image-20260522112057484](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522112057484.png?raw=1)

![image-20260522114038217](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522114038217.png?raw=1)

```markdown
`Be aware that a single ASN can cover multiple organizations. If the scope is limited to Bosch China, for example, IP ranges that belong to Bosch Home Appliances should be excluded.`
```

![image-20260522112151408](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260522112151408.png?raw=1)

### 2.7.2. Route Two: Data Aggregation

```markdown
# https://github.com/EdgeSecurityTeam/Eeyes

1. Domain Name Summary Section C
Enter the subdomain name collected in the previous steps and extract segment C assets through `./Eeyes-darwin -l domain.txt`
`Supports excluding CDN assets. Note that the IP must appear more than 3 times. If the number of times is small, it is easy to collect data incorrectly.
```

![image-20250711171614581](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711171614581.png?raw=1)

```markdown
2. IP summary segment C
Then use Tscan, enter the IP collected in the previous step (after deduplication), and extract the C segment addresses that appear more than 3 times.
```

![image-20260515134007355](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515134007355.png?raw=1)



## 2.8. IP Asset Collection

```markdown
# Just choose one route
```

### 2.8.1. Route One: Asset Search Platforms

```markdown
Because the first route relies on a relatively narrow data source, it usually needs to be combined with other methods. The second route uses existing asset search platforms directly. For example, you can feed a candidate /24 range into an asset platform, list the live IP assets, and inspect reverse domains, ownership data, and related metadata.
```

```markdown
Recommended use:
# Hunter https://hunter.qianxin.com/
# Quake https://quake.360.net/quake/#/
# FOFA https://fofa.info/
# ThreatBook https://x.threatbook.com/v5/survey?q=ip%3D%22122.194.76.0%2F24%22
```

![image-20260519215634936](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260519215634936.png?raw=1)

![image-20260519220123946](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260519220123946.png?raw=1)

### 2.8.2. Route Two: Tool-Assisted Workflow

#### 1. Live Host Discovery for Candidate /24 Ranges

```markdown
Probe the candidate /24 ranges for live hosts first, starting with a reduced port list to identify reachable IPs.
`This step is critical. You need to confirm which assets are actually alive before moving on to reverse-IP checks and full port scans. The main purpose is to save time.`
```

![image-20260515135058632](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515135058632.png?raw=1)

#### 2. IP Ownership Verification

```markdown
Because /24-based collection can drift into unrelated assets, you need to verify ownership after live-host discovery. Check whether the range belongs to the target's dedicated line and whether the resolved domains still point to the organization. `While validating ownership, it is also worth performing reverse-domain checks because newly discovered IPs may lead to additional domains.`

• Exclude: obvious CDN IP addresses.
• Exclude: third-party public-service IP addresses.
• Keep: ASN ranges and dedicated-line ranges registered under the target organization's name.
• Keep: cloud server ranges that cannot be attributed with certainty but repeatedly host core target subdomains discovered earlier.
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

#### 3. Full Port Scanning

```java
Perform a full port scan on the IP reserved above. The final output result is that the IP + port assets need to be integrated with the previously collected assets. The subsequent fingerprint identification will not be demonstrated here.
```

![image-20260515140136476](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515140136476.png?raw=1)



## 2.9. JavaScript File Discovery (Time-Consuming)

```markdown
Use all previously collected URLs as input, crawl the site's front-end JavaScript files, extract embedded URLs from those scripts, and then filter the results to identify new subdomains.
```

#### 1. URLFinder

```markdown
1. The example below shows URLFinder crawling a single site. In practice, it is better to process multiple sites together.
■ Key advantage: after extracting API paths, you can combine them with a custom dictionary for fuzzing or status-code checks to confirm whether those interfaces are actually reachable.

Command:
`./URLFinder-macos-arm64 -u https://www.talentsec.cn/ -s all -m 2`
```

```markdown
# Download: https://github.com/pingc0y/URLFinder
```

![image-20250710193348603](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250710193348603.png?raw=1)

#### 2. JSFinder

```markdown
1. JSFinder can also crawl JavaScript files and extract domains.
■ Key advantage: it supports recursive crawling. When it finds a new URL inside a script, it can continue following that URL to locate additional JavaScript files.

Command:
`python JSFinder.py -u https://www.talentsec.cn/`
```

```markdown
# Download: https://github.com/Threezh1/JSFinder
```

![image-20250711095243612](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711095243612.png?raw=1)

#### 3. FindSomething (Single-Site Use)

```markdown
1. Introduced as a browser extension
■ Outstanding advantages: It supports manual triggering of DOM nodes to load JS, but it cannot be crawled in batches due to manual triggering of DOM, so it can be used in daily penetration.
```

```markdown
# Download: https://github.com/momosecurity/FindSomething
```

![](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711094929647.png?raw=1)

## 2.10. Directory Scanning

```markdown
The goal of directory scanning is to identify sensitive paths and files on the target website that are not exposed through normal links but do exist and can be accessed directly.

1. In practice, backend-oriented wordlists are usually enough. Try routing through a proxy pool when needed, because some paths may trigger WAF rules. Thread counts should also stay relatively low.
```

```markdown
# Download: https://github.com/TideSec/TscanPlus/releases
```

![image-20260515145923699](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515145923699.png?raw=1)

![image-20260515150041247](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515150041247.png?raw=1)

```markdown
For example, this site "/index" and "/admin" can be regarded as two sites. They can not only be leaked as background login pages, but also can be distinguished as URL assets.
```

![image-20250711114145139](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711114145139.png?raw=1)

![image-20250711114318160](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711114318160.png?raw=1)

## 2.11. Host Header Collision (For Inaccessible Assets)

```markdown
When a domain no longer resolves publicly but Host-based virtual-host routing is still retained in a web server, reverse proxy, load balancer, or CDN origin configuration, manually mapping the domain to a candidate IP may still hit the correct route and expose the target service. `That is why direct IP access may fail and direct domain access may also fail, while the right domain-plus-IP combination still reaches the site.`

Step 1: Collect the IP address of the reverse proxy server
Step 2: Collect domain names with abnormal resolution and resolve them to domain names in the intranet
Step 3: Manually resolve the domain name to a certain IP, use Cartesian product collision, and match pairs until the domain name can access a certain system
```

#### 1. HostCollision

```markdown
From the inaccessible domain name, perform a collision test `java -jar HostCollision.jar -ipFilePath ip.txt  -hostFilePath host.txt` with all IPs. After the collision is successful, you can construct the request like this and fill in the real host. The following demonstrates the difference between configuring the correct host and not configuring the host.
```

```markdown
# Download: https://github.com/pmiaowu/HostCollision
```

![image-20250711133826918](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20250711133826918.png?raw=1)

![image-20260515180645244](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515180645244.png?raw=1)

![image-20260515180122831](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515180122831.png?raw=1)

#### 2. Tscan

```markdown
Tscan has also added a host collision function. You only need to paste the IP and domain name into it and run it.
```

![image-20260515180948072](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260515180948072.png?raw=1)

# 0x03. Social Media Accounts and Apps

## 3.1. Official Accounts, Mini Programs, and Service Accounts

```markdown
There used to be a useful service called `Newrank` that could collect historical WeChat public-account data, but it has shifted toward a B2B model and is no longer practical for individual users. In most cases, directly searching the target company's name within WeChat is sufficient for this step.

Search example: Jinguang Paper (China) Investment Co., Ltd.
```

![image-20260518133144273](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260518133144273.png?raw=1)

## 3.2. Apps

```markdown
1. Identify the app name
Search for the company name directly in app stores. When an application is published, the developer identity is often tied to the company itself. Be sure to check multiple app stores, because test builds are sometimes released only on specific platforms.
```

```markdown
# Diandian Data: https://www.diandian.com/
# Qimai Data: https://www.qimai.cn/account/setting
```

![image-20260518134539530](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260518134539530.png?raw=1)

![image-20260518140149901](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260518140149901.png?raw=1)

![image-20260518140253691](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260518140253691.png?raw=1)

# 0x04. Sensitive Information

## 4.1. Google Hacking

```markdown
`Tips: You can make the following syntax into a skill, no need to replace it manually, and put the assets into the model with new syntax.
```

### 1. Management Portal Discovery

```markdown
Set the target scope to the root domain and confirmed IP addresses. This is a fast way to uncover exposed OA systems, CMS backends, and test-environment management portals on the public internet.

# Note that using wildcard * may collect other assets. It is recommended to use the precise IP if there is a confirmed asset IP.
(site:app.com.cn OR site:122.194.76.10) (intitle:"管理" OR intitle:"后台" OR intitle:"登录" OR intitle:"admin" OR intitle:"login" OR intitle:"dashboard" OR intitle:"system" OR intitle:"portal" OR intitle:"manage" OR inurl:admin OR inurl:login OR inurl:manage OR inurl:dashboard)
```

### 2. Unauthenticated Interface Discovery

```markdown
You can find common interfaces, monitoring panels, and debugging pages where unauthorized access or sensitive information leakage occurs due to improper enterprise configuration.
# Note that using wildcard * may collect other assets. It is recommended to use the precise IP if there is a confirmed asset IP.
(site:app.com.cn OR site:122.194.76.10) (inurl:swagger OR inurl:api-docs OR intitle:"Swagger UI" OR inurl:graphql OR inurl:graphiql OR inurl:altair OR inurl:v2/api-docs OR inurl:v3/api-docs OR inurl:postman OR inurl:doc.html)
```

![image-20260519182823014](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260519182823014.png?raw=1)

### 3. Unauthenticated Microservice and Java Middleware Exposure

```markdown
These dorks are tailored to the Java ecosystem and are useful for finding Spring Boot monitoring endpoints, Alibaba Druid dashboards, service registries, configuration centers, and similar middleware components.
# Note that using wildcard * may collect other assets. It is recommended to use the precise IP if there is a confirmed asset IP.
(site:app.com.cn OR site:122.194.76.10) (inurl:actuator OR inurl:druid OR inurl:nacos OR intitle:"Eureka" OR intitle:"Spring Boot Admin" OR inurl:jolokia OR inurl:dubbo OR inurl:weblogic OR inurl:jmx-console OR intitle:"Tomcat" OR intitle:"Welcome to JBoss")
```

### 4. Operations and Monitoring Systems

```markdown
Match the characteristic pages of operational infrastructure such as Kibana for logging, Zabbix for monitoring, Jenkins for CI/CD, and GitLab for source control. These systems are often meant for internal use only, so once they are exposed to the public internet they frequently become strong entry points.

# Note that using wildcard * may collect other assets. It is recommended to use the precise IP if there is a confirmed asset IP.
1. (site:app.com.cn OR site:122.194.76.10) (intitle:"Kibana" OR inurl:kibana OR intitle:"Zabbix" OR inurl:jenkins OR intitle:"Dashboard [Jenkins]" OR intitle:"GitLab" OR intitle:"Gitea" OR inurl:harbor OR inurl:solr OR inurl:phpmyadmin OR intitle:"phpMyAdmin" OR inurl:adminer OR intitle:"Hadoop" OR intitle:"Apache Flink" OR intitle:"Spark" OR intitle:"RabbitMQ Management" OR inurl:flower)

2. (site:app.com.cn OR site:122.194.76.10) (inurl:"/phpinfo.php" OR intitle:"phpinfo()" OR inurl:"/server-status" OR inurl:"/server-info" OR intitle:"Apache Status")
```

### 5. Test Environments

```markdown
Matches pages containing development/testing feature words in the URL path. This type of environment is generally used for pre-launch testing. It is usually not connected to WAF, and there may be hard-coded passwords or backdoor interfaces left to facilitate debugging, which is also one of the breakthrough points.

(site:app.com.cn OR site:yicongfound.org OR site:122.194.76.10) (inurl:test OR inurl:dev OR inurl:staging OR inurl:demo OR inurl:uat OR inurl:beta OR inurl:sandbox OR inurl:pre OR inurl:local)
```

### 6. Sensitive Files

```markdown
Forcing Google to only return files in a specified format will inevitably store some documents with unauthorized access in enterprise operation and maintenance. In particular, storage buckets often store some unauthorized documents, which are directly exposed after being crawled by crawlers.
# Note that using wildcard * may collect other assets. It is recommended to use the precise IP if there is a confirmed asset IP.
1. (site:app.com.cn OR site:122.194.76.10) (ext:pdf OR ext:doc OR ext:docx OR ext:xls OR ext:xlsx OR ext:ppt OR ext:pptx OR ext:csv OR ext:txt)

2. (site:app.com.cn OR site:122.194.76.10) ((ext:xls OR ext:xlsx OR ext:csv OR ext:txt) (intext:"密码" OR intext:"账号" OR intext:"通讯录" OR intext:"联系方式" OR intext:"机密" OR intext:"内部" OR intext:"confidential" OR intext:"credentials" OR intext:"token"))

3. (site:app.com.cn OR site:122.194.76.10) (ext:sql OR ext:tar.gz OR ext:zip OR ext:rar OR ext:bak OR ext:7z OR ext:dump OR ext:db OR ext:sqlite OR ext:gz OR ext:bz2 OR ext:tar)

4. (site:app.com.cn OR site:122.194.76.10) (ext:env OR ext:ini OR ext:conf OR ext:config OR ext:xml OR ext:properties OR ext:yml OR ext:yaml OR ext:json OR ext:cfg OR ext:toml)

5. (site:app.com.cn OR site:122.194.76.10) (ext:pem OR ext:crt OR ext:key OR ext:p12 OR ext:pub OR ext:ovpn OR ext:jks OR ext:keystore OR intext:"BEGIN RSA PRIVATE KEY" OR intext:"BEGIN OPENSSH PRIVATE KEY")

6. (site:app.com.cn OR site:122.194.76.10) (ext:log OR inurl:log OR inurl:logs OR (ext:txt (intext:"Exception" OR intext:"Error")))

7. (site:app.com.cn OR site:122.194.76.10) (inurl:.git OR inurl:.svn OR inurl:.DS_Store OR inurl:.hg OR inurl:.vscode OR inurl:.idea)
```

### 7. Directory Listings

```markdown
This title will appear when the web server is not configured with a default home page and allows directory listing, and you can directly browse the file structure on the server.
# Note that using wildcard * may collect other assets. It is recommended to use the precise IP if there is a confirmed asset IP.
(site:app.com.cn OR site:122.194.76.10) (intitle:"index of" OR intitle:"Directory Listing For")
```

### 8. Error Page Discovery

```markdown
These queries target distinctive application error messages. They are not the highest-yield technique, but they can still help confirm the target's database stack and application framework.

(site:app.com.cn OR site:yicongfound.org OR site:122.194.76.10) (intext:"Fatal error:" OR intext:"Warning: mysql_connect()" OR intext:"stack trace:" OR intext:"Syntax error" OR intext:"java.lang.NullPointerException" OR intext:"SQL syntax" OR intext:"Exception in thread")
```

### 9. Third-Party Collaboration Platform Exposure

```markdown
Search third-party cloud collaboration platforms with the target organization's most distinctive keywords. This can expose project boards, code snippets, test accounts, or other development artifacts associated with the target.

# Note: The following syntax has a large search range, and the keywords must be adjusted in time to be as accurate as possible.
(site:trello.com OR site:atlassian.net OR site:notion.so OR site:shimo.im OR site:yuque.com OR site:processon.com OR site:github.com) ("yicongfound" OR "金光paper" OR "sinarmas")
```

## 4.2. Cloud Drive Search

```markdown
For the convenience of work, development, operation and maintenance or outsourcing personnel often package and upload the company's internal source code, network topology diagram, VPN account or handover documents to Baidu Cloud Disk, Alibaba Cloud Disk, or record them in cloud notes such as Yuque.

# Lingfengyun: https://www.lingfengyun.com/
# Lzpanx: https://www.lzpanx.com/
# Dalipan: https://www.dalipan.com/
```

```markdown
1. Find network topology and IT architecture
Sin Guang Paper Topology
Sinar Mas Group Structure
APP China IP Planning
sinarmas topology
yicongfound architecture diagram
 
2. Look for system handover and configuration documents
Sinar Paper VPN
Sinar Mas Group Fortress Machine
APP China Password
sinarmas account handover
yicongfound configuration document
 
3. Find core code and backup data
Jin Guang Paper source code
APP China database backup
sinarmas sql backup
yicongfound code
 
4. Look for internal administration/address book leaks (social engineering tool)
Sin Guang Paper Address Book
Sinar Mas Group Roster
APP China Organizational Structure
sinarmas employee list
```

![image-20260519234939022](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260519234939022.png?raw=1)

![image-20260520232223400](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260520232223400.png?raw=1)

## 4.3. Email Collection

```markdown
Email collection can usually be done through third-party services. Most of them search by email domain, so before using them you should first determine the company's primary mail domain during `1. Root Domain and Enterprise Profiling`. These services crawl or enumerate mailbox data across the internet, which saves time and can produce a surprisingly large dataset if the underlying sources are strong.

# https://app.snov.io/domain-search?name=app.com.cn&tab=emails
# https://phonebook.cz/
# http://www.skymem.info/
# https://hunter.io/dashboard
# https://www.email-format.com/i/search/
# telegram social engineering library (I won’t list the legal risks, just google a bunch of them)
```

![image-20260520232737788](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260520232737788.png?raw=1)

![image-20260520233326221](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260520233326221.png?raw=1)

## 4.4. Source Code Acquisition

### 1. Fingerprinting

```markdown
Use tools such as Tscan and Wappalyzer to identify the product's technology stack and fingerprint information.

# Tscan：https://github.com/TideSec/TscanPlus/releases
# Wappalyzer： https://chromewebstore.google.com/detail/wappalyzer-technology-pro/gppongmhjkpfnbhagpmjfkannfbllamg?hl=zh-CN&utm_source=ext_sidebar
```

![image-20260528100339175](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528100339175.png?raw=1)

![image-20260528094848203](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528094848203.png?raw=1)

### 2. Open-Source Products

```markdown
• For open-source products, GitHub and Gitee repositories can usually be cloned directly for auditing.

# https://github.com/
# https://gitee.com/
```

![image-20260528104919491](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528104919491.png?raw=1)

### 3. Commercial Products

```markdown
• Commercial products are sometimes circulated on resale or underground platforms. I am not listing some of the riskier links here for legal reasons, but they can be found if you search carefully.
	
# https://www.huzhan.com/
# https://28xin.com/
# https://bbs.bcb5.com/
# https://darkforums.su/
```

![image-20260528111615305](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528111615305.png?raw=1)

![image-20260528112037293](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528112037293.png?raw=1)

### 4. Customized or In-House Systems

```markdown
# Practical skills:
■ After searching the results, click into the repository to view the commit history. Many people have deleted the files after discovering the leak, but the history is still there.
■ Look at other repositories of this developer. If one person leaks one leak, there is a high probability that there will be other leaks.
■ Pay attention to the fork's warehouse. The original warehouse has been deleted but the fork is still there.
```

```markdown
1. Find website characteristics
■ Copyright information at the bottom of the page: "Powered by XXX" / "Copyright 2023 XX Technology"
■ Unique JS/CSS path: /static/js/chunk-2d0a1f3.js
■ Unique API path:/api/v2/internal/xxx
■ The system name in the page title or meta tag
■ Unique copy/image path for login page
```

```markdown
2. Search GitHub/grep.app for features
# Search copyright information
"XX Technology" OR "Powered by XXX"
# Search for unique API paths (most effective because the API paths are hard-coded in the code)
"/api/v2/internal/dashboard"
# Search for unique JS file names or path structures
"chunk-2d0a1f3" OR "/static/admin/js/"
# Search for unique Chinese copywriting on the page
"Welcome to XX management platform"
```

```markdown
3. Scan directories to find source code leaks
Directory scanning can sometimes scan source code backups or configuration files that developers have missed, and some configuration files can restore the source code. The principle is to use directory scanning and tool dump source code. You can refer to an article I saw before:
# https://juejin.cn/post/6950954967435837454
■ git source code leaked
■ svn source code leaked
■ hg source code leaked
■ Website backup compressed file
■ WEB-INF/web.xml leaked
■ DS_Store file leak
■ SWP file leak
■ CVS leak
■ Bzr leaked

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



## 4.5. Deep Web Leak Intelligence

```markdown
1. IntelX aggregated search engine
What makes this platform useful is that it aggregates a large archive of historical leaks from the deep web and underground forums. In many cases, the affected party is a third-party supplier associated with the target, and leaked credentials from those suppliers can still be valuable. The downside is that the platform is expensive.

Search: app.com.cn, @app.com.cn, company name
# https://intelx.io/
```

![image-20260528123220419](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528123220419.png?raw=1)

```markdown
2. Deep Web
Search: app.com.cn, @app.com.cn, company name
# https://darkforums.su/
# https://pwnforums.st/
# https://www.demonforums.net/

3. A network
xss.is
KillSec
darknetarmy
Chang'an never sleeps city
Chinese A network trading market
RansomHouse
```

![image-20260528133024298](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528133024298.png?raw=1)

## 4.6. Cloud Storage Bucket Enumeration

```markdown
Principle: Cloud storage buckets on Alibaba Cloud OSS, AWS S3, Tencent COS, and Huawei OBS are frequently exposed because of access-control mistakes. Bucket names are globally unique, so combining enterprise keywords with common suffixes is an effective way to discover forgotten or misconfigured storage assets.

# Note: I originally considered explaining the full bucket-enumeration workflow in prose, but it felt more practical to turn the process into a tool and publish it on GitHub for direct use.
# https://github.com/d0ctorsec/BucketHunter
```

```markdown
1. Generate a bucket name wordlist
Extract features from the subdomains and enterprise keywords collected in earlier stages, then generate a bucket name wordlist. Launch the interactive terminal, choose the first step for dictionary generation, and fill in the required inputs.
`./BucketHunter 

• Enterprise keywords (company abbreviation, product name, project codename)
• A list of subdomains collected earlier
```

![image-20260602134401499](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260602134401499.png?raw=1)

```markdown
2. Multi-cloud bucket scanning
The tool uses the open-source project `S3Scanner` to batch-scan the generated wordlist across multiple cloud providers. In practice, this often turns up buckets with readable objects or unauthorized write permissions. Because fuzzy matches introduce a lot of noise, the results should be reviewed together with other asset context and AI-assisted scoring.

`Select step two in the interactive terminal and start the scan.`
```

![image-20260602134738849](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260602134738849.png?raw=1)

![image-20260528144231746](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528144231746.png?raw=1)

```markdown
3. AI-assisted analysis
`After the second step is complete, AI can analyze the results and remove obviously irrelevant entries. To improve accuracy, provide the required context before starting the analysis. The AI output should still be manually reviewed and verified afterward. In the demonstration, the customer domain is relatively generic, yet the AI can still provide a useful probability-based assessment.`
```

![image-20260602135222516](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260602135222516.png?raw=1)

![image-20260602135336246](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260602135336246.png?raw=1)

![image-20260528144503357](https://github.com/d0ctorsec/IMG_File1/blob/main/%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E8%BE%B9%E7%BC%98%E8%B5%84%E4%BA%A7%E6%8C%96%E6%8E%98/image-20260528144503357.png?raw=1)