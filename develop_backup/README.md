通过公网服务器上的数据转发服务器进行数据服务的转发。

##  数据服务接口描述
- 数据上传
- 数据流下载
- 数据可视化snapshot


## 上传数据接口

- ### postman标准在线接口文档
  - https://documenter.getpostman.com/view/9819382/SWEDzuBT?version=latest

- 以下接口弃用
- ### http://111.229.14.128:8899/data POST
  - 上传任意类型数据
  - 支持多文件，单文件不做打包，多文件打包
  - 不做检验
  - 输入：
    - 参数部分：
      - 必填参数：name,userId,serverNode,origination,
      - 选填参数：info（json格式组织），access
    - 文件部分：
      - 任意数据文件+配置文件
  - 输出：文件提取索引id，输入的文件名参数
  - 错误返回：
     ```
    {
        code:-1,
        message:"xxxxx"
    }
      ``` 
  - 正确返回：
     ```
     {
       code:0,
       data:{
                {
                    "source_store_id": "854dd40d-89b9-4e1b-85c2-5ed2b1d7d449",
                    "file_name": "test"
                }
       }

     }
    
      ```  
  - 配置文件：
   ```
        <UDXZip>
          <Name >
            <add value="udxdata.xml" />
            <add value="schema.xml" />
          </Name>
          <DataTemplate type="id/schema/none">
            guid/udx schema
          </DataTemplate>
          
        </UDXZip>
    ```
- ### http://111.229.14.128:8899/udxzip POST
  - 专业用户
  - 必填参数：上传文件，name,userId,serverNode,origination,
  - 选填参数：info，access
  
  - 输入：
    - 参数部分：
      - 必填参数：name,userId,serverNode,origination,
      - 选填参数：info（json格式组织），access
    - 文件部分：
      - 只支持单文件
      - 专业zip包数据上传，zip中包含系列数据集，数据模板和配置i文件
  - 输出：文件提取索引id，输入的文件名参数
  
  - 接口检查：
    - 必填参数是否完整
    - 配置文件是否正确描述数据数目
  
  - 错误返回：
     ```
    {
        code:0,
        message:"xxxxx"
    }
      ``` 
  - 正确返回：
     ```
    {
        "source_store_id": "854dd40d-89b9-4e1b-85c2-5ed2b1d7d449",
        "file_name": "test"
    }
    ```
  - 数据留存：数据检验后，将解压后的文件，剔除配置文件的原始数据压缩包（以返回值id命名），以及原始数据zip保存为返回id命名的文件夹下
- ### http://111.229.14.128:8899/source/:type POST
  
  - type=tep, 原始数据+数据模板的数据上传方式
  - type=udx, mdl中的schema内容+udxdata上传方式
  
  - 必填参数：上传文件，name,userId,serverNode,origination,
  - 选填参数：info，access

  - 输入：
    - 参数部分：
      - 必填参数：name,userId,serverNode,origination,
      - 选填参数：info（json格式组织），access
    - 文件部分：
      - type=tep，原始数据 + 数据模板 + 配置i文件
      - type=udx, schema + udxdata + 配置i文件
  - 输出：文件提取索引id，输入的文件名参数
  - 接口检查：
  
    - 必填参数是否完整
    - 配置文件是否正确描述数据数目
    - ### type=tep时检查数据模板id是否出现在配置文件里
  
  - 错误返回：
     ```
    {
        code:0,
        message:"xxxxx"
    }
      ``` 
  - 正确返回：
     ```
    {
        "source_store_id": "854dd40d-89b9-4e1b-85c2-5ed2b1d7d449",
        "file_name": "test"
    }
        ```
  - 数据留存：数据检验后剔除配置文件保存为返回id命名的zip文件

- ### http://111.229.14.128:8899/randomsource POST
  
  - 上传任意类型数据
  - 支持多文件，只返回一个id
  - 不做检验
  - 输入：
    - 参数部分：
      - 必填参数：name,userId,serverNode,origination,
      - 选填参数：info（json格式组织），access
    - 文件部分：
      - 任意用户文件
  - 输出：文件提取索引id，输入的文件名参数
  - 错误返回：
     ```
    {
        code:0,
        message:"xxxxx"
    }
      ``` 
  - 正确返回：
     ```
    {
        "source_store_id": "854dd40d-89b9-4e1b-85c2-5ed2b1d7d449",
        "file_name": "test"
    }
        ```

-  以上接口的配置文件：

      ```
        <UDXZip>
          <Name >
            <add value="udxdata.xml" />
            <add value="schema.xml" />
          </Name>
          <DataTemplateId type="">
            guid
          </DataTemplateId>
          
        </UDXZip>

      ```




## 下载数据接口

- ### http://111.229.14.128:8899/data?uid=xxx GET
  - 下载指定类型的数据
  - 参数：
    - uid:上传数据返回的id
  - 返回值：文件数据流

- 以下接口弃用
- ### http://111.229.14.128:8899/zipsource?uid=xxx&type=xxx GET
  - 下载指定类型的数据
  - 参数：
    - uid:上传数据返回的id
    - type:
      - zip：专业用户zip
      - tep: 原始数据+数据模板
      - udx: mdl中读出的schema+udx.data
      - ran: 任意数据
  - 返回值：文件数据流


## 数据可视化接口

- ### http://111.229.14.128:8899/visual?uid=6b196f37-90e2-4d79-b844-4865db13a978 GET
- 数据可视化snapshot截图
- 参数：
 
  - uid:id,数据上传后返回的id
- 返回值：截图图片文件
- 若数据模板id在模板库中不存在：返回值为：
```
{ code:-1,
  message:'There are no existing data templates'
}
```
- 目前支持主流的gis数据文件：shp,tif
- shapefile数据可视化
![1](/snapShotCache/09ee3e4b-d350-45f7-b616-64027cc61494.png)
- tif数据可视化
![2](/snapShotCache/1ae50628-ec77-4e7e-b9f8-3c2316021357.png)
- ## 缓存说明：此接口支持缓存，若生成过数据图片，则会进行缓存，再次请求时直接获取缓存

- ### http://111.229.14.128:8899/visualnocache?uid=6b196f37-90e2-4d79-b844-4865db13a978 GET
- 数据可视化snapshot截图
- 参数：

  - uid:id,数据上传后返回的id
- 返回值：截图图片文件
- 若数据模板id在模板库中不存在：返回值为：
```
{ code:-1,
  message:'There are no existing data templates'
}
```
- 目前支持主流的gis数据文件：shp,tif
- shapefile数据可视化
![3](/snapShotCache/09ee3e4b-d350-45f7-b616-64027cc61494.png)
- tif数据可视化
![4](/snapShotCache/1ae50628-ec77-4e7e-b9f8-3c2316021357.png)
- ## 缓存说明：此接口不支持缓存，每次请求重新生成截图
