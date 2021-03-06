# 云数据库MongoDB版数据安全最佳实践 {#concept_491217 .concept}

针对用户重点关注的数据安全，云数据库MongoDB版提供了全面的安全保障。您可以通过同城容灾、RAM授权、审计日志、网络隔离、白名单、密码认证等多手段保障数据库数据安全。

## 同城容灾 {#section_got_1dj_jkl .section}

为进一步满足业务场景中高可靠性和数据安全需求，云数据库MongoDB版提供了同城容灾解决方案。该方案将副本集中的节点或分片集群实例中的组件分别部署在同一地域下三个不同的[可用区](https://help.aliyun.com/document_detail/26559.html#ul-icc-njg-hfb)，当三个可用区中的任一可用区因电力、网络等不可抗因素失去通信时，高可用系统将自动触发切换操作，确保整个实例的持续可用和数据安全。

您可以在创建实例时选择多可用区，详情请参见[创建多可用区副本集实例](../../../../cn.zh-CN/用户指南/同城容灾解决方案/创建多可用区副本集实例.md#)或[创建多可用区分片集群实例](../../../../cn.zh-CN/用户指南/同城容灾解决方案/创建多可用区分片集群实例.md#)；您也可以将现有的副本集实例从单可用区迁移至多可用区，详情请参见[迁移可用区](../../../../cn.zh-CN/用户指南/实例管理/迁移可用区.md#)。

## 权限控制 {#section_rrz_1uz_y7u .section}

-   授权RAM用户管理MongoDB实例

    使用访问控制RAM（Resource Access Management），您可以创建、管理子账号，控制子账号对您名下资源的操作权限。当您的企业存在多用户协同操作资源时，使用RAM可以按需为用户分配最小权限，避免与其他用户共享云账号密钥，降低企业的信息安全风险。

    具体操作方法请参见[云数据库MongoDB版如何配置RAM授权](../../../../cn.zh-CN/常见问题/账号__权限管理/云数据库MongoDB版如何配置RAM用户（子账号）授权.md#)。

-   创建数据库用户并授权

    请勿在生产环境中直接使用root用户连接数据库，您可以根据业务需求，创建数据库用户并分配权限。

    具体操作方法请参见[使用DMS管理MongoDB数据库用户](https://help.aliyun.com/document_detail/99142.html#concept-cgg-qxh-1gb)。


## 网络隔离 {#section_47l_isj_fhp .section}

-   使用专有网络

    云数据库MongoDB版支持多种网络类型，推荐使用**专有网络**。

    专有网络是一种隔离的网络环境，安全性和性能均高于传统的经典网络。专有网络需要事先创建，详情请参见[创建专有网络](https://help.aliyun.com/document_detail/65402.html)。

    当MongoDB实例为经典网络时，您可以将实例的网络类型切换至专有网络，详情请参见[切换实例网络类型](../../../../cn.zh-CN/用户指南/管理网络连接/切换实例网络类型.md#)。如果您的MongoDB实例已经是专有网络，则无需配置。

    **说明：** 云数据库MongoDB版支持在专有网络环境下开启免密访问，在保障高安全性的前提下提供更便捷的数据库连接方式，详情请参见[开启/关闭内网免密访问](../../../../cn.zh-CN/用户指南/管理网络连接/开启__关闭内网免密访问.md#)。

-   合理设置白名单

    MongoDB实例创建完毕后，默认情况下实例的白名单中IP地址为`127.0.0.1`，您必须手动设置白名单地址后才可以连接MongoDB数据库。

    具体操作方法请参见[设置白名单](../../../../cn.zh-CN/用户指南/数据安全性/设置白名单.md#)。

    **说明：** 

    -   请勿将白名单地址配置为`0.0.0.0/0`，允许所有IP地址访问。
    -   建议按需设置并定期维护白名单，及时删除不再需要的IP地址。

## 日志审计 {#section_n9a_s4e_rpi .section}

云数据库MongoDB版审计日志记录了您对数据库执行的所有操作。通过审计日志，您可以对数据库进行故障分析、行为分析、安全审计等操作，有效帮助您获取数据的执行情况。

具体操作方法请参见[审计日志](../../../../cn.zh-CN/用户指南/数据安全性/审计日志.md#)。

## 数据加密 {#section_na8_qxk_xey .section}

-   SSL链路加密

    通过公网连接数据库时，您可以启用SSL（Secure Sockets Layer）加密功能提高数据链路的安全性。通过SSL加密功能可以在传输层对网络连接进行加密，在提升通信数据安全性的同时，保障数据的完整性，详情请参见[使用Mongo Shell通过SSL加密连接数据库](../../../../cn.zh-CN/用户指南/数据安全性/使用Mongo Shell通过SSL加密连接数据库.md#)。

-   透明数据加密TDE

    透明数据加密TDE（Transparent Data Encryption）可对数据文件执行实时I/O加密和解密，数据在写入磁盘之前进行加密，从磁盘读入内存时进行解密。TDE不会增加数据文件的大小，您无需更改任何应用程序，即可使用TDE功能，详情请参见[设置透明数据加密TDE](../../../../cn.zh-CN/用户指南/数据安全性/设置透明数据加密TDE.md#)。


