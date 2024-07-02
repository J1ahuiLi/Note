# 新建模块
1. new -> other -> YonBuilder -> 业务组件
# 新建节点
1. MDP资源管理器 -> 新建实体组件 -> 设计元数据 -> 发布元数据。
    1. 主体（右键） -> 特征 -> 组织信息，审计信息，档案自定义项（有审批则需额外添加富客户端单据信息，审批流信息）。
    2. 从其他元数据复制 业务接口（id，pid）和（pk锁）。
    3. 添加pk主键，并设置为主键，且长度为20。
    4. 添加code（编码），name（名称），enablestate（启用状态：baseapp -> pubenum）字段。
    5. 配置业务接口属性映射
        * id：主键
        * pk_org：组织
        * code：作为参照时的编码，没有则为编码（code）
        * name：作为参照时的名称，没有则为名称（name）
        * pk_group：集团
        * enablestate：启用状态
        * pk锁：主键
    6. 主子表或多子表中主表对应的实体，访问器要设置为AggVO，其他一般选择NCVO
    7. 发布元数据。
2. 应用工厂 -> 代码定制 -> 生成代码。
3. 合并后端代码
    1. 将src下的private和public与后端工程相关模块代码下的src合并。
    2. 将src下的client与后端工程相关项目下的nccloud/src/client合并。
    3. 将META-INF与后端工程相关模块代码下的META-INF合并（upm文件）。
4. 合并前端代码
    1. 将src与前端工程代码中的src文件夹合并。
5. 菜单注册 -> 自定义菜单升级
6. 职责_集团 -> 分配应用
6. 配置前端config.js
7. 将Action文件与xml文件复制到home里
## 相关报错处理
1. pk_md_bizitfmap冲突：
```
DELETE FROM MD_BIZITFMAP WHERE intattrid = '60bdf1fb-95e3-43dc-8feb-f874de9293eb' AND classid = 'da871b0f-d620-4932-bdbd-935eb37ad57b'
```
2. 


