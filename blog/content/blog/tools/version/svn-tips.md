Title: subversion tips
Date: 2015-06-19 14:30
Category: tools
Tags: 201506, svn
Author: laomie
Summary: subversion技巧

检出
===============
```
svn co http://路径(目录或文件的全路径) [本地目录全路径] --username 用户名
```

SVN回滚
=====================
```
# 正统方法一
svn merge --dry-run -r73:68 http://my.repository.com/my/project/trunk
svn merge -r73:68 http://my.repository.com/my/project/trunk
svn commit -m "Reverted to revision 68."

# 正统方法二
svn merge -c -{rev} http://svn.example.com/repos/calc/trunk
svn commit -m 'undo'

# 直观方法
svn cat -r {rev} filename > filename
```

导出
======================
```
svn export [-r 版本号] http://路径(目录或文件的全路径)[本地目录全路径] --username 用户名
```

添加新文件
====================
```
svn add 文件名
```

提交
=====================
```
svn commit -m “提交备注信息文本“ [-N] [--no-unlock] 文件名
```

更新文件
=====================
```
svn update
#将版本库中的文件 Test.java还原到修正版本
svn update -r 200 Test.java
```

删除文件
=====================
```
svn delete 文件名
```

加锁/解锁
=====================
```
svn lock -m “加锁备注信息文本“ [--force] 文件名 
svn unlock
```

比较差异
=====================
```
svn diff 文件名
# 修正版本号200和修正版本号201比较差异
svn diff -r 200:201 Test.java
```

查看文件或者目录状态
=====================
```
# ?：不在svn的控制中；M：内容被修改；C：发生冲突
svn status [-v] [目录/文件]
```

查看日志
=====================
```
svn log 文件名
```

查看文件详细信息
=====================
```
svn info 文件名
```

查看版本库下的文件和目录列表
=====================
```
svn list svn:// 路径(目录或文件的全路径)
```

创建纳入版本控制下的新目录
=====================
```
svn mkdir 目录名
```

创建纳入版本控制下的新目录
=====================
```
svn mkdir 目录名
svn add --non-recursive 目录名
```

恢复本地修改
=====================
```
svn revert [--recursive] 文件名
```

解决冲突
=====================
```
svn resolved [本地目录全路径]
```

不checkout而查看输出特定文件或URL的内容
=====================
```
svn cat http://文件全路径
```

忽略文件
======================
```
svn propedit svn:ignore .
```

查看删除的文件
====================
```
svn log -r2080:HEAD -v | grep "^   D"
svn log -v . | awk '/^r[0-9]* \|/ { REVISION=$1; } /  D/ { print REVISION": "$0; }'
```

签出指定时间之前的版本
=========================
```
svn checkout --revision {2016-02-23} http://文件全路径
```
