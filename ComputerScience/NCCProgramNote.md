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
7. 配置前端config.js
8. 将Action文件与xml文件复制到home里

## 相关报错处理

1. pk_md_bizitfmap冲突：

```sql
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
6. 将module.xml复制到replacement相关路径下。

## 上传补丁

* NMC
* 账号密码 admin/admin
* 补丁管理 -> 补丁上传 -> 补丁应用（修改补丁包中packmetadata.xml文件中的id，则可再次上传）

## 抽脚本

1. 升级元数据。
2. 修改模块或节点编码，在数据库中执行并生成sql

    ``` sql
    --应用菜单项注册
    SELECT * FROM SM_APPMENUITEM WHERE MENUITEMCODE LIKE '%20H9%' AND PK_MENU = '1004Z510000000000FFL' ORDER BY ts DESC;
    SELECT * FROM sm_createcorp WHERE FUNCCODE ='20H4'
    --模块信息
    SELECT * FROM DAP_DAPSYSTEM WHERE Moduleid IN ('20H9');
    --元数据模块实体
    SELECT * FROM md_module WHERE id = 'taxm'
    --应用注册
    SELECT * FROM SM_APPREGISTER WHERE  parent_id IN ('20H9') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H9'));
    --应用页面
    SELECT * FROM SM_APPPAGE WHERE  PARENTCODE LIKE '20H9%';
    --页面模板
    SELECT * FROM PUB_PAGE_TEMPLET WHERE APPCODE  LIKE '20H9%';
    --区域
    SELECT * FROM pub_area WHERE TEMPLETID IN (SELECT PK_PAGE_TEMPLET FROM PUB_PAGE_TEMPLET WHERE APPCODE  LIKE '20H9%' );
    --查询区字段
    SELECT * FROM PUB_QUERY_PROPERTY WHERE AREAID IN (SELECT PK_AREA FROM pub_area WHERE TEMPLETID IN (SELECT PK_PAGE_TEMPLET FROM PUB_PAGE_TEMPLET WHERE APPCODE  LIKE '20H9%' ));
    --表单字段属性
    SELECT * FROM PUB_FORM_PROPERTY WHERE AREAID IN (SELECT PK_AREA FROM pub_area WHERE TEMPLETID IN (SELECT PK_PAGE_TEMPLET FROM PUB_PAGE_TEMPLET WHERE APPCODE  LIKE '20H9%' ));
    --页面按钮注册实体
    SELECT * FROM SM_APPBUTNREGISTER WHERE PAGECODE LIKE '20H9%' ;
    --导入导出
    SELECT * FROM EXCEL_OUTPUTPROCESS  WHERE  MODULE ='TOACCFLOW' AND BILLTYPE LIKE '%20H9%';
    SELECT * FROM EXCEL_BILLPROCESS  WHERE   MODULE ='TOACCFLOW' AND BILLTYPE LIKE '%20H9%';
    SELECT * FROM EXCEL_TRANSLATOR WHERE MODULE ='toaccflow' AND TRACLASSNAME LIKE '%ztfilp%' ORDER BY ts DESC ;
    --参照信息
    SELECT * FROM BD_REFINFO  WHERE MODULENAME  = 'toaccflow' AND code like ('%20H9%');
    --输出模板
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
    --应用菜单项注册
    DELETE FROM SM_APPMENUITEM WHERE MENUITEMCODE LIKE '%20H9%' AND PK_MENU = '1004Z510000000000FFL' ORDER BY ts DESC;
    DELETE FROM sm_createcorp WHERE FUNCCODE ='20H4'
    --模块信息
    DELETE FROM DAP_DAPSYSTEM WHERE Moduleid IN ('20H9');
    --元数据模块实体
    DELETE FROM md_module WHERE id = 'taxm'
    --应用注册
    DELETE FROM SM_APPREGISTER WHERE  parent_id IN ('20H9') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H9'));
    --应用页面
    DELETE FROM SM_APPPAGE WHERE  PARENTCODE LIKE '20H9%';
    --页面模板
    DELETE FROM PUB_PAGE_TEMPLET WHERE APPCODE  LIKE '20H9%';
    --区域
    DELETE FROM pub_area WHERE TEMPLETID IN (SELECT PK_PAGE_TEMPLET FROM PUB_PAGE_TEMPLET WHERE APPCODE  LIKE '20H9%' );
    --查询区字段
    DELETE FROM PUB_QUERY_PROPERTY WHERE AREAID IN (SELECT PK_AREA FROM pub_area WHERE TEMPLETID IN (SELECT PK_PAGE_TEMPLET FROM PUB_PAGE_TEMPLET WHERE APPCODE  LIKE '20H9%' ));
    --表单字段属性
    DELETE FROM PUB_FORM_PROPERTY WHERE AREAID IN (SELECT PK_AREA FROM pub_area WHERE TEMPLETID IN (SELECT PK_PAGE_TEMPLET FROM PUB_PAGE_TEMPLET WHERE APPCODE  LIKE '20H9%' ));
    --页面按钮注册实体
    DELETE FROM SM_APPBUTNREGISTER WHERE PAGECODE LIKE '20H9%' ;
    --导入导出
    DELETE FROM EXCEL_OUTPUTPROCESS  WHERE  MODULE ='TOACCFLOW' AND BILLTYPE LIKE '%20H9%';
    DELETE FROM EXCEL_BILLPROCESS  WHERE   MODULE ='TOACCFLOW' AND BILLTYPE LIKE '%20H9%';
    DELETE FROM EXCEL_TRANSLATOR WHERE MODULE ='toaccflow' AND TRACLASSNAME LIKE '%ztfilp%' ORDER BY ts DESC ;
    --参照信息
    DELETE FROM BD_REFINFO  WHERE MODULENAME  = 'toaccflow' AND code like ('%20H9%');
    --输出模板
    DELETE FROM  PUB_PRINT_TEMPLATE  WHERE APPCODE IN ( SELECT CODE FROM SM_APPREGISTER WHERE  parent_id IN ('20H9') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H9')));

    DELETE FROM   pub_print_datasource WHERE ctemplateid IN (SELECT ctemplateid FROM  PUB_PRINT_TEMPLATE  WHERE APPCODE IN (
    SELECT CODE FROM SM_APPREGISTER WHERE  parent_id IN ('20H9') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H9'))
    )
    );

    DELETE FROM PUB_PRINT_CELL WHERE ctemplateid IN (SELECT ctemplateid FROM  PUB_PRINT_TEMPLATE  WHERE APPCODE IN (
    SELECT CODE FROM SM_APPREGISTER WHERE  parent_id IN ('20H9') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H9'))
    )
    );

    DELETE FROM pub_print_variable WHERE ctemplateid IN (SELECT ctemplateid FROM  PUB_PRINT_TEMPLATE  WHERE APPCODE IN (
    SELECT CODE FROM SM_APPREGISTER WHERE  parent_id IN ('20H2') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H9'))
    )
    );

    DELETE FROM  pub_print_region WHERE ctemplateid IN (SELECT ctemplateid FROM  PUB_PRINT_TEMPLATE  WHERE APPCODE IN (
    SELECT CODE FROM SM_APPREGISTER WHERE  parent_id IN ('20H9') OR parent_id IN (SELECT PK_APPREGISTER  FROM SM_APPREGISTER WHERE parent_id IN ('20H9'))
    )
    );
    ```

3. 上传补丁
4. 在数据库中创建相关表

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
select a.*,(SNO||SNAME) from TEST_STUDENT a;
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

## 档案唯一性校验及数据隔离（集团管控）

### 元数据
实体和组件的扩展标签：BDMODE,URC,DOC
需要进行唯一性校验的字段的扩展标签：URC
业务接口属性映射：pk_org -> 组织，pk_group -> 集团

### ServiceImpl.java
```java
import nc.itf.toaccflow.ztrans_rc_sub4.ztransrcsub4vo.IZtransRcSub4VOService;
import nc.bs.framework.common.NCLocator;

public String[] listPit_inputPkByCond(String condition, String[] orderPath) throws BusinessException {
    // 查询条件拼接集团信息 Added by Jiahui Li
    String pk_group = InvocationInfoProxy.getInstance().getGroupId();
    sql.append(" where ").append(condition).append(" and pk_group = '" + pk_group + "' ");
}

public void initDefaultData(Pit_input vo) {
    // Added by Jiahui Li
    if(vo.getAttributeValue("pk_group") == null){
        vo.setAttributeValue("pk_group",InvocationInfoProxy.getInstance().getGroupId());
    }
}

private void setAuditInfo(Pit_input... vos) throws BusinessException {
    if (ArrayUtils.isNotEmpty(vos)) {
        // 通过集团获取客户端接口 Added by Jiahui Li
		IZtransRcSub4VOService service = NCLocator.getInstance().lookup(IZtransRcSub4VOService.class);
		Map<String, String> pk_groupToMandt = new HashMap<String, String>();// Map<集团主键，客户端>

		UFDateTime now = new UFDateTime();
		for (Pit_input vo : vos) {
			String pk = getVOPrimaryKey(vo);
			if (StringUtils.isEmpty(pk)) {
				// 设置创建人创建时间
				getMainVO(vo).setAttributeValue("creator", InvocationInfoProxy.getInstance().getUserId());
				getMainVO(vo).setAttributeValue("creationtime", now);
				getMainVO(vo).setAttributeValue("", now);
				getMainVO(vo).setAttributeValue("modifier", null);
				getMainVO(vo).setAttributeValue("modifiedtime", null);
			} else {
				// 设置修改人修改时间
				getMainVO(vo).setAttributeValue("modifier", InvocationInfoProxy.getInstance().getUserId());
				getMainVO(vo).setAttributeValue("modifiedtime", now);
				getMainVO(vo).setAttributeValue("", now);
			}

			// 补充集团信息、客户端信息 added by Jiahui Li
			String pk_group = InvocationInfoProxy.getInstance().getGroupId();
			if (vo.getAttributeValue("pk_group") == null) {
				getMainVO(vo).setAttributeValue("pk_group", pk_group);
			}
			getMainVO(vo).setAttributeValue("pk_org", pk_group);
			getMainVO(vo).setAttributeValue("pk_org_v", pk_group);
			// 补充集团后，如果集团还是空的，需要提示
			if (vo.getAttributeValue("pk_group") == null) {
				ExceptionUtils.wrapBusinessException("未获取到集团！");
			}
			// 补充客户端的值
			if (vo.getAttributeValue("mandt") == null) {
				String mandt = pk_groupToMandt.get(pk_group);
				if (mandt == null) {
					mandt = service.queryGroupCode(pk_group);
					pk_groupToMandt.put(pk_group, mandt);
				}
				getMainVO(vo).setAttributeValue("mandt", mandt);
			}
			// 补充客户端后，如果客户端还是空的，需要给出提示
			if (vo.getAttributeValue("mandt") == null) {
				ExceptionUtils.wrapBusinessException("未获取到客户端！");
			}
		}
	}
}	

```

## 导入导出
### 导入字段修改
1. 导入导出xml文件中相关字段下添加翻译器ID
```xml
<translator>1001ZZ1LCP000005XP1Y</translator>
```
```sql
SELECT PK_TRANSLATOR FROM EXCEL_TRANSLATOR WHERE MODULE ='taxm' ORDER BY ts DESC ;
```
2. 修改后端导入导出自定义翻译类代码
``` java
public class AggPit_tax_ledger_hTranslator extends AbstractRefTranslator {

	private Logger logger = LoggerFactory.getLogger(AggTaxRateConfigVOTranslator.class);

	@SuppressWarnings("unchecked")
	@Override
	public String translateExToNC(String srcValue, String metaDataID, ITranslateContext translateContext)
			throws PfxxException {
        //TODO
		// if (translateContext.getProperty().getLabel().equals("县支公司")) {
		// 	String countryBranchSql = "SELECT cccode, pk_costcenter FROM resa_costcenter";
		// 	String val = "";
		// 	try {
		// 		Map<String, String> countryBranchMap = (Map<String, String>) new BaseDAO()
		// 				.executeQuery(countryBranchSql, new KeyValueMapProcessor<String, String>());
		// 		val = countryBranchMap.get(srcValue);
		// 	} catch (Exception e) {
		// 		logger.error(e.getMessage());
		// 		return null;
		// 	}
		// 	return val;
		// }

		return null;
	}
}
```

### 导出字段修改
``` java
@SuppressWarnings("unchecked")
@Override
protected ExportDataInfo getValue(Object[] vos, List<InputItem> exportItems, BillDefination billdefination)
		throws BusinessException {
	ExtendedAggregatedValueObject[] aggvos = getConvertorForTemp(new DefRefPropertyProcess())
			.convertDataFromEditorData(billdefination, vos, exportItems);

    //TODO
	// String strMapSql = "SELECT riskcode, RISKCODE_YW FROM "
	// 		+ "( SELECT RISKCODE_YW, RISKCODE_NAME, RISKCODE, PK_ZTRANS_RCCONVERT, row_number() over(PARTITION BY RISKCODE_YW,RISKCODE_NAME,RISKCODE ORDER BY RISKCODE_YW) AS rk FROM "
	// 		+ "( SELECT trim(RISKCODE_YW) RISKCODE_YW, trim(RISKCODE_NAME) RISKCODE_NAME, trim(RISKCODE) RISKCODE, trim(PK_ZTRANS_RCCONVERT) PK_ZTRANS_RCCONVERT FROM ztrans_rcconvert UNION SELECT trim(RISKCODE_YW) RISKCODE_YW, '_', trim(RISKCODE) RISKCODE, PK_ZCIS_RCCONVERT  FROM zcis_rcconvert ) ) WHERE rk =1";

	// Map<String, String> ywCodeMap = (Map<String, String>) new BaseDAO().executeQuery(strMapSql.toString(),
	// 		new KeyValueMapProcessor<String, String>());
    // try {
    //     for (ExtendedAggregatedValueObject aggvo : aggvos) {
    //         aggvo.getParentVO().setAttributeValue("pk_taxcode", ywCodeMap.get(aggvo.getParentVO().getAttributeValue("pk_taxcode")));
    //     }
    // } catch (Exception e) {
    //     Logger.error(e);
    // }

	return new ExportDataInfo(aggvos);
}
```

### 导入字段赋值
``` java
@Override
public AggTaxRateConfigVO[] saveAggTaxRateConfigVO(AggTaxRateConfigVO vo) throws BusinessException {
	//TODO
	// vo.getParentVO().setPk_type1name(type1Name.get(vo.getParentVO().getPk_type1()));
		
	if (StringUtils.isEmpty(pk)) {
		return dao.insert(vo); // 插入
	} else {
		return dao.update(vo); // 更新
	}
}
```


## 模板不存在或已被删除
``` sql
SELECT * FROM PUB_PAGE_TEMPLET WHERE CODE ='18203008A_ReportZtransSubject'
UPDATE PUB_TEMPLATE_ASSIGNMENT SET PK_PAGE_TEMPLET = '1001ZZ1LCP00002V54UE' WHERE PAGE_CODE = '18203008A_ReportZtransSubject'
SELECT * FROM PUB_TEMPLATE_ASSIGNMENT where PAGE_CODE = '18203008A_ReportZtransSubject' order BY TS ASC
```