# 补丁
## 导出补丁
### 前端
1. 在config.json文件的buildEntryPath下添加节点路径和refer路径
2. TERMAINL中执行npm run patch
3. 将前端补丁replacement/hotwebs/nccloud/resources放入后端补丁replacement/hotwebs/nccloud/resources中
### 后端
1. 编译代码。
2. 选中所有与节点相关代码文件（public, META-INF, client, private）导出。
3. Export->NC Cloud/NC Cloud补丁包->填写补丁名称->选择导出文件路径（补丁命名规范：patch_yyyymmdd_ddmmss_开发者姓名_补丁名）
4. 将home/resources/excel/billdefine下相关模块中的xml文件复制到replacement文件夹相同路径下（replacement文件夹等于home文件夹）。
5. 将upm文件复制到replacement/modules/模块名/META-INF下。
## 上传补丁
* 地址10.18.18.62:9999/nmc
* 账号密码 admin/admin
* 补丁管理 -> 补丁上传 -> 补丁应用（修改补丁包中packmetadata.xml文件中的id，则可再次上传）
## 抽脚本
1. 升级元数据。
2. 修改模块或节点编码，在数据库中执行并生成sql。
``` sql
--菜单
SELECT * FROM SM_APPMENUITEM WHERE MENUITEMCODE LIKE '%20H9%' AND PK_MENU = '1004Z510000000000FFL' ORDER BY ts DESC;
SELECT * FROM DAP_DAPSYSTEM WHERE Moduleid IN ('20H9')   ;
--节点
SELECT * FROM SM_APPREGISTER WHERE  parent_id IN ('20H9') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H9'));
--页面
SELECT * FROM SM_APPPAGE WHERE  PARENTCODE LIKE '20H9%';
--模板
SELECT * FROM PUB_PAGE_TEMPLET WHERE APPCODE  LIKE '20H9%';
--区域
SELECT * FROM pub_area WHERE TEMPLETID IN (SELECT PK_PAGE_TEMPLET FROM PUB_PAGE_TEMPLET WHERE APPCODE  LIKE '20H9%' );
--查询区
SELECT * FROM PUB_QUERY_PROPERTY WHERE AREAID IN (SELECT PK_AREA FROM pub_area WHERE TEMPLETID IN (SELECT PK_PAGE_TEMPLET FROM PUB_PAGE_TEMPLET WHERE APPCODE  LIKE '20H9%' ));
--表单区
SELECT * FROM PUB_FORM_PROPERTY WHERE AREAID IN (SELECT PK_AREA FROM pub_area WHERE TEMPLETID IN (SELECT PK_PAGE_TEMPLET FROM PUB_PAGE_TEMPLET WHERE APPCODE  LIKE '20H9%' ));
--按钮
SELECT * FROM SM_APPBUTNREGISTER WHERE PAGECODE LIKE '20H9%' ;
--导入导出
SELECT * FROM EXCEL_OUTPUTPROCESS  WHERE  MODULE ='TOACCFLOW' AND BILLTYPE LIKE '%20H9%';
SELECT * FROM EXCEL_BILLPROCESS  WHERE   MODULE ='TOACCFLOW' AND BILLTYPE LIKE '%20H9%';
SELECT * FROM EXCEL_TRANSLATOR WHERE MODULE ='toaccflow' AND TRACLASSNAME LIKE '%ztfilp%' ORDER BY ts DESC ;
--参照
SELECT * FROM BD_REFINFO  WHERE MODULENAME  = 'toaccflow' AND code like ('%20H9%');
--打印
SELECT * FROM  PUB_PRINT_TEMPLATE  WHERE APPCODE IN ( SELECT CODE FROM SM_APPREGISTER WHERE  parent_id IN ('20H9') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H9')));

SELECT * FROM   pub_print_datasource WHERE ctemplateid IN (SELECT ctemplateid FROM  PUB_PRINT_TEMPLATE  WHERE APPCODE IN (
SELECT CODE FROM SM_APPREGISTER WHERE  parent_id IN ('20H9') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H9'))
)
);

SELECT * FROM PUB_PRINT_CELL WHERE ctemplateid IN (SELECT ctemplateid FROM  PUB_PRINT_TEMPLATE  WHERE APPCODE IN (
SELECT CODE FROM SM_APPREGISTER WHERE  parent_id IN ('20H9') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H9'))
)
);

SELECT * FROM pub_print_variable WHERE ctemplateid IN (SELECT ctemplateid FROM  PUB_PRINT_TEMPLATE  WHERE APPCODE IN (
SELECT CODE FROM SM_APPREGISTER WHERE  parent_id IN ('20H2') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H9'))
)
);

SELECT * FROM  pub_print_region WHERE ctemplateid IN (SELECT ctemplateid FROM  PUB_PRINT_TEMPLATE  WHERE APPCODE IN (
SELECT CODE FROM SM_APPREGISTER WHERE  parent_id IN ('20H9') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H9'))
)
);

--删除相关数据（导入错误数据后使用）
DELETE FROM SM_APPMENUITEM WHERE APPCODE LIKE '%20H2%' AND PK_MENU = '1004Z510000000000FFL' AND MENUITEMCODE <> '20H200TW';
DELETE FROM DAP_DAPSYSTEM WHERE Moduleid IN ('20H2')  ; 
--节点
DELETE FROM SM_APPREGISTER WHERE  parent_id IN ('20H2') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H2'));
--页面
DELETE FROM SM_APPPAGE WHERE  PARENTCODE LIKE '20H2%';
--模板
DELETE FROM PUB_PAGE_TEMPLET WHERE APPCODE  LIKE '20H2%';
--区域
DELETE FROM pub_area WHERE TEMPLETID IN (SELECT PK_PAGE_TEMPLET FROM PUB_PAGE_TEMPLET WHERE APPCODE  LIKE '20H2%' );
--查询区
DELETE FROM PUB_QUERY_PROPERTY WHERE AREAID IN (SELECT PK_AREA FROM pub_area WHERE TEMPLETID IN (SELECT PK_PAGE_TEMPLET FROM PUB_PAGE_TEMPLET WHERE APPCODE  LIKE '20H2%' ));
--表单区
DELETE FROM PUB_FORM_PROPERTY WHERE AREAID IN (SELECT PK_AREA FROM pub_area WHERE TEMPLETID IN (SELECT PK_PAGE_TEMPLET FROM PUB_PAGE_TEMPLET WHERE APPCODE  LIKE '20H2%' ));
--按钮
DELETE FROM SM_APPBUTNREGISTER WHERE PAGECODE LIKE '20H2%' ;
--导入导出
DELETE FROM EXCEL_OUTPUTPROCESS  WHERE  MODULE ='TOACCFLOW' AND BILLTYPE LIKE '%20H2%';
DELETE FROM EXCEL_BILLPROCESS  WHERE   MODULE ='TOACCFLOW' AND BILLTYPE LIKE '%20H2%';
DELETE FROM EXCEL_TRANSLATOR WHERE MODULE ='toaccflow' AND TRACLASSNAME LIKE '%irbps%' ORDER BY ts DESC ;
--参照
DELETE FROM BD_REFINFO  WHERE MODULENAME  = 'toaccflow' AND code like ('%20H2%');
--打印
DELETE FROM  PUB_PRINT_TEMPLATE  WHERE APPCODE IN ( SELECT CODE FROM SM_APPREGISTER WHERE  parent_id IN ('20H2') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H2')));

DELETE FROM   pub_print_datasource WHERE ctemplateid IN (SELECT ctemplateid FROM  PUB_PRINT_TEMPLATE  WHERE APPCODE IN (
SELECT CODE FROM SM_APPREGISTER WHERE  parent_id IN ('20H2') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H2'))
)
);

DELETE FROM PUB_PRINT_CELL WHERE ctemplateid IN (SELECT ctemplateid FROM  PUB_PRINT_TEMPLATE  WHERE APPCODE IN (
SELECT CODE FROM SM_APPREGISTER WHERE  parent_id IN ('20H2') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H2'))
)
);

DELETE FROM pub_print_variable WHERE ctemplateid IN (SELECT ctemplateid FROM  PUB_PRINT_TEMPLATE  WHERE APPCODE IN (
SELECT CODE FROM SM_APPREGISTER WHERE  parent_id IN ('20H2') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H2'))
)
);

DELETE FROM  pub_print_region WHERE ctemplateid IN (SELECT ctemplateid FROM  PUB_PRINT_TEMPLATE  WHERE APPCODE IN (
SELECT CODE FROM SM_APPREGISTER WHERE  parent_id IN ('20H2') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H2'))
)
);
```
3. 上传补丁
4. 在数据库中创建相关表
``` sql
--导表脚本
```

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
# 国寿开发笔记
## SAP & JAVA 
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

