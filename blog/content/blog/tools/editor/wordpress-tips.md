Title: wordpress tips
Date: 2017-07-12 14:30
Category: tools
Tags: 201707,wordpress 
Author: laomie
Summary: wordpress技巧

google字体加载慢
===============
查找出google字体加载代码，修改加载链接
```
grep -rnw 'wp_html' -e 'googleapis'

# 替换找到的链接
https://fonts.googleapis.com -> https://fonts.cat.net
https://ajax.googleapis.com -> https://ajax.cat.net
```

twenty seventeen theme
===========================
参考[Changing default content width in Twenty Seventeen](https://wordpress.org/support/topic/changing-default-content-width-in-twenty-seventeen/)，用以下css修改theme的宽
```
.wrap {
max-width: 80%;
}

@media screen and (min-width: 48em) {
.wrap {
max-width: 80%;
}
}

.page.page-one-column:not(.twentyseventeen-front-page) #primary {
max-width: 80%;
}

@media screen and (min-width: 30em) {
.page-one-column .panel-content .wrap
{
max-width: 80%;
}
}

.has-sidebar #secondary {
width: 26% !important;
}

.site-info { display: none; }
```

wordpress插件
=========================
1\. codecolorer 
