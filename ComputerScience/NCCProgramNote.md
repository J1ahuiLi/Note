# 新建节点
1. MDP资源管理器 -> 新建实体组件 -> 设计元数据 -> 发布元数据。
    1. 主体（右键） -> 特征 -> 组织信息，审计信息，档案自定义项（有审批则需额外添加富客户端单据信息，审批流信息）。
    2. 从其他元数据复制 业务接口（id，pid）和（pk锁）。
    3. 添加 pk主键，并设置为主键，且长度为20
    4. 添加code（编码），name（名称）字段。
    5. 发布元数据。
    6. 主子表或多子表中主表对应的实体，访问器要设置为AggVO，其他一般选择NCVO
2. 应用工厂 -> 代码定制 -> 生成代码。
3. 合并后端代码
    1. 后端代码src下的private和public与后端工程代码相关文件（basedoc)的src中对应的文件夹合并。
    2. 后端代码src下的client与后端工程代码nccloud/src/client合并。
    3. 后端代码METADATA和META-INF与后端工程代码相关文件（basedoc)中对应的文件夹合并。
4. 合并前端代码
    1. 前端代码src与前端工程代码中的src文件夹合并。

# 补丁
## 导出补丁
### 前端
1. 在config.json文件的buildEntryPath下添加节点路径和refer路径
2. TERMAINL中执行npm run patch
3. 补丁中只保留节点相关代码
4. 将前端补丁replacement/hotwebs/nccloud/resources放入后端补丁replacement/hotwens/nccloud/resources中
### 后端
1. 编译代码。
2. 选中所有与节点相关代码文件（public, META-INF, client, private）导出。
3. Export->NC Cloud/NC Cloud补丁包->填写补丁名称->选择导出文件路径（补丁命名规范：patch_yyyymmdd_ddmmss_开发者姓名_补丁名）
4. 将生成代码中的resources文件夹移动到后端补丁replacement文件夹下。
## 上传补丁
* 地址10.18.18.62:9999/nmc
* 账号密码 admin/admin
* 补丁管理 -> 补丁上传 -> 补丁应用（修改补丁包中packmetadata.xml文件中的id，则可再次上传）
## 抽脚本
1. 修改脚本中节点数据，在数据库中执行并生成sql

# 数据库

| 表名 | 说明 |
| - | - |
| md_moudle | 元数据所属模块信息 |
| md_component | 元数据信息 |
| md_class | 元数据实体信息 |
| md_enumvalue | 元数据枚举值信息 |
| md_property | 元数据实体属性信息 |
| md_association | 元数据连接线信息 |
| md_accessorpara | 聚合类ID和聚合类全类名信息 |
| md_bizitfmap | 实体和业务接口属性映射关系 |
| md_ormap | 实体属性和数据库表字段对应关系 |
| md_table | 元数据创建表信息 |
| md_column | 元数据表列信息 |
| sm_appregister | 应用注册 |
2. 
```sql
SHOW CREATE TABLE table_name
ALTER TABLE table_name MODIFY column_name VARCHAR(100)
SELECT * FROM table_name WHERE column_name LIKE ‘%condition%’
DROP TABLE table_name
SELECT * FROM table_name FETCH FIRST 1 ROWS ONLY
```
# SAP & JAVA 
| SAP | JAVA |
| - | - |
| X IS NOT INITIAL. | import org.apache.commons.lang3.StringUtils; <br> StringUtils.isNotBlank(X); |
| TRANSLATE X TO UPPER CASE. | X.toUpperCase(); |
| UNPACK seq TO str. | str = String.valueOf(seq); |
| MOVE-CORRESPONDING <gs_saknr_tmp> TO gt_saknr_header. | TypeSaknr2VO gs_saknr_header = new TypeSaknr2VO(); <br> BeanUtils.copyProperties(gs_saknr_tmp, gs_saknr_header); |
| Sys_user | InvocationInfoProxy.getInstance(); |
| MOVE-CORRESPONDING <fs_item> TO gs_item. | BeanUtils.copyProperties(fs_item, gs_item); |
| CONCATENATE x y INTO z. | String z = x + y; |
| sy_datum | new UFDateTime() |
| SAP | JAVA |
| SAP | JAVA |
| SAP | JAVA |
| SAP | JAVA |
| SAP | JAVA |
| SAP | JAVA |
| SAP | JAVA |
| SAP | JAVA |
| SAP | JAVA |
| SAP | JAVA |
| SAP | JAVA |
| SAP | JAVA |
| SAP | JAVA |
| SAP | JAVA |


