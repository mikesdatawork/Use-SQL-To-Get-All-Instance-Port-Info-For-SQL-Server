> **Note** — The folder `linguist-samples/` contains tiny real files so GitHub can correctly display all languages used in this repo.  
> The actual content and examples remain in this README.

![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Use SQL To Get All Instance Port Info For SQL Server
**Post Date: November 4, 2016**        



## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process

<p>Save a bunch of clicks with this SQL logic. This will find all the ports for each SQL Server instance installed on your multi-instance SQL Server. This logic assumes the default port of 1433 is being used for the default SQL instance.</p>  


## SQL-Logic
```SQL
use master;
set nocount on
  
declare @sql_instances  table
(   
    [rootkey]   varchar(255)
,   [value]     varchar(255)
)
insert into @sql_instances 
exec master.dbo.xp_instance_regenumvalues 
    @rootkey    = N'HKEY_LOCAL_MACHINE'
,   @key        = N'SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL';
  
declare db_cursor   cursor for select upper([rootkey]), upper([value]) from @sql_instances
declare @instance_name  varchar(255)
declare @instance_path  varchar(255)
open    db_cursor;
fetch next from db_cursor into @instance_name, @instance_path
    while @@fetch_status = 0  
        begin
            declare @port_table table
            (
                [Instance]  varchar(255)
            ,   [Port]      int
            )
            declare @port   varchar(50)
            declare @key    varchar(255) = 'software\microsoft\microsoft sql server\' + @instance_path + '\mssqlserver\supersocketnetlib	cp\ipall'
            exec master..xp_regread
                @rootkey    = 'hkey_local_machine'
            ,   @key        = @key
            ,   @value_name = 'tcpdynamicports'
            ,   @value      = @port output
             
            insert into @port_table
            select
                'Instance'  = @instance_name
            ,   'Port'  = isnull(convert(varchar(10), @port), 1433)
            fetch next from db_cursor into @instance_name, @instance_path
        end;
    close db_cursor
deallocate db_cursor;
 
select * from @port_table
```

![Use SQL To Get SQL Port Info]( https://mikesdatawork.files.wordpress.com/2016/11/image0011.png "Get SQL Port Info For All Instances")
 



[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

  
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")
