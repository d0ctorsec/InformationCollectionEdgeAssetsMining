<p>
  <a href="https://github.com/d0ctorsec/BucketHunter/stargazers"><img src="https://img.shields.io/badge/Thanks%20for%20a%20Star-ff4d8d?style=for-the-badge&logo=github" alt="Thanks for starring"></a>
  <a href="./README.md"><img src="https://img.shields.io/badge/Chinese-README-blue?style=for-the-badge" alt="Chinese README"></a>
  <a href="./README_EN.md"><img src="https://img.shields.io/badge/English-README-black?style=for-the-badge" alt="English README"></a>
</p>

# Information Gathering and Edge Asset Discovery Handbook

This is the English edition of [README.md](./README.md). The original article is a 20,000-word handbook on information gathering and edge asset discovery, focused on building a practical reconnaissance workflow for finding hidden external assets.

## References

1. https://cloudsec.huoxian.cn/docs/articles/aliyun/aliyun_oss#2bucket%E6%A1%B6%E7%88%86%E7%A0%B4
2. https://juejin.cn/post/6950954967435837454
3. https://websec.readthedocs.io/zh/latest/info/index.html
4. https://cloud.tencent.com/developer/article/1459303
5. https://mp.weixin.qq.com/s/LE37OcNYWC9rFH8qeiYsmA

## 0x00 Preface

In a recent pre-sales project, the client required us to collect hidden assets. Traditional information gathering usually relies on many platforms and tools for cross-validation, while notes, screenshots, and utilities are scattered across different modules. That makes the workflow inefficient. I therefore reorganized the overall thought process into a single article, removed repeated or outdated methods, and turned it into a practical end-to-end workflow.

The sections in this article are already ordered by priority. Methods listed earlier in each module are generally more recommended. In most cases, choosing one or two effective methods per module is enough. Unless the target is especially important, it is not necessary to use every single approach because doing so is very time-consuming.

## 0x01 Root Domain and Enterprise Profiling

### 1.1 Enterprise Information Lookup

If the scope includes subsidiaries or parent companies, clarify the equity threshold with the client first. Then use enterprise-information platforms and equity penetration graphs to collect subsidiary names and domains. There is no perfect tool for this step, so manual review is still reliable. When the organizational structure is large, `Tscan` and `ENScan_GO` are currently good choices.

#### 1. Tscan

Configure the target site's cookie and API key, then input the company name to collect basic enterprise information.

Download:

```text
https://github.com/TideSec/TscanPlus/releases
```

#### 2. ENScan_GO

After downloading, configure the API and cookie before use. The following is a commonly used command. If errors occur, adjust it according to your environment and config file.

```bash
./enscan-v2.0.5-darwin-arm64 -n 北京金山云网络技术有限公司 -field icp,weibo,wechat,app,job,wx_app,copyright,supplier -type tyc,chinaz -timeout 30 --hold --supplier --branch
```

Download:

```text
https://github.com/wgpsec/ENScan_GO
```

### 1.2 Query the Root Domain via ICP Filing

After section `1.1` gives you the company name and part of the filing information, go directly to the Ministry of Industry and Information Technology ICP platform and search the company name. This allows you to retrieve the currently filed root domain. You need to click `Details`. This source is highly time-sensitive and is the primary data source for filing updates.

```text
https://beian.miit.gov.cn/
```

### 1.3 Historical WHOIS and Reverse Lookup

By examining historical WHOIS records, such as registrant email addresses, phone numbers, and names, you can pivot to other domains registered by the same entity and discover deprecated or forgotten "shadow domains". If you are lucky, some of these domains still resolve to assets. Even if they do not, they can still be useful later for Host-header collision testing.

#### 1. Historical WHOIS Lookup

WHOIS records often change over time. Sometimes key values change, such as registrant email, phone number, or name. Searching these fields can reveal domains that were previously registered by the company but are no longer filed. ThreatBook is recommended here because it hides less relevant changes and highlights useful ones.

```text
https://x.threatbook.com/
```

