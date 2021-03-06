# 从MongoDB副本集实例迁移至分片集群实例 {#concept_fdj_twz_zfb .concept}

本文介绍如何使用数据传输服务DTS（Data Transmission Service），将MongoDB副本集实例迁移至分片集群实例。DTS支持全量数据迁移和增量数据迁移，同时使用这两种迁移类型可以实现在不停服的情况下，平滑地完成数据库的迁移。

## 前提条件 {#section_hy5_sc1_4gb .section}

目标实例中每个Shard节点的存储空间均大于源实例的存储空间。

## 注意事项 {#section_dww_lmr_zfb .section}

-   为避免影响您的业务使用，请在业务低峰期进行数据迁移。
-   不支持迁移admin数据库，即使您将admin数据库选择为迁移对象，该库中的数据也不会被迁移。
-   MongoDB实例支持的版本与存储引擎请参见[版本及存储引擎](../cn.zh-CN/产品简介/版本及存储引擎.md#)，如需跨版本或跨引擎迁移，请提前确认兼容性。

## 费用说明 {#section_m3c_t5f_cgb .section}

|迁移类型|链路配置费用|公网流量费用|
|:---|:-----|:-----|
|全量数据迁移|不收取|不收取|
|增量数据迁移|收取，费用详情请参见[数据传输服务DTS定价](https://cn.aliyun.com/price/product#/dts/detail)。|不收取|

## 迁移类型说明 {#section_vax_ah2_vha .section}

-   全量数据迁移：将源MongoDB数据库迁移对象的存量数据全部迁移到目标MongoDB数据库中。

    **说明：** 支持database、collection、index的迁移。

-   增量数据迁移：在全量迁移的基础上，将源MongoDB数据库的增量更新数据同步到目标MongoDB数据库中。

    **说明：** 

    -   支持database、collection、index的新建和删除操作的同步。
    -   支持document的新增、删除和更新操作的同步。

## 数据库账号的权限要求 {#section_kvw_kb1_kfb .section}

|迁移数据源|全量数据迁移|增量数据迁移|
|:----|:-----|------|
|自建MongoDB数据库|待迁移库的read权限|待迁移库、admin库和local库的read权限|
|阿里云MongoDB数据库|目标库的readWrite权限|目标库的readWrite权限|

**说明：** 数据库账号创建及授权方法请参见[使用DMS管理MongoDB数据库用户](cn.zh-CN/用户指南/账号管理/使用DMS管理MongoDB数据库用户.md#)。

## 迁移前准备工作 {#section_pcx_yhs_zfb .section}

根据业务需要，在目标MongoDB实例中创建需要分片的数据库和集合，并配置数据分片，详情请参见[设置数据分片以充分利用Shard性能](../cn.zh-CN/最佳实践/设置数据分片以充分利用Shard性能.md#)。

**说明：** 配置数据分片可避免数据被迁移至同一Shard，导致无法发挥集群性能。

## 操作步骤 {#section_lnt_knr_zfb .section}

1.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据迁移**。
3.  在迁移任务列表页面顶部，选择目标MongoDB实例所属地域。

    ![选择地域](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6682/156879125550190_zh-CN.png)

4.  单击右上角的**创建迁移任务**。
5.  配置迁移任务的源库及目标库信息。

    ![配置迁移源库和目标库信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/75938/156879125533658_zh-CN.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源库信息|实例类型|选择**云数据库MongoDB**。|
    |实例地区|选择源MongoDB实例所在地域。|
    |MongoDB实例ID|选择源MongoDB实例ID。|
    |数据库名称|填入鉴权数据库名称。|
    |数据库账号|填入连接源MongoDB实例的数据库账号，权限要求请参见[数据库账号的权限要求](#section_kvw_kb1_kfb)。|
    |数据库密码|填入连接源MongoDB实例的数据库账号对应的密码。 **说明：** 源库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的源库信息是否正确。源库信息填写正确则提示**测试通过**，如提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的源库信息。

 |
    |目标库信息|实例类型|选择**云数据库MongoDB**。|
    |实例地区|选择目标MongoDB实例所在地域。|
    |MongoDB实例ID|选择目标MongoDB实例ID。|
    |数据库名称|填入鉴权数据库名称。|
    |数据库账号|填入连接目标MongoDB实例的数据库账号，权限要求请参见[数据库账号的权限要求](#section_kvw_kb1_kfb)。|
    |数据库密码|填入连接目标MongoDB实例的数据库账号对应的密码。 **说明：** 目标库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的目标库信息是否正确。目标库信息填写正确则提示**测试通过**，如提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的目标库信息。

 |

6.  配置完成后，单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到目标MongoDB实例的白名单中，用于保障DTS服务器能够正常连接目标MongoDB实例。迁移完成后如不再需要可手动删除，详情请参见[设置白名单](../cn.zh-CN/分片集群快速入门/设置白名单.md#)。

7.  选择迁移对象及迁移类型。

    ![配置数据迁移对象和迁移类型](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/75938/156879125633662_zh-CN.png)

    |配置|说明|
    |:-|:-|
    |迁移类型|     -   如果只需要进行全量迁移，则勾选**全量数据迁移**。

**说明：** 为保障数据一致性，全量数据迁移期间请勿在自建MongoDB数据库中写入新的数据。

    -   如果需要进行不停机迁移，则同时勾选**全量数据迁移**和**增量数据迁移**。
 |
    |迁移对象|     -   在**迁移对象**框中单击待迁移对象，然后单击![向右箭头](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156879125640698_zh-CN.png)将其移动至**已选择对象**框。

**说明：** 不支持迁移admin数据库，即使您将admin数据库选择为迁移对象，该库中的数据也不会被迁移。

    -   迁移对象选择的粒度为database、collection/function。
    -   默认情况下，迁移完成后，迁移对象的名称保持不变。如果您需要迁移对象在目标数据库中的名称不同，那么需要使用DTS提供的对象名映射功能。使用方法请参见[库表列映射](https://help.aliyun.com/document_detail/26628.html)。
 |

8.  上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在迁移任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动迁移任务。
    -   如果预检查失败，单击具体检查项后的![提示](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/140110/156879125650068_zh-CN.png)，查看失败详情。根据提示修复后，重新进行预检查。
9.  预检查通过后，单击**下一步**。
10. 在**购买配置确认**页面，选择**链路规格**并勾选**数据传输（按量付费）服务条款**。
11. 单击**购买并启动**，迁移任务正式开始。
    -   全量数据迁移

        请勿手动结束迁移任务，否则可能会导致数据不完整。您只需等待迁移任务完成即可，迁移任务会自动结束。

    -   增量数据迁移

        迁移任务不会自动结束，需要手动结束迁移任务。

        **说明：** 请选择合适的时间手动结束迁移任务，例如业务低峰期或准备将业务切换至MongoDB实例时。

        1.  观察迁移任务的进度变更为**增量迁移**，并显示为**无延迟**状态时，将源库停写几分钟，此时**增量迁移**的状态可能会显示延迟的时间。
        2.  等待迁移任务的**增量迁移**再次进入**无延迟**状态，手动结束迁移任务。

            ![增量迁移无延迟](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/75938/156879125633674_zh-CN.png)

12. 将业务切换至MongoDB实例。

## 更多信息 {#section_kkd_rc1_4gb .section}

 [分片集群实例连接说明](../cn.zh-CN/分片集群快速入门/连接实例/分片集群实例连接说明.md#)

