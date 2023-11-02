# 基于Selenium模拟浏览器行为的小红书关键词搜索和笔记爬取
  你好，我是Tangerineeew！非常感谢你能浏览我创建的第二个项目：“基于Selenium模拟浏览器行为的小红书关键词搜索和笔记爬取”...这是我在秋季校园招聘期间的笔试题目^^。
## 案例选择背景以及原因
  作为一名资深小红书用户，我注册加入小红书社区已经有1740余天了。在这期间，我养成了参与社区互动的习惯，发掘了诸多个人兴趣，例如美妆、穿搭、美甲、发型设计，同时收藏了大量关于学习、考试、就业、职场和商品的干货笔记。小红书不仅仅是一个社交媒体平台，更是一个充满活力和创造力的生活方式社区。
### “小红书”是如何玩转社交电商的？
  小红书是在全球拥有亿级用户的社交电商平台，它的迅速崛起和巨大成功，源于精准的目标用户定位，由用户驱动的内容生产和机制，以及对用户体验的持续优化。而这一切都离不开其背后的专业策划团队对于社区规范、激励计划的完善，和借助数据挖掘、数据分析、数学建模等技术提供的精细化运营管理策略支持。
### 为什么选择小红书作为数据源？
2016年初，小红书将人工运营内容修改为机器分发的形式，通过大数据和人工职能，将社区中的内容精准匹配给对它感兴趣的用户，从而提高用户体验。<br>
小红书上的内容涵盖了诸如美妆、个护、运动、旅游、酒店、餐饮等消费经验和生活方式的各个领域，这使得它成为一个极其丰富的数据源，能够提供各种主题和类别的数据。其次，小红书被称为“三次元社区”，这是因为其内容主要由用户生成，这意味着它包含了像你、我、他这样的真实用户的观点、评论和评价，这些内容对于市场调研、品牌检测和消费者洞察而言极具价值，不仅有助于理解用户行为，还具有相当的挖掘潜力，用于预测趋势、推荐系统以及更多应用领域，这使得小红书成为了品牌方看中的“智库”。<br>
出于个人亲身体验和对社区活力的深刻认知，在该案例中，我将选择小红书作为数据源，深入挖掘小红书社区数据来提供有关用户行为和市场趋势的信息，以作为我投参与Dashmote爬虫工程师岗位的笔试主题，同时支持有关市场分析和竞争研究。伴随着人类生活数字化，我相信小红书社区无论在“消费升级”还是“消费降级”的大潮中更将发挥更大的社会价值。<br>

## 关于小红书数据源的介绍
  对于小红书，我们希望最终得到以下数据:<br>
●	Id,	author_collect_nr,	author_fans_nr,	author_name,	author_note_nr, comment_nr, content, datePublished, images, like_nr, star_nr, url, user_url<br>
  分别对应id、作者获赞与收藏数量、粉丝数量、名字、笔记数量，评论数量，内容，发布日期，封面图片，获赞数量，收藏数量，笔记url，用户url，在完成爬取的时候我会进行完整的字段说明。
### 关于小红书的反爬虫技术和机制
小红书作为一个社交媒体平台，需要采用一系列反爬虫技术和机制来保护其数据和用户隐私，因此，网络爬虫可能会被视为非法。我们来检查小红书的用户隐私在线协议和robots.txt文件以确保使用怎样的爬虫复合物网站规则，了解哪些URL允许或者不允许被爬取。<br>
小红书用户隐私协议中明确指出：<br>
为了保护您的个人信息安全，我们将努力采取各种符合行业标准的安全措施来保护您的个人信息以最大程度降低您的个人信息被毁损、盗用、泄露、非授权访问、使用、披露和更改的风险。我们将积极建立数据分类分级制度、数据安全管理规范、数据安全开发规范来管理规范个人信息的存储和使用。<br>
https://www.xiaohongshu.com/robots.txt内容如下：<br>
<br>
User-agent:Googlebot<br>
Disallow:/<br>