#### 2. Reverse Lookup of Associated Domains

Once you obtain WHOIS data such as registrant email addresses, phone numbers, and names, use them to reverse-search other domains registered under the same identity. Some of those domains may still be active but absent from the current ICP filing.

```text
https://x.threatbook.com/
```

## 0x02 Subdomain and IP Collection

Once root domains and basic enterprise data are collected, use them as seeds to expand the attack surface through multiple passive and active channels.

### 2.1 Passive Collection Through Asset Search Engines

Passive collection should combine tools and asset-mapping platforms. Do not fully trust tool output. API-based tools may miss results because of implementation issues, and most assets are discovered during this phase, so broad coverage matters.

#### 2.1.1 Tools That Call Multiple APIs

##### 1. Tscan

Tscan is placed first because it can quickly gather the information you need. For deeper collection, however, you should still use the syntax of multiple asset-mapping platforms.

1. Configure all available API keys.
2. Collect domains and IPs through ICP numbers. Use the main filing number rather than a sub-filing number.
3. Collect subdomains and IPs with the root domain.
4. Collect subdomains and IPs with the certificate field.

Note: wildcard certificates and fuzzy matching can introduce noise. For example, searching `app.com.cn` may return `abcdefapp.com.cn`, which is not a target asset, so manual review is required.

Download:

```text
https://github.com/TideSec/TscanPlus/releases
```

##### 2. subfinder

`subfinder` is a passive subdomain tool that supports many commercial and community APIs.

1. Check where the config file is located:

```bash
./subfinder -version
```

2. Configure as many APIs as possible, including `bevigil`, `censys`, `chaos`, `digitalyama`, `dnsdumpster`, `fofa`, `github`, `hunter`, `intelx`, `leakix`, `netlas`, `quake`, `rsecloud`, `shodan`, and `zoomeyeapi`.

3. Example command:

```bash
./subfinder -dL domains.txt -rl 20 -all -json -o results.json
```

Download:

```text
https://github.com/projectdiscovery/subfinder/releases
```

#### 2.1.2 Asset Search Platforms

##### 1. Domain Search

```text
# Hunter
domain.suffix="app.com.cn"

# Quake
domain:"app.com.cn"

# FOFA
host="talentsec.cn"
domain="talentsec.cn"
```

##### 2. ICP Filing Search

```text
# Hunter
icp.number="filing-number"

# Quake
icp:"filing-number"

# FOFA
icp="沪ICP备20019790号"
```

##### 3. `icon_hash`

First save either the icon download path or the icon hash collected from asset platforms. They are useful later when searching for additional edge assets.

```text
# Hunter (MD5 of the favicon)
web.icon="MD5"

# Quake (MD5 of the favicon)
favicon:"MD5"

# FOFA (mmh3)
icon_hash="585442251"
```

##### 4. Search by `title` and `body`

Title search:

```text
# Hunter
web.title="title"

# Quake
title:"title"

# FOFA
title="螣龙安科"||title="螣龙安科，专注于新一代攻击面管理"
```

Body search:

```text
# Hunter
web.body="content"

# Quake
body:"content"

# FOFA
body="螣龙安科是国内新一代主动安全领域的专精特新企业，致力于为客户提供专业的标准化产品与解决方案。"||body="螣龙安科，螣龙天眼，螣龙天眼ASM，螣龙攻击面管理系统"
```

When using body search, avoid reusing title keywords; otherwise you often get duplicate results.

### 2.2 Brute-Forcing Subdomains

Passive sources such as search-engine crawlers, certificate transparency logs, and threat-intelligence databases all have a delay. If a target just configured a new subdomain, passive APIs may not see it yet. Wordlist-based brute-forcing can reveal it in real time.

#### 1. Findomain

`Findomain` supports both passive APIs and brute-force expansion.

1. Its config file must be downloaded manually. Use the compiled default config:

```text
https://github.com/Findomain/Findomain/tree/master/config_examples
```

2. Save the root domains gathered earlier into `domains.txt`, configure API keys, and reuse Tscan's wordlist if needed.
3. Example command:

```bash
./findomain --file domains.txt --wordlist subnames-9.5w.txt --config config.example.yml --resolved --output
```

Download:

```text
https://github.com/Findomain/Findomain
```

#### 2. OneForAll

`OneForAll` supports passive collection plus subdomain brute force. In this workflow, its brute-force capability is the main focus.

1. Configure API keys in `config/api.py`.
2. Save the root domains into `domains.txt`.
3. Example command:

```bash
python oneforall.py --targets ./domains.txt --wordlist subnames-9.5w.txt --brute True run
```

Important: for Python tools, it is better to run them in isolated virtual environments because dependency conflicts are common.

Download:

```text
https://github.com/shmilylty/OneForAll
```

### 2.3 Certificate Transparency Queries

To prevent certificate authorities from abusing certificate issuance, public TLS certificates issued by mainstream trusted CAs are generally subject to CT policies. As a result, many public-facing subdomains are recorded in CT logs permanently. If a company has ever applied for an HTTPS certificate for a subdomain, that subdomain may have already been exposed publicly through CT data.

#### 1. `crt.sh`

Query the root domain directly to retrieve domain names historically bound to certificates.

```text
https://crt.sh/?q=talentsec.cn
```

#### 2. SSLMate

Also useful for querying domains tied to historical certificates.

```text
https://sslmate.com/ct_search_api/
```

### 2.4 Threat Intelligence Queries

Threat-intelligence platforms process huge volumes of DNS requests, malware callback logs, and security-device telemetry. Those logs contain many domain-resolution traces. Threat-intelligence queries therefore work well as a supplementary source for finding hidden subdomains and related infrastructure.

#### 1. ThreatBook

Search the root domain to retrieve subdomain information.

```text
https://x.threatbook.cn/
```

#### 2. Qianxin Threat Intelligence

Search the root domain to obtain `CNAME` records and associated domains. The associated-domain module often includes useful subdomains.

```text
https://ti.qianxin.com/
```

#### 3. 360 Threat Intelligence

Search the root domain to retrieve subdomain information.

```text
https://ti.360.cn/domain/talentsec.cn
```

### 2.5 Search Engine Queries

Google search is a critical passive reconnaissance technique in both asset mapping and penetration testing. Use `site:` queries to find indexed pages related to the target domain and exclude `www` when necessary.

```text
https://www.google.com
site:app.com.cn -www
```

If Google API integration is too cumbersome, browser plugins such as `Google SERP Scrapper` can help capture results.

### 2.6 Historical DNS Records

When a service is first launched or tested, it is often pointed directly to the real origin server before a CDN is added. Historical DNS records can reveal those old IPs and forgotten domains. Such "shadow assets" are often poorly maintained and are therefore valuable entry points.

#### 1. DNSDumpster

Search the root domain or subdomain to inspect historical DNS records and identify related domains and IPs.

```text
https://dnsdumpster.com
```

#### 2. ViewDNS

```text
https://viewdns.info/iphistory/?domain=talentsec.cn
```

#### 3. SecurityTrails

```text
https://securitytrails.com/domain/talentsec.cn/history/a
```

### 2.7 Extract IP `C` Segments (Exclude CDN Ranges)

In real enterprise operations, a company often runs many internal systems and infrastructure nodes that are never bound to domains and are accessible only by IP. There are two common routes for collecting such IP ranges.

#### 2.7.1 Route One: ASN Collection

This route is mainly useful for very large companies that own ASNs. IP ranges under a company's ASN are more accurate and more complete than simple /24 aggregation.

##### 1. Identify the Company's ASN

Find the company's English name first. You can search it on Google, Wikipedia, or Baidu Baike. Then search the name on:

```text
https://bgp.he.net/
```

If the exact English name is unclear, take a confirmed company IP and reverse-check the ASN:

```bash
whois 103.92.88.7 | grep -i "origin\|netname\|descr"
curl https://ipinfo.io/103.92.88.7 | jq '.org'
```

##### 2. Retrieve IP Prefixes Under the ASN

