## EasyDBC 与 Excel 互转工具

> DBC与Excel互转工具（EasyDBC）

### 项目简介

EasyDBC 是一款用于DBC文件与Excel文件双向转换的工具，旨在简化CAN/LIN总线开发中数据库文件的编辑与管理流程。工具内置Excel模板校验功能，确保数据格式合规性，同时提供直观的GUI界面，降低操作门槛。

- 核心功能：DBC → Excel 转换、Excel → DBC 转换、DBC→C代码、模板文件生成、Excel数据校验

- 适用场景：汽车电子CAN总线开发、嵌入式系统通信数据库编辑、CANoe/CANalyzer工具数据交互

- 开发语言：Python 3.x

- 当前版本：V0.1.0

### 运行截图

<img src=".\_doc\_image\image1.png" alt="image1" style="zoom:60%;" />

<img src=".\_doc\_image\image2.png" alt="image2" style="zoom:60%;" />

<img src=".\_doc\_image\image3.png" alt="image3" style="zoom:60%;" />

<img src=".\_doc\_image\image4.png" alt="image4" style="zoom:60%;" />

<img src=".\_doc\_image\image5.png" alt="image5" style="zoom:60%;" />

### 环境依赖

使用前需安装以下Python依赖库，建议通过pip命令批量安装：

```bash
pip install -r requirements.txt
```

依赖列表


|依赖库|版本要求|用途说明|
|---|---|---|
|`cantools`|≥37.0.0|DBC文件解析与生成|
|`openpyxl`|≥3.1.2|Excel文件读写与格式设置|
|`PyQt5`|≥5.15.9|图形用户界面（GUI）开发|

### 快速开始

#### 1.工具启动

直接运行主程序文件，启动GUI界面：

```sh
python EasyDBC.py
```

#### 2.核心操作流程

##### 2.1 生成模板文件（首次使用推荐）

1. 在GUI界面点击「生成模板」按钮

2. 工具将自动在当前工作目录生成两个模板文件：

     - template.xlsx：Excel编辑模板（含格式约束与示例数据）
     - template.dbc：DBC标准模板（含VCU、BMS节点示例）


##### 2.2 DBC → Excel 转换

1. 点击「选择文件」，选择待转换的 .dbc 文件

2. 点击「选择路径」，指定转换后Excel文件的保存目录

3. 点击「转换」，工具自动生成 xxx_converted.xlsx 文件

##### 2.3 Excel → DBC 转换

1. **重要前提：Excel文件必须遵循 template.xlsx 格式，不可随意修改表头**
2. 点击「选择文件」，选择填写完成的 .xlsx 文件
3. 点击「选择路径」，指定转换后DBC文件的保存目录
4. 点击「转换」，工具自动校验Excel数据并生成 xxx_converted.dbc 文件

##### 2.4 DBC→C代码

1. **重要前提：DBC文件需要通过CANdb++等工具进行二次检查，确保正常后可以使用本工具一键生成对应的c语言库。由于本工具在进行DBC转换时会在文件名后追加_converted用于区分，在二次检查DBC正确后，建议修改文件名如CAN1.dbc等，千万不能出现中文！！！千万不能出现中文！！！千万不能出现中文！！！因为DBC文件名将决定c语言结构体的命名规则！！！**
2. 点击「选择文件」，选择待转换的 .dbc 文件
3. 点击「选择路径」，指定生成的C语言库的保存目录
4. 点击「DBC转C代码」,工具自动校验DBC文件，并生成对应的C代码（.h,.c）

### Excel模板规范

#### 1.模板参考

Excel模板请以工具生成的 template.xlsx模板为准，禁止修改表头格式，否则会导致转换失败。

#### 2.数据校验规则

工具会对Excel文件进行自动校验，校验不通过将终止转换并提示错误原因。校验范围包括但不限于：

|校验项|校验规则|
|---|---|
|表头格式校验|必须与模板完全一致（如“Message Name 报文名称”“Signal Name 信号名称”）|
|报文名称唯一性校验|所有“报文名称”不可重复|
|报文ID校验|16进制格式（如`0x100`），且不可重复|
|报文长度校验|CAN协议：8字节；CAN FD协议：8-64字节。本工具生成的DBC默认为支持CAN FD，增加了报文长度选项约束|
|报文周期时间校验|非负整数（单位：ms），支持空值（表示非周期报文）|
|信号名称唯一性校验|所有“信号名称”不可重复|
|信号最值校验|物理最小值 ≤ 物理最大值；总线最小值（16进制）与物理值计算逻辑一致。如果在编写Excel时不想计算总线最值，可以留空，只填写物理最值即可，本工具在生成dbc时会自动计算总线最值，DBC验证完毕后可以使用本工具进行dbc转excel操作，会在新的Excel中自动添加16进制的总线最值。**注意：物理最值不可留空**|
|节点有效性校验|节点必须关联至少1条报文，不允许“无意义节点”存在（仅存在节点但没有报文关联）|


