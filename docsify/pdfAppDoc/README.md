# PyInstaller 打包指南

本指南介绍如何使用 PyInstaller 打包 Python 应用程序，包括无控制台和有控制台两种模式，以及处理 Windows 依赖项的步骤。

## 1. 无控制台加载脚本

要生成没有控制台窗口的可执行文件，使用以下命令：
 pyinstaller.exe -D -w  .\app.py  --collect-all rapidocr_onnxruntime --collect-all rapid_table --collect-all wired_table_rec --collect-all lineless_table_rec --collect-all table_cls --collect-all pyzbar --collect-all pypdfium2 --collect-all pypdfium2_raw  --add-data "config.py;." --add-data "services/;services/" --add-data "core/;core/"

   <!-- pyinstaller.exe -D -w  .\app.py  --collect-all paddleocr --collect-all pyclipper --collect-all rapidocr_onnxruntime --collect-all imghdr --collect-all rapid_table --collect-all wired_table_rec --collect-all lineless_table_rec --collect-all table_cls --collect-all skimage --collect-all imgaug --collect-all scipy.io --collect-all lmdb  --add-data "config.py;." --add-data "services/;services/" --add-data "core/;core/" -->

## 2. 控制台加载脚本

要生成控制台窗口的可执行文件，使用以下命令：
   pyinstaller.exe -D -c  .\app.py  --collect-all rapidocr_onnxruntime --collect-all rapid_table --collect-all wired_table_rec --collect-all lineless_table_rec --collect-all table_cls  --collect-all pyzbar --collect-all pypdfium2 --collect-all pypdfium2_raw --add-data "config.py;." --add-data "services/;services/" --add-data "core/;core/"


# 对外接口说明

## PDF 转 Excel

### 接口描述
本接口支持将PDF转为Excel表格。

### 请求说明

#### 请求URI
POST 

`http://localhost:8087/api/pdf_to_excel`

#### 请求Header参数

|参数|类型|是否必须|描述|示例值|
| ----------- | ----------- | ----------- | ----------- | ----------- |
| Content-Type | string  | 是 | 请求体的编码格式 | application/form-data |

#### 请求Body参数

Form字段数据结构说明

|参数|类型|是否必须|描述|
| ----------- | ----------- | ----------- | ----------- |
| pdfFile | file | 是 | 目标pdf文件。 |
| pages | string | 否 | 指定转换页面，多个页码用英文逗号分隔（如：1,2-5,8）; 不传值或空字符，则转换全部  |
| returnType | string | 否 | 返回类型(text,stream)，默认为stream, 当设置为text时，返回值为json格式，详细查看返回说明。|

### 返回说明

#### 返回参数

|字段|类型|描述|
| ----------- | ----------- | ----------- |
| status | string | success 成功; error 失败 |
| message | string | api执行消息，一般为api执行失败时的提示信息。 |
| --fileName | string | 导出生成的结果文件名称，可以在下载接口中使用，详细查看接口： |
| --url | string | 导出生成的结果文件的下载地址 |
| --pageStatus | string | 每页是否存在表格 |

#### 返回示例
当入参returnType=text时，返回值的结果
```json
{
    "status": "success",
    "message": {
        "fileName": "合并PDF10.xlsx",
        "url": "http://10.1.31.210:8087/api/pdf_to_excel?fileName=合并PDF10.xlsx",
        "pageStatus": [
            {
                "page_num": 10,
                "has_table": true,
                "isOcr": false,
                "tabletitles": [
                    "共和枢纽立交 挖淤泥排水数量表"
                ]
            }
        ]
    }
}

```

当入参returnType=stream 或者没有传值时，返回值是附件流

## 文件下载

### 接口说明
本接口支持将PDF转为Excel表格的文件下载到本地。

### 请求说明


#### 请求URI
GET 

`http://localhost:8087/api/pdf_to_excel?fileName=xxxxx.xlsx`

#### 请求query参数

|参数|类型|是否必须|描述|
| ----------- | ----------- | ----------- | ----------- |
| fileName | string | 是 | 要下载的文件名称。一般都是导出接口返回的fileName |

#### 返回示例

当下载失败时
```json
{
    "status": "error",
    "message":"file not found: 12345678.xlsx"
}
```
## 判断是否需要Ocr环境

### 接口描述
本接口判断当前要操作的是否需要Ocr环境。

### 请求说明

#### 请求URI
POST 

`http://localhost:8087/api/judge_ocr_environment`

#### 请求Header参数

|参数|类型|是否必须|描述|示例值|
| ----------- | ----------- | ----------- | ----------- | ----------- |
| Content-Type | string  | 是 | 请求体的编码格式 | application/form-data |

#### 请求Body参数

Form字段数据结构说明

|参数|类型|是否必须|描述|
| ----------- | ----------- | ----------- | ----------- |
| pdfFile | file | 是 | 目标pdf文件。 |
| pages | string | 否 | 指定转换页面，多个页码用英文逗号分隔（如：1,2-5,8）; 不传值或空字符，则判断全部页面  |

### 返回说明

#### 返回参数

|字段|类型|描述|
| ----------- | ----------- | ----------- |
| status | string | success 成功; error 失败 |
| message | string | api执行消息，一般为api执行失败时的提示信息。成功的情况是Json结构可以拿到对应的环境 |
| --needOcr | bool | 判断是否需要对应的ocr环境： |