Search the ASN on `bgp.he.net` and click `Prefixes v4`, or use:

```bash
whois -h whois.radb.net -- '-i origin AS38378' | grep -E "^route|^descr|^origin"
```

Be aware that a single ASN can contain multiple organizations. Keep only the ranges that really belong to the agreed target.

#### 2.7.2 Route Two: Data Merging

Use previously gathered domains and IPs to aggregate repeated `C` segments.

```text
https://github.com/EdgeSecurityTeam/Eeyes
```

1. Aggregate `C` segments from domains:

```bash
./Eeyes-darwin -l domain.txt
```

This also supports CDN exclusion. In practice, keep only IPs that appear more than three times to reduce noise.

2. Aggregate `C` segments from IPs:

Use Tscan to process the de-duplicated IP list and extract `C` segments that appear at least three times.

### 2.8 IP Asset Collection

Once candidate IP ranges are available, choose one of the following routes.

#### 2.8.1 Route One: Asset Search Platforms

Use platforms such as Hunter, Quake, FOFA, and ThreatBook to list live IP assets in a given `C` segment and inspect reverse domains and ownership information.

```text
Hunter: https://hunter.qianxin.com/
Quake: https://quake.360.net/quake/#/
FOFA: https://fofa.info/
ThreatBook: https://x.threatbook.com/v5/survey?q=ip%3D%22122.194.76.0%2F24%22
```

#### 2.8.2 Route Two: Tool-Based Workflow

##### 1. Liveness Detection for `C` Segments

Perform liveness checks on the candidate `C` ranges first, starting with a reduced port set. This identifies which IPs are worth deeper investigation.

##### 2. Confirm IP Ownership

Because `C`-segment collection can drift into unrelated assets, confirm ownership afterward.

Keep:

- ranges registered under the target company's name
- dedicated lines and self-owned ASN ranges
- cloud ranges that repeatedly host core target subdomains

Exclude:

- obvious CDN IPs
- public third-party service infrastructure

Useful lookup sites:

```text
https://tool.chinaz.com/same
https://dns.aizhan.com/
https://x.threatbook.com/v5/batchQuery
https://site.ip138.com/
https://www.yougetsignal.com/tools/web-sites-on-web-server/
```

##### 3. Full Port Scanning

After filtering the IPs, scan all ports. The final output is an `IP + port` asset list that should later be merged with the rest of your reconnaissance results.

### 2.9 JavaScript File Discovery

Use all previously collected URLs as input. Crawl front-end JavaScript files, extract embedded URLs, and then filter the output to identify new subdomains and endpoints.

#### 1. URLFinder

`URLFinder` can crawl a website and extract API paths from its JavaScript files. A practical advantage is that extracted paths can be combined with dictionaries for fuzzing and status checks.

```bash
./URLFinder-macos-arm64 -u https://www.talentsec.cn/ -s all -m 2
```

Download:

```text
https://github.com/pingc0y/URLFinder
```

#### 2. JSFinder

`JSFinder` can also crawl JavaScript files and extract domains. Its key advantage is recursive crawling: when it discovers a new URL from one JavaScript file, it can keep following it to locate more scripts.

```bash
python JSFinder.py -u https://www.talentsec.cn/
```

Download:

```text
https://github.com/Threezh1/JSFinder
```

#### 3. FindSomething

This is delivered as a browser extension. Its main advantage is that it can help with manual DOM-triggered JavaScript loading, which is highly useful in day-to-day penetration testing.

Download:

```text
https://github.com/momosecurity/FindSomething
```

### 2.10 Directory Scanning

Directory brute-forcing is intended to find sensitive paths and files that are not linked publicly but still exist on the server. In practice, use backend-oriented wordlists, small thread counts, and proxies if necessary because some requests can trigger WAF rules.

Download:

```text
https://github.com/TideSec/TscanPlus/releases
```

Some results, such as `/index` and `/admin`, may be better treated as separate URL assets rather than just pages under the same site.

### 2.11 Host Header Collision

