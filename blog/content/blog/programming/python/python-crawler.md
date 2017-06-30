Title: python crawler 
Date: 2017-06-14 14:30
Category: programming
Tags: 201706, python, crawler
Author: laomie
Summary: python爬虫

依赖包安装
================
```bash
pip install selenium beautifulsoup4 requests lxml gevenp install selenium beautifulsoup4 requests lxml gevent
```
下载[phantomjs](http://phantomjs.org/download.html)并安装

下载[geckodriver](https://github.com/mozilla/geckodriver/releases)并安装

下载[chromedriver](http://chromedriver.storage.googleapis.com/index.html)并安装

phantomjs使用
=========================
```python
proxy = '--proxy=' + ip + ':' + port
dcap = dict(DesiredCapabilities.PHANTOMJS)
dcap["phantomjs.page.settings.userAgent"] = ''Mozilla/5.0 (Windows NT 10.0; WOW64; rv:45.0) Gecko/20100101 Firefox/45.0'
#dcap["phantomjs.page.settings.resourceTimeout"] = ("2000")
service_args = [proxy]
browser = webdriver.PhantomJS(executable_path=r'/data/apache/tools/phantomjs/bin/phantomjs', service_args=service_args, desired_capabilities=dcap)
browser.set_page_load_timeout(60)
browser.get('http://icanhazip.com/')
```

firefox使用
=====================
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions

fp = webdriver.FirefoxProfile()
# Direct = 0, Manual = 1, PAC = 2, AUTODETECT = 4, SYSTEM = 5
fp.set_preference("network.proxy.type", 1)
fp.set_preference("network.proxy.http", ip)
fp.set_preference("network.proxy.http_port", int(port))
fp.set_preference("general.useragent.override", "whater_useragent")
fp.update_preferences()
browser = webdriver.Firefox(executable_path=r'/home/laomie/tools/geckodriver', firefox_profile=fp)
browser.set_page_load_timeout(20)
browser.get('http://icanhazip.com/')

# 如需等待页面载入相应的元素，可用以下类似代码
WebDriverWait(browser, 20).until(
    expected_conditions.text_to_be_present_in_element(
        (By.TAG_NAME, 'h1'),
        '查询结果'
    )
)
```

chrome使用
====================


参考
==================
* <http://cuiqingcai.com/2599.html>
* <http://www.jianshu.com/p/2631bf34328e>