#### 返回示例
当入参returnType=text时，返回值的结果
```json
{
    "status": "success",
    "message": {
        "needOcr": false,
        "version": "1.1.4"
    }
}

```

## 发票识别

### 接口描述
本接口实现电子发票的识别，返回发票数据。

### 请求说明

#### 请求URI
POST 

`http://localhost:8087/api/invoice`

#### 请求Header参数

|参数|类型|是否必须|描述|示例值|
| ----------- | ----------- | ----------- | ----------- | ----------- |
| Content-Type | string  | 是 | 请求体的编码格式 | application/form-data |

#### 请求Body参数

Form字段数据结构说明

|参数|类型|是否必须|描述|
| ----------- | ----------- | ----------- | ----------- |
| invoiceFile | file | 是 | 电子发票pdf文件。 |
| invoiceType | string | 否 | 发票类型：full/regular/special; full-全电,regular-普票,special-专票。不传值或空值时，自动判断类型 |

### 返回说明

#### 返回参数

|字段|类型|描述|
| ----------- | ----------- | ----------- |
| status | string | success 成功; error 失败 |
| message | string | api执行消息，一般为api执行失败时的提示信息。 |
| data | array | 识别结果集合。 详细看【返回示例】 |

#### 返回示例

```json
{
    "status": "success",
    "message": "",
    "data": [
        {
            "InvoiceType": {
                "value": "电子发票(普通发票)",
                "name": "发票类型",
                "type": "32"
            },
            "InvoiceNumber": {
                "value": "24447200000315707890",
                "name": "发票号码"
            },
            "InvoiceCode": {
                "value": null,
                "name": "发票代码"
            },
            "IssueTime": {
                "value": "20241111",
                "name": "开票日期"
            },
            "Drawer": {
                "value": "王梅",
                "name": "开票人"
            },
            "CheckCode": {
                "value": null,
                "name": "校验码"
            },
            "PwdArea": {
                "value": null,
                "name": "密码区"
            },
            "TotalAmWithoutTax": {
                "value": "81.99",
                "name": "不含税金额"
            },
            "TotalTaxAm": {
                "value": "10.65",
                "name": "税额合计"
            },
            "TotalTaxIncludedAmount": {
                "value": "92.64",
                "name": "价税合计"
            },
            "TotalTaxIncludedAmountInChinese": {
                "value": "玖拾贰圆陆角肆分",
                "name": "价税合计（大写）"
            },
            "Remark": {
                "value": "订单号:294662572531",
                "name": "备注"
            },
            "Payee": {
                "value": null,
                "name": "收款人"
            },
            "Receiver": {
                "value": null,
                "name": "复核"
            },
            "SellerIdNum": {
                "value": "91440101MA9UQLD53M",
                "name": "销方纳税人识别号"
            },
            "SellerName": {
                "value": "广州京东旭春贸易有限公司",
                "name": "销方单位名称"
            },
            "SellerBankAccNum": {
                "value": null,
                "name": "销方开户行及账号"
            },
            "SellerAddrTelNum": {
                "value": null,
                "name": "销方地址、电话"
            },
            "BuyerIdNum": {
                "value": "91440400632834484E",
                "name": "购方纳税人识别号"
            },
            "BuyerName": {
                "value": "同望科技股份有限公司",
                "name": "购方单位名称"
            },
            "BuyerAddr": {
                "value": null,
                "name": "购方地址"
            },
            "BuyerBankAccNum": {
                "value": null,
                "name": "购方开户行及账号"
            },
            "BuyerAddrTelNum": {
                "value": null,
                "name": "购方地址、电话"
            },
            "IssuItemInformation": [
                {
                    "ItemName": {
                        "value": "*美容护肤品*丝塔芙净润温和洁面乳591ml蓝\n朋友无泡洗面奶保湿清洁补水敏感肌男女",
                        "name": "项目名称"
                    },
                    "SpecMod": {
                        "value": "丝塔芙洁面乳591ml",
                        "name": "规格型号"
                    },
                    "MeaUnits": {
                        "value": "瓶",
                        "name": "单位"
                    },
                    "Quantity": {
                        "value": "1",
                        "name": "数量"
                    },
                    "UnPrice": {
                        "value": "83.19",
                        "name": "单价"
                    },
                    "Amount": {
                        "value": "83.19",
                        "name": "金额"
                    },
                    "TaxRate": {
                        "value": "13%",
                        "name": "税率"
                    },
                    "ComTaxAm": {
                        "value": "10.81",
                        "name": "税额"
                    }
                },
                {
                    "ItemName": {
                        "value": "*美容护肤品*丝塔芙净润温和洁面乳591ml蓝\n朋友无泡洗面奶保湿清洁补水敏感肌男女",
                        "name": "项目名称"
                    },
                    "SpecMod": {
                        "value": null,
                        "name": "规格型号"
                    },
                    "MeaUnits": {
                        "value": null,
                        "name": "单位"
                    },
                    "Quantity": {
                        "value": null,
                        "name": "数量"
                    },
                    "UnPrice": {
                        "value": null,
                        "name": "单价"
                    },
                    "Amount": {
                        "value": "-1.20",
                        "name": "金额"
                    },
                    "TaxRate": {
                        "value": "13%",
                        "name": "税率"
                    },
                    "ComTaxAm": {
                        "value": "-0.16",
                        "name": "税额"
                    }
                }
            ]
        }
    ]
}
```