When a domain no longer resolves in DNS but the upstream web server, reverse proxy, load balancer, or CDN origin still retains Host-based virtual-host routing, manually mapping a candidate domain to a candidate IP can still hit the correct route and expose the service.

The rough workflow is:

1. Collect IPs of reverse-proxy or candidate web servers.
2. Collect domains with broken or anomalous DNS records.
3. Test them in a Cartesian-product manner until a domain and IP combination reveals a valid service.

#### 1. HostCollision

Use inaccessible domains and all candidate IPs for collision testing:

```bash
java -jar HostCollision.jar -ipFilePath ip.txt -hostFilePath host.txt
```

Download:

```text
https://github.com/pmiaowu/HostCollision
```

#### 2. Tscan

Tscan also includes Host-header collision support. Paste the candidate IPs and domains into the interface and run it directly.

## 0x03 New Media Accounts and Mobile Apps

### 3.1 WeChat Public Accounts, Mini Programs, and Service Accounts

There used to be a useful service called `新榜` for collecting historical WeChat public-account data, but it has shifted toward B2B and is no longer practical for individual users. In many cases, directly searching the target company's name inside WeChat still works.

Example:

```text
金光纸业（中国）投资有限公司
```

### 3.2 Mobile Apps

#### 1. Obtain the App Name

Search the company name directly in app stores. Developers usually bind published apps to enterprise identities. Also switch among different app stores, because test apps are sometimes released only on specific platforms.

Useful sites:

```text
https://www.diandian.com/
https://www.qimai.cn/account/setting
```

## 0x04 Sensitive Information

### 4.1 Google Hacking

The following Google dorks are useful for different discovery goals.

#### 1. Search for Admin Panels

Quickly exposes OA systems, CMS backends, test environments, and management portals on the public internet.

```text
(site:app.com.cn OR site:122.194.76.10) (intitle:"管理" OR intitle:"后台" OR intitle:"登录" OR intitle:"admin" OR intitle:"login" OR intitle:"dashboard" OR intitle:"system" OR intitle:"portal" OR intitle:"manage" OR inurl:admin OR inurl:login OR inurl:manage OR inurl:dashboard)
```

#### 2. Search for Unauthenticated Interfaces

Find common unauthorized APIs, monitor panels, and debugging pages.

```text
(site:app.com.cn OR site:122.194.76.10) (inurl:swagger OR inurl:api-docs OR intitle:"Swagger UI" OR inurl:graphql OR inurl:graphiql OR inurl:altair OR inurl:v2/api-docs OR inurl:v3/api-docs OR inurl:postman OR inurl:doc.html)
```

#### 3. Search for Java Middleware and Microservice Exposure

Useful for Spring Boot endpoints, Druid dashboards, service registries, and middleware consoles.

```text
(site:app.com.cn OR site:122.194.76.10) (inurl:actuator OR inurl:druid OR inurl:nacos OR intitle:"Eureka" OR intitle:"Spring Boot Admin" OR inurl:jolokia OR inurl:dubbo OR inurl:weblogic OR inurl:jmx-console OR intitle:"Tomcat" OR intitle:"Welcome to JBoss")
```

#### 4. Search for Operations and Monitoring Systems

Useful for Kibana, Zabbix, Jenkins, GitLab, Harbor, Solr, phpMyAdmin, Hadoop, Flink, Spark, RabbitMQ, and similar infrastructure.

```text
1. (site:app.com.cn OR site:122.194.76.10) (intitle:"Kibana" OR inurl:kibana OR intitle:"Zabbix" OR inurl:jenkins OR intitle:"Dashboard [Jenkins]" OR intitle:"GitLab" OR intitle:"Gitea" OR inurl:harbor OR inurl:solr OR inurl:phpmyadmin OR intitle:"phpMyAdmin" OR inurl:adminer OR intitle:"Hadoop" OR intitle:"Apache Flink" OR intitle:"Spark" OR intitle:"RabbitMQ Management" OR inurl:flower)

2. (site:app.com.cn OR site:122.194.76.10) (inurl:"/phpinfo.php" OR intitle:"phpinfo()" OR inurl:"/server-status" OR inurl:"/server-info" OR intitle:"Apache Status")
```

