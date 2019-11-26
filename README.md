![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 敏感公共角色权限
#### Sensible SQL Public Role Permissions
**发布-日期: 2018年01月05日 (评论)**


## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
创建临时表以保存公共角色权限。
这里有一些快速的sql逻辑，可以帮助你更好地理解公共角色权限。在尝试找出可能需要编写的撤消操作时，这可能会有所帮助。为了更好地保护你的数据库环境，需要使用一个小的临时表（填充公共角色权限）。我能够编写一个更简化的查询来查找我正在寻找的权限。

获取一组基本的公共角色权限。

使用SMTP获取快速报告并添加到你查询到的电子邮件（如果这是你已经设置好的内容）。

最后，用几个简短的查询来获取公共角色，扩展存储过程或系统对象。


## English
Creating a temp table to hold the Public Role permissions…
Here’s some quick sql logic to help make more sense of the public role permissions. This might be helpful when trying to figure out what revokes you might need to write in order to better secure your database environment. Using a small temporary table (populated with public role permissions), I’m able to write a much more simplified query to find the permissions I’m looking for.

Get a basic set of Public Role permissions.

Get a quick report to add to your query-to-email using SMTP (if this is something you already have setup).

Finally; a couple short queries to get the dirt on the Public Role, and the extended stored procedures or some the system objects.

![#](images/Sensible-SQL-Public-Role-Permissions.png?raw=true "#")


---
## Logic
```SQL
use master;
set nocount on
 
-- build temp table to hold public role permissions
if object_id('tempdb..#role_permissions') is not null
drop table      [#role_permissions]
create table    [#role_permissions]
(
    [role]      varchar(255)
,   [permissions]   varchar(255)
,   [schema]    varchar(255)
,   [object]    varchar(255)
)
insert into [#role_permissions]
select
    'role'      = upper(sdprin.name)
,   'permission'    = sdperm.permission_name
,   'schema'    = object_schema_name(so.object_id)
,   'object'    = so.name
from
    sys.all_objects so
    inner join sys.database_permissions sdperm on so.object_id          = sdperm.major_id
    inner join sys.database_principals  sdprin on sdprin.principal_id   = sdperm.grantee_principal_id
 
select 'number of role permissions' = count(*) from [#role_permissions]
select  top 20 * from [#role_permissions]
 
 
-- get existing permissions for PUBLIC role.
select
    [role]
,   [permissions]
,   'object' = [schema] + '.' + [object]
from
     [#role_permissions]
where
    [role] = 'public'
order by
    [object] asc
 
-- looking at it a more sensible way when reading it you can do this...
select
    'Quick Permissions Report'      = ' The role  ' + [role] + '  has  ' + [permissions] + '  permissions on object:  [' + [schema] + '].[' + [object] + ']  under the  [' + upper(db_name()) + ']  database.'
from
    [#role_permissions]
where
    [role] = 'public'
order by
    [object] asc
 
-- couple short queries to get public role permissions to xp's and some system objects
select [role], [permissions], 'object' = [schema] + '.' + [object] from [#role_permissions] where [object] like 'xp%'
select [role], [permissions], 'object' = [schema] + '.' + [object] from [#role_permissions] where [object] like 'sys%'

```



[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

