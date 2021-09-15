# Data-analysis
import numpy as np
import pandas as pd
import time
import datetime
road = 'c:/project/kdxf/train.csv'
df = pd.read_csv(road)
# 将年龄代号替换为年龄区间
df['age'].replace([1,2,3,4,5,6],['18岁以下','18~22','23~30','31~40','41~55','55岁以上'],inplace=True)
# 将性别代号替换为性别区间
df['gender'].replace([0,1],['男','女'],inplace=True)
# 将时间戳转换为时间格式
df['time'] = df['time'].apply(lambda x:eval(x))
df['time_mean'] = df['time'].apply(np.mean)
def timestamp_transform(x):
    x=(x+28800000)/1000
    time_local = time.localtime(x)
    dt = pd.to_datetime(time.strftime("%Y-%m-%d %H:%M:%S", time_local))
    return dt
df['time_mean'] = df['time_mean'].apply(timestamp_transform)
df['year'] = df['time_mean'].dt.year
df['month'] = df['time_mean'].dt.month
df['days'] = df['time_mean'].dt.day
df['hour'] = df['time_mean'].dt.hour
def weekday_transform(x):
    weekday = x.weekday()
    return weekday
df['weekday'] = df['time_mean'].apply(weekday_transform)
dict = {
    0:'周一',
    1:'周二',
    2:'周三',
    3:'周四',
    4:'周五',
    5:'周六',
    6:'周日'
}
df['weekday'].replace(dict,inplace=True)
df_bought = df[df['label'] == 1]
def groupby_count(x,y):
    data = x.groupby(y).size()
    DataFrame = pd.DataFrame(data)
    return DataFrame
for i in ('province','make','gender','age','year','month','days','hour','weekday'):
    locals()['count_'+str(i)] = groupby_count(df,i)
    locals()['count_'+str(i)+'_bought'] = groupby_count(df_bought,i)
count_year.drop(labels=[1970, 1989, 1995, 2006, 2010, 2013, 2014, 2015, 2016, 2017, 2018, 2024], inplace=True)
count_year_bought.drop(labels=[2014, 2016, 2017, 2018, 2024], inplace=True)
writer1 = pd.ExcelWriter('c:/project/kdxf/project.xlsx')
for i in ['count_province','count_make','count_age','count_gender','count_year','count_month','count_days','count_hour','count_weekday']:
    eval(i).to_excel(excel_writer=writer1,sheet_name=i)
writer2 = pd.ExcelWriter('c:/project/kdxf/project_bought.xlsx')
for i in ['count_province_bought','count_make_bought','count_age_bought','count_gender_bought','count_year_bought','count_month_bought','count_days_bought','count_hour_bought','count_weekday_bought']:
    eval(i).to_excel(excel_writer=writer2,sheet_name=i)
writer1.save()
writer1.close()
writer2.save()
writer2.close()