#### 5. Search for Test Environments

```text
(site:app.com.cn OR site:yicongfound.org OR site:122.194.76.10) (inurl:test OR inurl:dev OR inurl:staging OR inurl:demo OR inurl:uat OR inurl:beta OR inurl:sandbox OR inurl:pre OR inurl:local)
```

#### 6. Search for Sensitive Files

```text
1. (site:app.com.cn OR site:122.194.76.10) (ext:pdf OR ext:doc OR ext:docx OR ext:xls OR ext:xlsx OR ext:ppt OR ext:pptx OR ext:csv OR ext:txt)
2. (site:app.com.cn OR site:122.194.76.10) ((ext:xls OR ext:xlsx OR ext:csv OR ext:txt) (intext:"密码" OR intext:"账号" OR intext:"通讯录" OR intext:"联系方式" OR intext:"机密" OR intext:"内部" OR intext:"confidential" OR intext:"credentials" OR intext:"token"))
3. (site:app.com.cn OR site:122.194.76.10) (ext:sql OR ext:tar.gz OR ext:zip OR ext:rar OR ext:bak OR ext:7z OR ext:dump OR ext:db OR ext:sqlite OR ext:gz OR ext:bz2 OR ext:tar)
4. (site:app.com.cn OR site:122.194.76.10) (ext:env OR ext:ini OR ext:conf OR ext:config OR ext:xml OR ext:properties OR ext:yml OR ext:yaml OR ext:json OR ext:cfg OR ext:toml)
5. (site:app.com.cn OR site:122.194.76.10) (ext:pem OR ext:crt OR ext:key OR ext:p12 OR ext:pub OR ext:ovpn OR ext:jks OR ext:keystore OR intext:"BEGIN RSA PRIVATE KEY" OR intext:"BEGIN OPENSSH PRIVATE KEY")
6. (site:app.com.cn OR site:122.194.76.10) (ext:log OR inurl:log OR inurl:logs OR (ext:txt (intext:"Exception" OR intext:"Error")))
7. (site:app.com.cn OR site:122.194.76.10) (inurl:.git OR inurl:.svn OR inurl:.DS_Store OR inurl:.hg OR inurl:.vscode OR inurl:.idea)
```

#### 7. Search for Directory Listings

```text
(site:app.com.cn OR site:122.194.76.10) (intitle:"index of" OR intitle:"Directory Listing For")
```

#### 8. Search for Error Pages

Useful for identifying database types and application stacks through exposed stack traces or fatal errors.

```text
(site:app.com.cn OR site:yicongfound.org OR site:122.194.76.10) (intext:"Fatal error:" OR intext:"Warning: mysql_connect()" OR intext:"stack trace:" OR intext:"Syntax error" OR intext:"java.lang.NullPointerException" OR intext:"SQL syntax" OR intext:"Exception in thread")
```

#### 9. Search for Third-Party Collaboration Platforms and Boards

Search target-specific high-entropy keywords on third-party platforms such as Trello, Atlassian, Notion, Shimo, Yuque, ProcessOn, and GitHub to find leaked development artifacts, project boards, or test accounts.

```text
(site:trello.com OR site:atlassian.net OR site:notion.so OR site:shimo.im OR site:yuque.com OR site:processon.com OR site:github.com) ("yicongfound" OR "金光纸业" OR "sinarmas")
```

### 4.2 Cloud-Drive Search

Developers, operators, or outsourcing personnel sometimes upload internal source code, topology diagrams, VPN credentials, or handover documents to public cloud drives or note-taking platforms. Searching those sources can reveal valuable information.

Useful sites:

```text
https://www.lingfengyun.com/
https://www.lzpanx.com/
https://www.dalipan.com/
```

Sample keyword directions:

1. Network topology and IT architecture
2. System handover and configuration documents
3. Source code and database backups
4. Internal admin files and employee directories

### 4.3 Email Collection

