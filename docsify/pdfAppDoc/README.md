# PDF 转 Excel

## 接口描述
本接口支持将PDF转为Excel表格。

## 请求说明

### 请求URI
POST 

`http://localhost/api/pdf_to_excel`

### 请求Header参数

|参数|类型|是否必须|描述|示例值|
| ----------- | ----------- | ----------- | ----------- | ----------- |
| Content-Type | string  | 是 | 请求体的编码格式 | application/form-data |

### 请求Body参数

Form字段数据结构说明

|参数|类型|是否必须|描述|
| ----------- | ----------- | ----------- | ----------- |
| pdfFile | file | 是 | 目标pdf文件。 |
| pages | string | 否 | 指定转换页面，多个页码用英文逗号分隔（如：1,2-5,8）; 不传值或空字符，则转换全部  |
| returnType | string | 否 | 返回类型(text,stream)，默认为stream, 当设置为text时，返回值为json格式，详细查看返回说明。|

## 返回说明

### 返回参数

|字段|类型|描述|
| ----------- | ----------- | ----------- |
| status | string | success 成功; error 失败 |
| message | string | api执行消息，一般为api执行失败时的提示信息。 |
| --fileName | string | 导出生成的结果文件名称，可以在下载接口中使用，详细查看接口： |
| --url | string | 导出生成的结果文件的下载地址 |

### 返回示例
当入参returnType=text时，返回值的结果
```json
{
    "status": "success",
    "message":{
        "fileName": "12345678.xlsx",
        "url": "http://localhost/api/pdf_to_excel?fileName=12345678.xlsx"
    }
}
```


# 文件下载

## 接口说明
本接口支持将PDF转为Excel表格的文件下载到本地。

## 请求说明


### 请求URI
GET 

`http://localhost/api/pdf_to_excel?fileName=xxxxx.xlsx`

### 请求query参数

|参数|类型|是否必须|描述|
| ----------- | ----------- | ----------- | ----------- |
| fileName | string | 是 | 要下载的文件名称。一般都是导出接口返回的fileName |

### 返回示例

当下载失败时
```json
{
    "status": "error",
    "message":"file not found: 12345678.xlsx"
}
```