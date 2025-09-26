---
title: python-datetime
date: 2022-08-03 11:23:51
categories: python #文章分类
tags: [python] #文章标签，可以一次添加多个标签
---

...

<!-- more -->

## Python 中的 datetime 模块

### 基本操作

- date 操作日期对象
- time 操作时间对象
- datetime 是日期和时间的组合
- timedelta 允许我们使用时间区间
- tzinfo 允许我们使用时区

```py
# From the datetime module import date
from datetime import date
# Create a date object of 2000-02-03
date(2022, 2, 3)

# From the datetime module import time
from datetime import time
# Create a time object of 05:35:02
time(5, 35, 2)

# From the datetime module import datetime
from datetime import datetime
# Create a datetime object of 2000-02-03 05:35:02
datetime(2000, 2, 3, 5, 35, 2)

# Time at the moment
now = datetime.now()
print (now)

today = date.today()
print (today)

time(now.hour, now.minute, now.second)

now.isocalendar()

# Access week number
week_number = now.isocalendar()[1]
print (week_number)

# Convert a date string into a date object
date.fromisoformat("2022-12-31")
# Convert a datetime object into a string in the ISO format
date(2022, 12, 31).isoformat()

# Date as a string
iso_date = "2022-12-31 23:59:58"
# ISO format
iso_format = "%Y-%m-%d %H:%M:%S"
# Convert the string into a datetime object
datetime.strptime(iso_date, iso_format)

# Create a datetime object
datetime_obj = datetime(2022, 12, 31)
# American date format
american_format = "%m-%d-%Y"
# European format
european_format = "%d-%m-%Y"
# American date string
print(f"American date string: {datetime.strftime(datetime_obj, american_format)}.")
# European date string
print(f"European date string: {datetime.strftime(datetime_obj, european_format)}.")

new_year_2023 = datetime(2022, 12, 31)
datetime.timestamp(new_year_2023)
datetime.fromtimestamp(1672441200)
```

### 算术运算

- timedelta()时间增量
- relativedelta()相对增量

```py
from datetime import date
# Function to convert datetime to string
def dt_string(date, date_format="%m %d, %Y"):
    return date.strftime(date_format)

from datetime import datetime
# Import timedelta
from datetime import timedelta
# Current time
now = datetime.now()
# timedelta of 30 days
# 有以下参数：days=0, seconds=0, microseconds=0, milliseconds=0, minutes=0, hours= 0，周=0
one_month = timedelta(days=30)
# Day in one month/using dt_string function defined above
print(f"The day in 30 days is {dt_string(now + one_month)}.")

# New year 2030
new_year_2030 = datetime(2030, 1, 1, 0, 0)
# timedelta of 12 hours
twelve_hours = timedelta(hours=12)
# Time string of 12 hours before New Year 2023
twelve_hours_before = (new_year_2030 - twelve_hours).strftime("%B %d, %Y, %H:%M:%S")
# Print the time 12 hours before New Year 2023
print(f"The time twelve hours before New Year 2030 will be {twelve_hours_before}.")

# 使用 dateutil 包中的 relativedelta 函数实现加减年,月
# Import relativedelta
from dateutil.relativedelta import relativedelta
# Current time
now = datetime.now()
# relativedelta object
relative_delta = relativedelta(years=2, months=3, days=4, hours=5)
two_years = (now - relative_delta).strftime("%B %d, %Y, %H:%M:%S")
print(f"The time 2 years, 3 months, 4 days, and 5 hours ago was {two_years}.")

relativedelta(datetime(2030, 12, 31), now)

#时区
import zoneinfo
# Will return a long list of timezones (opens many files!)
zoneinfo.available_timezones()

# Function to convert datetime into ISO formatted time
def iso(time, time_format="%Y-%m-%d %H:%M:%S"):
    return time.strftime(time_format)
# CET time zone
cet_tz = ZoneInfo("Europe/Paris")
# PST time zone
pst_tz = ZoneInfo("America/Los_Angeles")
# Current time in Central Europe
dt_cet = datetime.now(tz=cet_tz)
# Current time in California
dt_pst = datetime.now(tz=pst_tz)
print(f"Current time in Central Europe is {iso(dt_cet)}.")
print(f"Current time in California is {iso(dt_pst)}.")
```