Email collection is often easiest through third-party interfaces. Most of them search by mail domain, so first identify the company's primary email domain during enterprise profiling.

Useful sites:

```text
https://app.snov.io/domain-search?name=app.com.cn&tab=emails
https://phonebook.cz/
http://www.skymem.info/
https://hunter.io/dashboard
https://www.email-format.com/i/search/
```

### 4.4 Obtaining Website Source Code

#### 1. Fingerprinting

Use tools such as `Tscan` and `Wappalyzer` to identify technologies and fingerprints.

```text
https://github.com/TideSec/TscanPlus/releases
https://chromewebstore.google.com/detail/wappalyzer-technology-pro/gppongmhjkpfnbhagpmjfkannfbllamg?hl=zh-CN&utm_source=ext_sidebar
```

#### 2. Open-Source Products

If the product is open source, search GitHub or Gitee and clone the code directly for auditing.

```text
https://github.com/
https://gitee.com/
```

#### 3. Commercial Products

Some commercial products can be found on resale or underground markets. This carries legal risk, so treat it carefully.

```text
https://www.huzhan.com/
https://28xin.com/
https://bbs.bcb5.com/
https://darkforums.su/
```

#### 4. Heavily Customized or Self-Developed Systems

Practical tips:

- check repository commit history after finding a hit
- inspect other repositories of the same developer
- pay attention to forks when the original repo is gone

Look for characteristics such as:

- footer copyright strings
- distinctive JavaScript or CSS paths
- unique API routes
- page titles and meta tags
- special login-page images or wording

Then search with queries like:

```text
"某某科技" OR "Powered by XXX"
"/api/v2/internal/dashboard"
"chunk-2d0a1f3" OR "/static/admin/js/"
"欢迎使用XX管理平台"
```

You can also combine directory scanning with source-code dump techniques to detect `.git`, `.svn`, `.hg`, `.bzr`, `WEB-INF`, backup archives, `.DS_Store`, `.swp`, and configuration files.

### 4.5 Deep-Web Data Leaks

#### 1. IntelX

IntelX aggregates many historical leaks from the deep web and underground forums. Searching company names, domains, or mail domains can reveal exposed credentials and data, especially from third-party suppliers.

```text
https://intelx.io/
```

Suggested keywords:

```text
app.com.cn
@app.com.cn
company-name
```

#### 2. Deep-Web and Underground Markets

Useful communities and marketplaces include:

```text
https://darkforums.su/
https://pwnforums.st/
https://www.demonforums.net/
xss.is
KillSec
darknetarmy
RansomHouse
```

### 4.6 Cloud Storage Bucket Enumeration

> [!IMPORTANT]
> ### BucketHunter is an interactive bucket-name enumeration tool for AWS S3, OSS, COS, and OBS, with AI-assisted result analysis.

Cloud storage buckets on public clouds such as Alibaba Cloud OSS, AWS S3, Tencent COS, and Huawei OBS are often exposed because of permission misconfiguration. Since bucket names are globally unique, enumerating them with company keywords and common suffixes is an effective way to discover forgotten or misconfigured cloud assets.

#### 1. Generate a Bucket Name Dictionary

Extract features from earlier subdomains and company keywords, then generate candidate bucket names. Run `BucketHunter` interactively and choose the first step:

```bash
./BucketHunter
```

Prepare inputs such as:

- company abbreviations
- product names
- project codenames
- previously collected subdomains

#### 2. Multi-Cloud Bucket Scanning

The tool calls `S3Scanner` internally to batch-scan generated names across multiple cloud platforms. Because fuzzy matches can introduce false positives, the results should be reviewed with context.

#### 3. AI-Assisted Analysis

After scanning, AI can be used to analyze and score the results, remove obviously irrelevant buckets, and help prioritize manual verification.

## Closing Note

This article is designed as a workflow handbook rather than a rigid checklist. In real projects, the best results usually come from combining a few high-signal methods from each stage, continuously cross-validating findings, and manually reviewing noisy data instead of blindly trusting any single platform or tool.