## 元数据参照
### 财务会计
业务单元: 组织 -> 业务单元  
会计年度: 会计期间档案 -> 会计年度  
会计期间: 会计月份  
利润中心: 利润中心  
县支公司: 成本中心 -> 管会成本中心  
成本中心: 组织_部门





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

jps -ml
kill -9 进程


# Java
1. 根据逗号截取字符串存入List
``` Java
String str = "one,two,three";
List<String> strList = Arrays.asList(str.split(","));
```
2. 字符串逗号分隔加引号
``` Java
// 方法一: 
// String str = "TODO (Features)" 结果为 'TODO (Features)'
public static String spilt(String str) {
        StringBuffer sb = new StringBuffer();
        String[] temp = str.split(",");
        for (int i = 0; i < temp.length; i++) {
            if (!"".equals(temp[i]) && temp[i] != null)
                sb.append("'" + temp[i] + "',");
        }
        String result = sb.toString();
        String tp = result.substring(result.length() - 1, result.length());
        if (",".equals(tp)){
            return result.substring(0, result.length() - 1);
        } else{
            return result;
        }
// 方法二:
// String str = "TODO (Features)" 结果为 'TODO' ('Features')
replaceAll("\\b", "'") 
```
3. List按某字段排序
``` Java 1.8
List<User> newList = list.stream().sorted(Comparator.comparing(User::getAge)).collect(Collectors.toList());
```
3. 判断List中是否有重复元素
``` Java
// 方法一：将List转为Set，通过2个集合的size大小是否相等来判断有无重复元素
public static void main(String[] args) {
    List<String> stringList=new ArrayList<>(Arrays.asList("a","a","b","c"));
    Set<String> stringSet=new HashSet<>(stringList);
    if (stringList.size() == stringSet.size()) {
        System.out.println("没有重复元素");
    } else {
        System.out.println("有重复元素");
    }
}
// 方法二：使用jdk8的Stream来判断
public static void main(String[] args) {
    List<String> stringList=new ArrayList<>(Arrays.asList("a","a","b","c"));
    long count = stringList.stream().distinct().count();
    if (stringList.size() == count) {
    System.out.println("没有重复元素");
    } else {
    System.out.println("有重复元素");
    }
}
```
3. List按某字段排序
``` java
```
3. List按某字段排序
``` java
```
3. List按某字段排序
``` java
```
3. List按某字段排序
``` java
```
3. List按某字段排序
``` java
```
3. List按某字段排序
``` java
```
3. List按某字段排序
``` java
```
3. List按某字段排序
``` java
```
3. List按某字段排序
``` java
```





