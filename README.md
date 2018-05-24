# load-big-data-pandas
load big data pandas by using pandas package pandas

This .py file aims to load big data by setting parameters of pd.read_csv() and there is some explanations of it. 

## 1. Data Type and Data Blocks:
At bottom, pandas will calssify data by **data type** into **data blocks**, here are the relationship between data type and data block. 
|Data Type | Data Blocks|
|----------|------------|
|string | Objectblock|
|float | floatblock| 
|int | intblock| 

Besides that every data type has different sub data type. Every the relationship betweeen data type and memory usage is listed as followings.

|Memory Usage | float | int | uint | datetime | bool | Object |
|-------------|-------|-----|------|----------|------|-------|
|1 bytes | | int8 (-128,127)| uint8(0,255)| | bool | |
|2 bytes | float16 | int16 (-32768,32767) | uint16 | | | | 
|4 bytes | float32 | int32 | uint32| | | |
|8 bytes | float64 | int64 | uint64| datetime64 | | |
|variable| | | | | | Object|

__The Object data type  will have larger memory usage__

For columns which dtype == Object, if unique value is smaller than 50%, we can use **.astype('category')** to transform string type into categorical type. However, we can't do this if unique value is bigger than 50%. 

## 2. Set Parameters for read_csv()
```python
scoredf = pd.read_csv(f,engine = 'c',skiprows =[1],usecols =score+lists+['flag_score'],dtype = dtype,memory_map = True)
```
* f:  f= open(filepath, encoding = 'utf-8')
* engine = 'c', use the c engine which is faster
* usecols = lists. if you have specified columns lists to load, you can just load these columns.
* dtype: dictionary {colname: dtype}. eg. {'col1':np.float16,'col2': 'category', 'col3': uint8}
* memory_map = True: map the file object directly onto memory and access the data directly from there.
* low_memory = False: if your dataset is really large.
 
 ## 3. Insert a chart into Excel directly
 This file provide a function to inserta chart into Excel directly.
 ```python
 def insert_plot(url,start_row,y_title=None,x_title=None):
	'''
	function: insert chart into every function
	input: 	url:  filepath
			start_row: int, the number of lines you need 
	output: excel file
	'''
  colors = ['FF6699','FF0033','FFFF33','FF6600','00CC00','330066','3399FF','663399','FF6699','9966FF','99CC00']  ## the rgb need to be standard color
  wb = load_workbook(url)   ## load workbook
  sheetnames = wb.get_sheet_names()
  for sheetname in sheetnames:
    ws = wb[sheetname]   ## load sheet 
    c1 = LineChart()    ## set the linechart 
    c1.title = sheetname
    c1.style = 13
    c1.y_axis.title = y_title
    c1.x_axis.title = x_title
    data = Reference(ws, min_col=2+start_row, min_row=1, max_col=1+start_row*2, max_row=15)  ### the data range to inset a chart 
    c1.add_data(data, titles_from_data=True) 
    titles = Reference(ws, min_col=1, min_row=2, max_row=15)  ## the range to insert a title/ 
    c1.set_categories(titles)
    for i in range(3):
      s1 = c1.series[i]
      s1.graphicalProperties.line.solidFill = colors[i]
    ws.add_chart(c1, "A20")
  wb.save(url)
  ```
