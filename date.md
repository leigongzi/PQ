```
date=(optional 请输入开始年份 as number,optional 请输入结束年份 as number)=>

let
    x = 请输入开始年份,
    y = if 请输入结束年份 = null then 请输入开始年份 else 请输入结束年份,
    begin_date = if x = null then #date(Date.Year(DateTime.LocalNow()),1,1) else #date(x,1,1),
    end_date = if y = null then #date(Date.Year(DateTime.LocalNow()),12,31) else #date(y,12,31),
    list = {1..Number.From(end_date)-Number.From(begin_date)+1},
    dates = List.Transform( list , (item)=> Date.AddDays(begin_date,item-1) ),
    table = Table.TransformColumnTypes(Table.RenameColumns(Table.FromList(dates, Splitter.SplitByNothing(), null, null, ExtraValues.Error),{{"Column1", "日期"}}),{{"日期", type date}}),
    date_id = Table.TransformColumnTypes(Table.AddColumn(table, "日期序号", each Date.Year([日期])*10000+Date.Month([日期])*100+Date.Day([日期])),{{"日期序号", type number}}),
    year_id = Table.AddColumn(date_id, "年序号", each Date.Year([日期]), type number),
    year_name = Table.AddColumn(year_id, "年份名称", each "Y"&Text.From([年序号])),
    quarter_id = Table.AddColumn(year_name, "季度序号", each Date.QuarterOfYear([日期]), type number),
    quarter_name = Table.AddColumn(quarter_id, "季度名称", each "Q"&Text.From([季度序号])),
    month_id = Table.AddColumn(quarter_name, "月份序号", each Date.Month([日期]), type number),
    month_name = Table.AddColumn(month_id, "月份名称", each "M"&Text.From([月份序号])),
    week_id = Table.AddColumn(month_name, "周序号", each Date.WeekOfYear([日期]), type number),
    week_name = Table.AddColumn(week_id, "周名称", each "W"&Text.From([周序号])),
    year_quarter_id = Table.AddColumn(week_name, "年季序号", each Date.Year([日期])*10+Date.QuarterOfYear([日期]), type number),
    year_quarter_name = Table.AddColumn(year_quarter_id, "年季名称", each "YQ"&Text.From([年季序号])),
    year_month_id = Table.AddColumn(year_quarter_name, "年月序号", each Date.Year([日期])*100+ Date.Month([日期]), type number),
    year_month_name = Table.AddColumn(year_month_id, "年月名称", each "YM"&Text.From([年月序号])),
    year_week_id = Table.AddColumn(year_month_name, "年周序号", each Date.Year([日期])*100+ Date.WeekOfYear([日期]), type number),
    #"year_week-name" = Table.AddColumn(year_week_id, "年周名称", each "YW"&Text.From([年周序号])),
    day_in_week_id = Table.AddColumn(#"year_week-name", "日序号", each Date.DayOfWeek([日期],0), type number),
    day_in_week_name = Table.AddColumn(day_in_week_id, "周天名称", each if [日序号] = 1 then "WD1" else
if [日序号] = 2 then "WD2" else
if [日序号] = 3 then "WD3" else
if [日序号] = 4 then "WD4" else
if [日序号] = 5 then "WD5" else
if [日序号] = 6 then "WD6" else
"WD7"),
    work_day = Table.AddColumn(day_in_week_name , "工作日", each if [日序号] = 6 or [日序号] = 0 then "休息日" else "工作日" )
in
    work_day
```