User-agent:Baiduspider<br>
Disallow:/<br>

User-agent:bingbot<br>
Disallow:/<br>

User-agent:Sogou web spider<br>
Disallow:/<br>

User-agent:Sogou wap spider<br>
Disallow:/<br>

User-agent:YisouSpider<br>
Disallow:/<br>

User-agent:BaiduSpider-ads<br>
Disallow:/<br>

User-agent:*<br>
Disallow:/<br>
<br>
根据以上内容，小红书不允许包括Googlebot，Baiduspider，bingbot，Sogou web spider，YisouSpider，BaiduSpider-ads等任何搜索引擎爬虫爬取其内容，且对于所有用户代理User-agent都设置了’Disallow:/’，即不允许爬取网站内任何页面。<br>
经过搜索，近期除却借助第三方数据平台和爬取脚本的方法，还有一种通过手机端小红书App小程序接口获得前端请求链接实现Web抓取的傻瓜方法，但是似乎已经有人反馈无法监控数据流了。同时我还尝试了其他的爬虫框架，例如XPath+requests，但是我的有关经验仍然无法支持我克服小红书的反爬虫机制。<br>
至此，我决定使用Selenium模拟浏览器行为访问小红书，使用XPath和Selector选择器对象解析页面内容进行爬取。且由于该案例中仅需要爬取100条笔记的有关字段，无需使用并行处理来爬取数据，对于速度没有较高要求，该爬取方法具备一定稳定性，可以满足该案例需要。<br>
### 数据源：小红书官网
我们在小红书小程序页面上方的搜索框中键入关键词“上海酒吧”，并将排序方式修改为“最热”，得到如图1所示的页面，可以查看当前笔记的标题、作者名字和获赞数量，同时使用检查工具可获取笔记url和用户url；选择第一条笔记 “上海真没啥好玩的😅”，得到如图2所示的页面，在该页面中可以补充笔记内容、图片、收藏数量、发布时间和评论数量数据；我们还需要了解作者获赞与收藏数量、粉丝数量、笔记数量，点击作者头像进入作者的个人页面，如图1所示。<br>