非本地升级元数据:业务系统管理 -> 模型定制管理 -> 元数据管理 -> 升级

查询元数据版本号: 
```sql 
SELECT t.version FROM md_component t WHERE t.id='';
```


62数据库已迁移。
新地址： 10.18.60.225 3306     
用户：CLIC_MR_TEST2@fmisV8dbtest#rcobc03:1661242987 密码：Kjy2023

62服务器：10.18.18.62
用户名/密码：root/Kjy@2023

业务单元：组织 -> 业务单元
利润中心：成本中心 -> 管会成本中心
县支公司：组织_部门 -> 组织



state  
0：默认  
1：修改  
2：新增  
3：删除  


## 添加分页
### 前端
创建state模型下，相关模块区域下添加
```js
handleModal: {
    showPagination: true, // 显示分页器
    // 页器操作的回调函数
    handlePageInfoChange: (props, config, pks, total) => { 
    // 加载表格数据  -> 将数据设置到表格上  -> 更新按钮状态
        this.onHandle(null, 'handleModal');
        this.setPageStatus(EDITMODE_EDIT, this.updateBtnStatus);
    }
},
    
onHandle = (param = {}, modalType) => {
    let pageInfo = this.props.editTable.getTablePageInfo(this.state.handleModal.area); // 页面分页信息   
    ajax({
        url: '',
        data: { pageInfo, queryTreeFormatVO: { ...queryInfo, pageCode: this.config.pagecode } },
        success: (res) => {
                if (res.data != null) {
                    this.props.editTable.setTableData(handleModal.area, res.data.data['cit_daily_credTable']);
                }
            }
        })
    }
```
### 后端
```java
// json数据转换
public Object doAction(IRequest request, RequstParamWapper paramWapper) throws Throwable {
    RequestDTO param = VOTransform.toVO(paramWapper.requestString, RequestDTO.class);
    QueryTreeFormatVO queryVo = param.getQueryTreeFormatVO();
    ITService service = ServiceLocator.find(ITService.class);
    List<T> result = service.handleModal(queryVo);
    List<String> allpksList = new ArrayList<String>();
    for (T resultItem : result) {
        allpksList.add(resultItem.getDef2());
    }
    if (allpksList == null || allpksList.size() < 1) {
        return null;
    }
    // 获取结果pk
    String[] allpks = (String[]) allpksList.toArray(new String[allpksList.size()]);
    //  传入全部数据
    param.setAllpks(allpks);
    //  获取页面信息
    PageInfo pageInfo = param.getPageInfo();
    // 根据分页拿到当前分页的pk
    String[] curPagePks = pageInfo == null ? allpks : paramWapper.pageResult(pageInfo, allpks);
    // 查询当前分页数据
    List<T> finalResult = result.stream().filter(m -> Arrays.stream(curPagePks).anyMatch(def2 -> def2 == m.getDef2())).collect(Collectors.toList());
    // 构造返回结果
    return buildResult(param, false, null, finalResult.toArray());
}
```
### 数据库
```sql
SELECT * FROM pub_area WHERE code = '';
UPDATE pub_area SET pagination = 'true' WHERE code = '';
```