# 高级编程小组作业#
组员：蔡旻 刘文韬 周胜鸿 

Written by Markdown
## 任务一：
 
## 任务二：二十四史
 
- 爬虫目标http://www.guoxuedashi.com/24shi/
- 根据链接分类存储
- 用到的包：os、requests、bs4

### 依赖包（requirement.txt）
- os包：os模块提供了非常丰富的方法用来处理文件和目录。在本程序中使用了os.mkdir("文件夹名称")语句在程序所在目录下创建目录（文件夹）以存放爬取的材料。
- requests包：关于http的库。

 requests.get()用于请求目标网站，类型是一个HTTPresponse类型。
- bs4包：解析html，获取标签;

### 爬虫设计方案
- 网页一共有三层，分别是“二十四史目录——各篇目录——正文”，我们设计三层爬取；
- 建立一个文件夹存储爬到的结果；
````
os.mkdir("24shi/")  # 创建24shi文件夹
````
- 爬虫基本逻辑是：

-- 发送访问请求

-- 获取内容（url）

-- 选择器，选择要获取的url的标签
	
- 首先分析一级页面的html编码
````
<dd class="dd2">
< a href=" " target="_blank">汉书</ a></dd>
````
由此我们设计第一层爬取
````
#第一层爬取
	url = "http://www.guoxuedashi.com/24shi/"
	raw_url = "http://www.guoxuedashi.com"
	respce = requests.get(url)   #发送访问请求
	soup = BeautifulSoup(respce.content, "html.parser") 
 #获取内容（url），其中r.content是获取对象编码
	result = soup.find_all("dd", class_="dd2")   
#选择器，选择要获取的url的标签；选择的是“dd”中具有“class为dd2的元素”
#也可以写作 result = soup.find_all('dd',attrs={'class':'dd2'})
```` 


- 现在爬虫进入了二级页面，分析二级页面代码
````
<dd>
< a href="/a/2p/111012g.html" target="_blank">卷六武帝纪第六</ a></dd>
````
由此我们设计第二层爬取
````
for elem in result:
'''
对该页面下的各链接进行访问
'''
new_url = raw_url + elem.a['href']  
# 把<a href……>里面的url拼起来
new_respce = requests.get(new_url)
new_soup = BeautifulSoup(new_respce.content, "html.parser")
new_result = new_soup.find_all("dd")
````
- 现在进入三级页面，爬取正文，分析页面代码
````
<div id="ArtContent">
<h1>卷一上高帝纪第一上</h1>
#省略部分代码
</div>
<div class="info_txt clearfix" id="infozj_txt">
#此处省略正文
</div>
````
由此设计第三层爬取
````
for new_elem in new_result:
'''
爬取各html链接
'''
final_html = raw_url + new_elem.a['href']
final_respce = requests.get(final_html)
final_soup = BeautifulSoup(final_respce.content, "html.parser")
final_header = final_soup.find("div", id="ArtContent").h1.get_text()  
#三级页面的标题
final_text = final_soup.find("div", id="infozj_txt").get_text()  
#三级页面正文内容
#以下是写文件操作：
     with open('24shi/'+final_header+'.txt', "a") as f:
				f.write(final_header+'\n')
				f.write(final_text)
````