#### 3.下拉选项约束

Excel模板中部分列已设置固定下拉选项，不可手动输入其他值，包括：

|列名|可选值|
|---|---|
|报文类型（CAN Type）|`CAN`、`CAN FD`|
|报文长度（Message Length）|`8`, `12`, `16`, `20`, `24`, `32`, `48`, `64`|
|报文格式（Message Type）|`CAN Extended`（扩展帧）、`CAN Standard`（标准帧）|
|发送类型（Message Send Type）|`Cycle`（周期）、`Event`（事件）、`On Change`（变化触发）、`Trigger`（触发）|
|排列格式（Byte Order）|`Motorola`（大端）、`Intel`（小端）|
|数据类型（Data Type）|`Unsigned`（无符号）、`Signed`（有符号）、`Float`、`Double`|
|节点收发标识|`T`（发送）、`R`（接收）|

### 功能模块说明

#### 1.核心函数

|函数名|功能描述|参数说明|
|---|---|---|
|`excel_to_dbc()`|将Excel文件转换为DBC文件|`excel_file`：输入Excel路径；`dbc_file`：输出DBC路径|
|`dbc_to_excel()`|将DBC文件转换为Excel文件|`dbc_file_path`：输入DBC路径；`excel_file_path`：输出Excel路径|
|`create_excel_template()`|生成标准Excel模板文件|`excel_file_path`：模板保存路径|
|`create_dbc_template()`|生成标准DBC模板文件|`dbc_file_path`：模板保存路径|
|`validate_excel_template()`|校验Excel文件格式与数据合法性|`excel_file_path`：待校验Excel路径；返回`True`（通过）/`False`（失败）|
|`dbc_to_c()`|DBC文件转C代码|`dbc_file_path`:目标DBC文件，`c_file_path`:代码保存路径|

#### 2.GUI界面说明

界面分为「文件选择区」「操作区」「日志区」三部分：

- 文件选择区：选择待转换文件（.dbc/.xlsx）与输出路径

- 操作区：包含「生成模板」「转换」「DBC转C代码」三个核心按钮，支持一键操作

- 日志区：实时显示操作过程（如文件选择、转换进度、错误提示）

### 常见问题（FAQ）

1. Q：Excel转换时提示“表头不匹配”？A：请确保Excel文件表头与模板完全一致，包括换行符（如“Message Name \n报文名称”），建议直接基于 template.xlsx 编辑。
2. Q：DBC转换Excel后，部分信号的物理值显示异常？A：检查DBC文件中信号的scale（精度）和offset（偏移量）是否合法，工具会自动基于 物理值=总线值×scale+offset 计算。
3. Q：生成模板时提示“权限不足”？A：请确保当前工作目录有写入权限，或手动指定其他有读写权限的目录。
4. Q：支持CAN FD协议的DBC文件转换吗？A：支持，Excel模板中“报文类型”列选择CAN FD即可，工具会自动校验报文长度（0-64字节）。
5. Q：DBC生成的C代码在Simulink中加载成BUS对象后，生成代码报错？A：Simulink基于BUS对象生成代码时，使用的是typedef方式定义结构体，但是本工具生成的c结构体使用的是struct。为了解决这个问题，可以另外定义一个interface.h，间接引用dbc生成c代码的头文件，并手动使用typedef xxx_t xxx_t的方式为can报文添加别名，然后Simulink通过引用这个interface.h，就能避免该问题的产生了。

### 开发与维护

- 作者：polaris

- 更新日期：2025-11-14

- 后续计划：

  1. 支持LIN总线LDF文件与Excel互转

  2. 增加批量转换功能（多文件批量处理）

  4. 增加DBC文件语法校验功能

### 免责声明

1. 本工具仅用于CAN总线开发辅助，转换结果需结合CANoe/CANalyzer/CANdb++等工具二次验证，避免因数据错误导致硬件故障。

2. 建议在转换前备份原始DBC/Excel文件，工具不对数据丢失或格式错误承担责任。

3. 不支持非标准DBC扩展格式（如自定义属性、用户定义信号类型），此类文件转换可能导致信息丢失。
