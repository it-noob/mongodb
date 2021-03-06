# 迁移MongoDB实例至其他地域 {#concept_fly_23g_jhb .concept}

本文介绍如何使用数据传输服务DTS（Data Transmission Service），将MongoDB单节点实例或副本集实例迁移至其他地域。DTS支持全量数据迁移和增量数据迁移，同时使用这两种迁移类型可以实现在不停服的情况下，平滑地完成数据库的迁移。

## 场景介绍 {#section_a4y_xm4_vxl .section}

在某些业务场景下，可能需要更换MongoDB实例的地域，例如：

-   业务结构调整。
-   在ECS实例上部署了相关应用程序，需要使用MongoDB实例提供数据库服务，但是ECS实例与MongoDB实例不在同一地域。

本文以MongoDB实例从华北1迁移至华东1为例，介绍具体的操作流程。

**说明：** 该操作仅迁移源实例的数据，源实例在迁移完成后如不再需要可执行释放操作。

![迁移MongoDB实例至其他地域](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/155400/156775246843672_zh-CN.png)

## 前提条件 {#section_tql_qpw_pgb .section}

-   源实例类型为单节点实例或副本集实例。如果源实例为分片集群实例，请使用MongoDB自带的工具进行迁移，步骤与[使用MongoDB工具迁移自建数据库上云](../intl.zh-CN/分片集群快速入门/数据迁移/使用MongoDB工具迁移自建数据库上云.md#)类似。

    **说明：** 使用DTS迁移单节点实例时，不支持增量数据迁移，详情请参见[迁移类型说明](#section_lml_bh7_52y)。

-   已在目标地域中创建了目标实例，详情请参见[创建单节点实例](../intl.zh-CN/单节点快速入门/创建单节点实例.md#)、[创建副本集实例](../intl.zh-CN/副本集快速入门/创建副本集实例.md#)或[创建分片集群实例](../intl.zh-CN/分片集群快速入门/创建分片集群实例.md#)。
-   目标实例的存储空间需大于源实例的已使用存储空间。

## 注意事项 {#section_a3z_x11_kfb .section}

-   为避免影响您的业务使用，请在业务低峰期进行数据迁移。
-   在迁移单节点实例时，为保障数据一致性，在全量数据迁移期间，请勿在源实例中写入新的数据。
-   不支持迁移admin数据库，即使您将admin数据库选择为迁移对象，该库中的数据也不会被迁移。
-   MongoDB实例支持的版本与存储引擎请参见[版本及存储引擎](../intl.zh-CN/产品简介/版本及存储引擎.md#)，如需跨版本或跨引擎迁移，请提前确认兼容性。

## 费用说明 {#section_ozu_kps_cj8 .section}

|迁移类型|链路配置费用|公网流量费用|
|:---|:-----|:-----|
|全量数据迁移|不收取|不收取|
|增量数据迁移|收取，费用详情请参见[数据传输服务DTS定价](https://www.alibabacloud.com/zh/product/data-transmission-service/pricing)。|不收取|

## 迁移类型说明 {#section_lml_bh7_52y .section}

-   全量数据迁移：将源MongoDB数据库迁移对象的存量数据全部迁移到目标MongoDB数据库中。

    **说明：** 支持database、collection、index的迁移。

-   增量数据迁移：在全量迁移的基础上，将源MongoDB数据库的增量更新数据同步到目标MongoDB数据库中。

    **说明：** 

    -   支持database、collection、index的新建和删除操作的同步。
    -   支持document的新增、删除和更新操作的同步。

## 数据库账号的权限要求 {#section_ou2_77n_w0z .section}

|迁移数据源|全量数据迁移|增量数据迁移|
|:----|:-----|------|
|源MongoDB实例|待迁移库的read权限|待迁移库、admin库和local库的read权限|
|目标MongoDB实例|目标库的readWrite权限|目标库的readWrite权限|

**说明：** 数据库账号创建及授权方法请参见[使用DMS管理MongoDB数据库用户](intl.zh-CN/用户指南/账号管理/使用DMS管理MongoDB数据库用户.md#)

## 操作步骤 {#section_njl_2c1_kfb .section}

1.  登录[数据传输控制台](https://dts-intl.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据迁移**。
3.  单击页面右上角的**创建迁移任务**。
4.  配置迁移任务的源库及目标库信息。

    ![MongoDB源目库配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/155400/156775246843677_zh-CN.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源库信息|实例类型|选择**云数据库MongoDB**。|
    |实例地区|选择源MongoDB实例所属地域，本案例选择为**华北1（青岛）**。|
    |MongoDB实例ID|选择源MongoDB实例ID。|
    |数据库名称|填入鉴权数据库名称。|
    |数据库账号|填入连接源MongoDB实例的数据库账号，权限要求请参见[数据库账号的权限要求](#section_ou2_77n_w0z)。|
    |数据库密码|填入连接源MongoDB实例的数据库账号对应的密码。 **说明：** 源库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的源库信息是否正确。源库信息填写正确则提示**测试通过**，如提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的源库信息。

 |
    |目标库信息|实例类型|选择**云数据库MongoDB**。|
    |实例地区|选择目标MongoDB实例所属地域，本案例选择为**华东1（杭州）**。|
    |MongoDB实例ID|选择目标MongoDB实例ID。|
    |数据库名称|填入鉴权数据库名称。|
    |数据库账号|填入连接目标MongoDB实例的数据库账号，权限要求请参见[数据库账号的权限要求](#section_ou2_77n_w0z)。|
    |数据库密码|填入连接目标MongoDB实例的数据库账号对应的密码。 **说明：** 目标库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的目标库信息是否正确。目标库信息填写正确则提示**测试通过**，如提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的目标库信息。

 |

5.  单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到源和目标MongoDB实例的白名单中，用于保障DTS服务器能够正常连接源和目标MongoDB实例。迁移完成后如不再需要可将DTS服务器的IP地址从白名单中移出，详情请参见[白名单设置](intl.zh-CN/副本集快速入门/设置白名单.md#)。

6.  选择迁移对象及迁移类型。

    ![配置数据迁移对象和迁移类型](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/75938/156775246833662_zh-CN.png)

    |配置|说明|
    |:-|:-|
    |迁移类型|     -   如果只需要进行全量迁移，则勾选**全量数据迁移**。

**说明：** 为保障数据一致性，全量数据迁移期间请勿在源实例中写入新的数据。

    -   如果需要进行不停机迁移，则同时勾选**全量数据迁移**和**增量数据迁移**。

**说明：** 使用DTS迁移单节点实例时，不支持增量数据迁移。

 |
    |迁移对象|     -   在**迁移对象**框中单击待迁移的对象，然后单击![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/83046/156775246837966_zh-CN.png)移动到**已选择对象**框。

**说明：** 不支持迁移admin数据库，即使您将admin数据库选择为迁移对象，该库中的数据也不会被迁移。

    -   迁移对象选择的粒度为database、collection/function。
    -   默认情况下，迁移完成后，迁移对象的名称保持不变。如果您需要迁移对象在目标数据库中的名称不同，那么需要使用DTS提供的对象名映射功能。使用方法请参见[库表列映射](https://www.alibabacloud.com/help/zh/doc-detail/26628.htm)。
 |

7.  上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在迁移任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动迁移任务。
    -   如果预检查失败，单击具体检查项后的![提示](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/140110/156775246950068_zh-CN.png)，查看失败详情。根据失败原因修复后，重新进行预检查。
8.  预检查通过后，单击**下一步**。
9.  在**购买配置确认**页面，选择**链路规格**并勾选**数据传输（按量付费）服务条款**。
10. 单击**购买并启动**，迁移任务正式开始。
    -   全量数据迁移

        请勿手动结束迁移任务，否则可能会导致数据不完整。您只需等待迁移任务完成即可，迁移任务会自动结束。

    -   增量数据迁移

        迁移任务不会自动结束，需要手动结束迁移任务。

        **说明：** 请选择合适的时间手动结束迁移任务，例如业务低峰期或准备将业务切换至MongoDB实例时。

        1.  观察迁移任务的进度变更为**增量迁移**，并显示为**无延迟**状态时，将源库停写几分钟，此时**增量迁移**的状态可能会显示延迟的时间。
        2.  等待迁移任务的**增量迁移**再次进入**无延迟**状态，手动结束迁移任务。

            ![增量迁移无延迟](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/75938/156775246933674_zh-CN.png)

11. 将业务切换至目标MongoDB实例中。
12. 根据业务需求确认是否需要释放源实例，如果无需释放可跳过本步骤。
    -   源实例付费类型为按量付费时，请参见[释放实例](intl.zh-CN/用户指南/实例管理/释放实例.md#)。
    -   源实例付费类型为包年包月时，不支持释放。

