# 将外部文件添加到 JIRA

> 原文：<https://dev.to/mfurmaniuk/adding-an-external-file-to-jira-dmh>

这是我几年前研究的东西。作为我公司个人项目时间的一部分，他们称之为“脱离网格”的一年一度的事件，我是团队项目的一部分，目标是将外部文件添加到 JIRA 票证。

JIRA 被用来跟踪公司的一切。这是我第一次用它，从那以后我在其他一些地方用过它。

这是我一手打造的 python 脚本。

> # [T1】！/usr/bin/env python](#usrbinenv-python)
> 
> 从 stat 导入 S_ISREG，ST_CTIME， ST_MODE
> 
> import os，sys，re，requests
> 
> from requests . auth import HTTPBasicAuth
> 
> import URL lib 3
> 
> URL lib 3 . disable _ warnings()
> 
> def openFile(location，key，filetype，fileKey):
> 
> " " "
> 
> 测试文件位置是否存在并在每个文件中搜索特定关键字
> 
> :param location:
> 
> :参数
> 
> 除 OSError:
> 
> pass
> 
> f = open(output file，' w')
> 
> print > > f，" Looking in % s " % location
> 
> for dirpath，dirnames，files in OS . walk(location):
> 
> 如果不是 files:
> 
> print dirpath，' is empty '
> 
> exit()
> 
> entries f = . 条目中的路径 if S _ is reg(stat[ST _ MODE])
> 
> # for cdate，路径 in sorted(entries):
> 
> # print time . ctime(cdate)，OS . path . basename(path)
> 
> check _ file = re . compile(file key)
> 
> for file in entries f:
> 
> if OS . path . is file(file):# and check _ file . search(file):. "没有与关键字%s" % fileKey
> 
> 匹配的条目 else:
> 
> print>>f，"没有与关键字% s " % fileKey
> 
> f . close()
> 
> 匹配的文件返回 outputFile
> 
> def addSqlTraceToJira(location，ticket):
> 
> """
> 
> 给吉拉票证添加一个 SQL 跟踪文件
> 
> :param location:
> 
> :param Ticket:
> 
> :return:
> 
> " "
> 
> entries f =(OS . path . join(location fn)for fn in OS . listdir(location))
> 
> check _ file = re . compile(' TRC $ ')
> 
> check _ ticket = re . compile(ticket)
> 
> for file in entries f:
> 
> if OS . path . is file(file)and check _ file . search(file)and check _ ticket . search(file):
> 
> attachFileToJira(file，ticket)
> 
> else:
> 
> print " No files found with % s " % ticket
> 
> def attachFileToJira(log file，ticket):
> 
> " " "
> "
> 将输出文件附加到吉拉票证
> 
> :param log file:
> 
> :param ticket:
> 
> :return:
> 
> " "
> 
> URL = "[https://t 密码))
> 
> # print "发送%s 到票据%s" %(日志文件，URL)
> 
> r = requests . post(URL，headers=headers，files=files，auth=auth，verify=False)
> 
> print '吉拉文件附加发送给我的%s，带有\n %s' % (r.status_code，r.text)](https://jira-test.fakedomain.comnet/rest/api/2/issue/%s/attachments)
> 
> def addCommentToJira(keyword，ticket):
> 
> """
> 
> 这附加了关于添加到票证
> 
> 的搜索参数的注释:param keyword:
> 
> :param ticket et:
> 
> :return:
> 
> " " "
> 
> URL = "[https://jira-test . fake domain . com net/rest/API/2/issue/% s/comment](https://jira-test.fakedomain.comnet/rest/api/2/issue/%s/comment)" % ticket% keyword
> 
> JSON = { " body ":comment }
> 
> r = requests . post(URL，headers=headers，json=json，auth=auth，verify=False)
> 
> print '吉拉添加评论向我发送了一个%s 与\n %s' % (r.status_code，r.text)
> 
> def main():
> 
> if len(sys . argv)<5:
> 
> 从 os.path 导入 basename
> 
> print('用法:%s location key 类型票“% basename(_ _ File _ _)
> 
> else:
> 
> # File LOCATION
> 
> LOCATION = sys . argv[1]
> 
> # search key
> 
> search key = sys . argv[2]
> 
> 【timeStart】
> 
> # timeStart = sys . argv[3]
> 
> # time end
> 
> # time end = sys . argv[4]
> 
> # log file = openFile(LOCATION，SEARCHKEY，FILETYPE，file key)
> 
> # attachFileToJira(log file，TICKET)
> 
> # addCommentToJira(search key，TICKET)
> 
> addsqltrace

我声称对此负有一些责任，主要是因为我在构建函数的时候，拼凑了很多 python，做了很多 TDD。