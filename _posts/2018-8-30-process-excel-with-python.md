---
layout: post
title: '使用xlrd和xlwt处理excel文件'
subtitle: ''
date: 2018-08-30
categories: Python
cover: ''
tags: Python
---
# 介绍
最近要处理一些excel文件,不想手动处理,想通过脚本处理,本来想使用pandas这个模块处理,但是网络环境很差,没有办法安装,pandas内部是使用xlrd和xlwt处理的,所以不如直接使用这两个模块来处理excel文件,考虑到以后还有可能使用python来处理excel文件所以记录下来.

# 打开excel文件
通过调用xlrd模块的open_workbook()打开,这个方法的参数是路径,使用Windows操作系统要注意,Windows系统使用反斜杠来标识路径,但反斜杠在python中是转义符,所以在有反斜杠的地方多加一个反斜杠.调用示例:
```python
table = xlrd.open_workbook("D:\\Document\\Excel\\test.xls")
```

# 打开工作表
一个excel文件往往有多个工作表,可以通过调用xlrd模块的一下方法获得工作表
```python
sheet = table.sheets()  # 这个是获得工作表的列表
sheet = table.sheet_by_index(0) # 这个是根据索引获得一个工作表
sheet = table.sheet_by_name("Sheet2")  # 这个是根据工作表的名字获得一个工作表
```

# 处理行和列
excel文件的基本单位是单元格(cell),通过行和列来组织
## 获取行数和列数
工作表对象(sheet)拥有两个属性nrows和ncols可以分别得到工作表的行数和列数,如:
```python
row = sheet.nrows
column = sheet.mcols
```

## 获取一行和一列
工作表对象(sheet)拥有两个方法可以获得一行或者一列数据,返回的类型都是list
```python
row_values = sheet.row_values(0)
column_values = sheet.col_values(0)
```

## 获取单元格
可以从工作表对象的一个方法获得一个单元格对象,单元格对象有一个属性可以获得单元格的值
```python
cell = sheet.cell(5,6)
value = cell.value
```
也可以通过sheet对象的一个方法直接获取一个单元格的值
```python
value = sheet.cell_value(5,5)
```

# 使用xlwt向文件写入数据
## 创建一个excel文件
使用xlwt这个对象的一个方法可以创建一个excel文件,**注意创建方法的首字母是大写的**
```python
work_book = xlwt.Workbook("D:\\Document\\Excel\\mytest.xls")
```

## 创建一个工作表
通过workbook对象(excel工作簿)可以创建一个工作表,并返回这个工作表
```python
sheet = work_book.add_sheet("test sheet")
```

## 设置格式
通过xlwt对象可以设置格式,并返回这个格式
```python
myStyle = xlwt.easyxf('font: name Times New Roman, color-index red, bold on', num_format_str='#,##0.00')
```
我一般对格式的要求不大

## 写入数据
工作表对象(sheet)有多个重载方法,基本的参数有三个,单元格的位置(i,j),要写入的数据,格式,其中的数据可以通过xlwt.Formula()填入公式,我对这个的需求也不大.
```python
sheet.write(0,0,"tinuv")
```

## 保存文件
调用workbook的sava()方法就可以保存了
```python
work_book.save("mytest.xls")
```