```java
/**
 * @Author: Jiahui Li
 * @Date: YYYY-MM-DD
 * @Description: 
 */

String strListSql = "SELECT * FROM tableName";  
List<T> gt_track1990 = (List<T>) new BaseDAO().executeQuery(strListSql, new BeanListProcessor(T.class));

String strMapSql = "SELECT s1, s2 FROM tableName";
Map<String, String> typeXmMap = (Map<String, String>) new BaseDAO().executeQuery(strMapSql.toString(), new KeyValueMapProcessor<String, String>());

String lt_taxSql = "SELECT zitem FROM zirbps_tax WHERE bschl = '50'";
List<String> lt_taxList = (List<String>) dao.executeQuery(lt_taxSql.toString(), new ArrayListProcessor());

UFDouble.ZERO_DBL

String sql2 = "update zirbps_sf_pclog set batchstatus = 'C1' where batch_head = '" + fs_head.getBatch_head()+ "'";
baseDAO.executeUpdate(sql1);



BeanUtils.copyProperties(pkRisk, tempYW);


String updateZtfilp_headSql = "update ztfilp_head set poststatus = '03' where gjahr = '" + gs_handle.getGjahr() + "' AND bukrs = '" + gs_handle.getBukrs() + "' AND monat = '" + gs_handle.getMonat() + "'";
dao.executeUpdate(updateZtfilp_headSql);




```
前端取数据
loadlist



# Note
## 前端
### 架构
* React架构  
* 脚手架规则：src -> 项目 -> 模块 -> 节点 -> 页面 -> index.js
* 配置文件：config.json
### 开发

## 后端
### 架构
* 采用前端框架异步请求到后端，通过 ServiceLocator 调用NC服务。  
* NC服务专注于内部核心业务逻辑，由WEB服务处理交互，显示，鉴权，安全，分流，集成等其他逻辑。
* 前后台交互采用JSON，作为数据传输格式。
* NC容器不能反调Web容器
### 基本原则
* ServiceLocator是WEB容器调用NC容器服务唯一入口（Web容器可以调用NC容器public端）
* WEB容器不允许直接访问数据库
* WEB容器不允许直接调用NCLocator
* WEB容器不允许调用InvocationInfoProxy
* WEB容器获取环境信息SessionContext
### 开发
#### hotwebs/client（Action层/Controller层）
* client需以public作为中间层，通过ServiceLocator远程调用private
* 隶属于前端，起控制器作用
* Action的配置文件和鉴权文件在home/hotwebs/nccloud/WEB-INF/extend下的文件目录中（非开发环境在lib下的jar包中）。
* 在userdef.xml中配置单据或档案中的自定义项及其相关。
* Action接口是ICommonAction。
```java
//json数据转换，并获取参数
RequestDTO param = VOTransform.toVO(paramWapper.requestString,RequestDTO.class);
Map<String,Object> userJson = param.getUserJson();
Toaccflow_ztfilp_sub[] vos = this.getVOs(param, Toaccflow_ztfilp_sub.class);
//获得实体VO
IToaccflow_ztfilp_subService service = ServiceLocator.find(IToaccflow_ztfilp_subService.class);
```


#### home/modules/public
* 包括接口类，实体类，工具类
#### home/modules/private
* 包括接口的实现类(service层)
* 不可使用client端的代码
#### 服务启动
* 读取Action配置文件、鉴权文件等 -> 供对应的单据请求对应的Action。
* 读取upm配置文件 -> 供远程调用Action。
* 读取固定的xml配置文件


### 参照
#### 树形参照
* 前端：rootNode和TreeConfig
* 后端：DefaultTreeRefAction
#### 表形参照
* 前端：columnConfig
* 后端：DefaultGridRefAction
#### 树表形参照
* 两次请求，一次树形参照，一次表形参照
#### 实现原理
* bd_refinfo里插入一条参照数据。
    * refclass：原NC参照（YonBIP高级版可为空）
    * refpath：YonBIP高级版前端js路径
    * mobilerefpath：移动端前端js路径
* 画参照元数据（配置模型视图->参照）
* 在DefaultRefSqlbuilder类中拼接sql条件
    * getRefMeta()：查询的字段和查询的表名
    * getExtraSql()：where后的条件语句
    * getExtralSqlParameter()：where后条件语句中占位符对应的真实数据（为了防止Sql注入）
    * getOrderSql()：order by内容
* 配置参照action映射文件以及authorize鉴权文件
* 参照前端js

### 编码规则
编码规则定义表：pub_bcr_rulebase  
编码规则元素表：pub_bcr_elem 
#### 编码规则定义
* 编码方式：
    * 前编码方式：新增单据时即生成编码（不能支持在编码中出现业务实体和业务时间）
    * 后编码方式：新增单据后，保存时生成单据编码
* 编码是否可编辑：对于前编码方式可用
* TODO

### 导入导出功能
#### 设计思路
* 配置文件：必须要在home中注册对应档案的配置文件
* 确定导出项
* 导入数据处理
* 错误信息提示和日志功能

#### 后端
* XML配置文件：导入档案主子vo关系配置，导入字段顺序设置，动态为参照字段设置参数
    * XML配置文件命名：单据类型编码.xml 
    * XML配置文件位置： NCCHome\resources\excel\billdefine\模块编码\xml。（对应前端传对的模块名）
    * metadataid：对应vo的元数据id，可以从md_class表中进行查询
* 处理导入插件类实现（private端）
    * 继承 nccloud.bs.excel.plugin.AbstractImportProcess 
    * 重写 protected void processBill(Object vo) throws BusinessException 方法


## 环境搭建
### 工具
* Eclipse
* VSCode
* Node.js










非本地升级元数据:业务系统管理 -> 模型定制管理 -> 元数据管理 -> 升级

查询元数据版本号: 
```sql 
SELECT t.version FROM md_component t WHERE t.id='';
```