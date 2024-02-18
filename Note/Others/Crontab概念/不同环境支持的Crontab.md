
## Linux
Linux: `0 */12 * * * [user] [command]` 

```
 Linux
    *    *    *    *    *
    -    -    -    -    -
    |    |    |    |    |
    |    |    |    |    +----- day of week (0 - 7) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
    |    |    |    +---------- month (1 - 12) OR jan,feb,mar,apr ...
    |    |    +--------------- day of month (1 - 31)
    |    +-------------------- hour (0 - 23)
    +------------------------- minute (0 - 59)
```

Linux 的crontab不支持年和秒

|字段|是否必填|允许值|允许特殊字符|备注|
|---|---|---|---|---|
|Seconds|是|0–59|`*` `,` `-`|标准实现不支持此字段。|
|Minutes|是|0–59|`*` `,` `-`||
|Hours|是|0–23|`*` `,` `-`||
|Day of month|是|1–31|`*` `,` `-` `?` `L` `W`|`?` `L` `W`只有部分软件实现了|
|Month|是|1–12 or JAN–DEC|`*` `,` `-`||
|Day of week|是|0–7 or SUN–SAT|`*` `,` `-` `?` `L` `#`|`?` `L` `#`只有部分软件实现了  <br>Linux和Spring的允许值为0-7，0和7为周日  <br>Quartz的允许值为1-7，1为周日|
|Year|否|1970–2099|`*` `,` `-`|标准实现不支持此字段。|