![1](https://github.com/Tangerineeew/Selenium-basedXiaohongshuCrawler/assets/117080849/8422ad0b-df55-49c8-88e6-477139d3a882)
![2](https://github.com/Tangerineeew/Selenium-basedXiaohongshuCrawler/assets/117080849/bcd3a5cf-8408-49d2-9386-05efc1758d0a)
![3](https://github.com/Tangerineeew/Selenium-basedXiaohongshuCrawler/assets/117080849/2428ff75-5064-43f7-9e2f-96ff324f5cdc)

因此，我的爬取大致思路如下：首先在搜索页面中爬取热度前100条笔记的author_name, like_nr, url, user_url字段，然后分别使用100条url爬取对应笔记页面的comment_nr, content, datePublished, images, star_nr字段，最后分别使用100条user_url爬取对应作者个人页面的author_collect_nr, author_fans_nr, author_note_nr字段。

## 工作环境概览
操作系统：Windows 11<br>
Python环境：Python 3.11(64-bit)<br>
工作环境： Visual Studio 2022，Visual Studio Code<br>
###   Visual Studio和Visual Studio Code
Visual Studio 2022和Visual Studio Code都是由微软开发的集成开发环境(IDE)。<br>
Microsoft Visual Studio2022适用于多种编程语言和平台，提供丰富的开发工具和功能。另外，Visual Studio 2022拥有强大的Python环境和包(PyPI)的管理和更新功能，故我使用它来配置Python环境。<br>
Visual Studio Code相较于Visual Studio 2022是一款更加轻量级的文本编辑器，拥有丰富的扩展，主要适用于快速开发小型项目或进行脚本编写，故我主要使用Visual Studio Code来编辑代码。另外，Visual Studio Code在安装了Jupyter扩展后，可以使用的Jupyter Python3(ipykernel)内核URL直接运行.ipynb文件。<br>
当在Visual Studio 2022中配置好Python环境，并且在Visual Studio Code中安装了Python扩展后，它会读取系统的PATH环境变量，从中找到Python解释器的路径，使用该解释器来执行Python代码。

## 小红书上海酒吧最热笔记Top100
### 准备工作
正如我在介绍关于小红书的反爬虫技术和机制时所说，我们使用Selenium模拟浏览器行为访问小红书：<br>
1)	配置Chrome浏览器选项：启用远程调控模式和无痕模式（即隐身模式），创建Chrome实例，然后打开cmd命令提示符启动Chrome实例；<br>
2)	检查小红书登录状态：通过检查page_source中是否包含“登录探索更多内容”来检查小红书登录状态，如果未登录，那么提示用户手动登录，如果已登录则返回登录成功，同时打印每次检查的状态和时间作为日志，以提供实时监控和反馈；<br>
3)	执行关键词搜索：根据用户设置的搜索关键词执行检索，并提示用户注意手动完成人机验证；<br>
4)	检查网页加载状态：通过检查title中是否包含用户设置的关键词key_word来检查页面加载状态；<br>
5)	更改模式和排序方式：由于小红书笔记包含视频笔记和图文笔记，我们根据案例字段要求，通过模拟鼠标点击、悬停选择图文模式和排序方式（综合，最新，最热），用户可以指定使用哪种排序方式。
### 爬取数据
根据关于小红书数据源的介绍，所有字段的爬取流程可以分为三大部分：<br>
1)	搜索页面：搜索页面可以直接观察到作者名字，笔记获赞数两个字段，使用DevTools开发者工具检查页面可以获取笔记url，作者url两个字段，即author_name, like_nr, url, user_url4个属性；<br>
2)	笔记页面：笔记页面可以直接观察到评论数量、内容、发布日期、收藏数量4个字段，使用DevTools开发者工具检查页面可以获取封面图片并追加为图片链接，即comment_nr, contet, dataPublished, star_nr, images5个属性；<br>
3)	个人页面：个人页面可以直接观察到获赞与收藏数量、粉丝数量两个字段，通过累计特定类型的元素出现的次数可以获取笔记数量字段，即author_collect_nr, author_fans_nr, author_note_nr3个属性。<br>
### 数据清洗
经过爬取得到的结果为原始数据，可能存在异常值、缺失值，需要对原始数据进行数据清洗，包括异常值、缺失值处理，数据转换等，以确保经过处理的数据的一致性，使得适用于数据库存储，才能将所有存储字段的列表合并为一个字典，最后转换为DataFrame结构，通过Pymongo驱动程序导入MongoDB对应数据库下的集合中。<br>
另外，在模拟鼠标滚动来加载搜索页面的过程中我注意到，当我们快速滚动页面且没有阅读其中笔记时，可能会重复推送相同的笔记，这种现象在各个平台上都会出现，这将导致重复爬取相同的字段。由于时间关系，我暂时还未来得及对这一问题进行处理。<br>
最后，authorNoteNr_list列表的绝大多数author_note_nr有时为12，有时为24，我猜测是因为模拟鼠标滚动一次刷新并加载12条笔记， author_note_nr字段为12或24的作者真实的笔记数量应该都大于24条，而模拟时不知出于何种原因没有完全读取笔记信息元素下的特定类型元素，导致计数出现错误。该问题由于时间关系也暂时未得到解决。<br>
### 存储数据
经过清洗得到的结果为清洁数据，在检查清洗过后的所有用于存储字段的列表的长度均为用户设置的数量后，即可将其合并为一个字典，并转换为DataFrame结构，可以通过Pymongo驱动程序导入MongoDB对应数据库下的集合中，存储结果如图4所示。我已经将完整的结果分别导出为.json和.csv格式的文件，您可以方便地查看。<br>
![MongoDBDatabase Collection Preview](https://github.com/Tangerineeew/Selenium-basedXiaohongshuCrawler/assets/117080849/1288571c-f8b2-4edd-9dcd-99f0912aad5b)
