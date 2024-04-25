# WPS文档中心V7-lite服务实例部署文档

## 概述

金山软件文档中心lite服务（自动创建RDS数据库与OSS对象存储）,本产品用于在线管理云文档，支持多人在线文档预览编辑。

支持在计算巢平台上一键基于WPS免维护版本服务创建可直接投产的整套环境（包括rds和oss）。


## 计费说明

WPS服务在计算巢上的费用主要涉及：

- 所选vCPU与内存规格
- 系统盘类型及容量
- 公网带宽

计费方式包括：

- 按量付费（小时）
- 包年包月

目前支持8核32G及以上规格的实例（请根据实际并发用户数需要提高规格），例如：

| 规格族 | vCPU与内存 | 系统盘 | 公网带宽 |
| --- | --- | --- | --- |
| ecs.g6.2xlarge | 通用型 g6，8vCPU 32GiB | ESSD云盘 200GiB及以上系统盘 250GiB及以上数据盘 | 建议带宽10Mbps及以上 |
| ecs.g6.4xlarge | 通用型 g6，16vCPU 64GiB | ESSD云盘 200GiB及以上系统盘 500GiB及以上数据盘 | 建议带宽10Mbps及以上 |

预估费用在创建实例时可实时看到。



## 部署架构

本方案目前仅需一台ECS主机提供服务，如需更多规格、其他服务（如集群高可用性要求、企业级支持服务等），请联系我们 [mailto:https://www.wps.cn](mailto:https://www.wps.cn)。

## RAM账号所需权限(推荐直接使用管理员账号创建，无需设置权限)


WPS服务需要对ECS、VPC、RDS、OSS等资源进行访问和创建操作，若您使用RAM用户创建服务实例，需要在创建服务实例前，对使用的RAM用户的账号添加相应资源的权限。添加RAM权限的详细操作，请参见[为RAM用户授权](https://help.aliyun.com/document_detail/121945.html)。所需权限如下表所示。

### 1.RAM账户操作计算巢的权限
```
AliyunComputeNestUserFullAccess
AliyunComputeNestSupplierFullAccess
AliyunRAMReadOnlyAccess
AliyunVPCReadOnlyAccess
AliyunECSReadOnlyAccess
AliyunROSFullAccess
AliyunQuotasReadOnlyAccess
```

### 2.RAM账户操作资源的权限（建议添加自定义策略）
```
{
    "Statement": [
        {
            "Action": [
                "ecs:AddTags",
                "ecs:AllocatePublicIpAddress",
                "ecs:AttachKeyPair",
                "ecs:AuthorizeSecurityGroup",
                "ecs:AuthorizeSecurityGroupEgress",
                "ecs:ConfigureSecurityGroupPermissions",
                "ecs:CreateSecurityGroup",
                "ecs:DeleteInstance",
                "ecs:DeleteSecurityGroup",
                "ecs:DescribeAvailableResource",
                "ecs:DescribeCloudAssistantStatus",
                "ecs:DescribeDedicatedHosts",
                "ecs:DescribeDisks",
                "ecs:DescribeImageSupportInstanceTypes",
                "ecs:DescribeImages",
                "ecs:DescribeInstanceAutoRenewAttribute",
                "ecs:DescribeInstanceRamRole",
                "ecs:DescribeInstances",
                "ecs:DescribeInvocationResults",
                "ecs:DescribeInvocations",
                "ecs:DescribeKeyPairs",
                "ecs:DescribeManagedInstances",
                "ecs:DescribeNetworkInterfaces",
                "ecs:DescribePrice",
                "ecs:DescribeSecurityGroupAttribute",
                "ecs:DescribeSecurityGroups",
                "ecs:DescribeSnapshots",
                "ecs:DescribeUserData",
                "ecs:DetachKeyPair",
                "ecs:JoinResourceGroup",
                "ecs:ModifyDiskSpec",
                "ecs:ModifyInstanceAttribute",
                "ecs:ModifySecurityGroupEgressRule",
                "ecs:ModifySecurityGroupRule",
                "ecs:RemoveTags",
                "ecs:ReplaceSystemDisk",
                "ecs:ResizeDisk",
                "ecs:RunCommand",
                "ecs:RunInstances",
                "ecs:StartInstance",
                "ecs:StopInstance",
                "ecs:StopInvocation",
                "ecs:TagResources",
                "ecs:UntagResources",
                "ecs:ModifyDiskAttribute",
                "ecs:CreateSnapshot",
                "ecs:CreateImage",
                "ecs:DeleteSnapshot",
                "ecs:DeleteImage",
                "ecs:AttachInstanceRAMRole",
                "vpc:AssociateVpcCidrBlock",
                "vpc:CreateVSwitch",
                "vpc:CreateVpc",
                "vpc:DeleteVSwitch",
                "vpc:DeleteVpc",
                "vpc:DescribeVSwitches",
                "vpc:DescribeVpcs",
                "vpc:DescribeVpnGateways",
                "vpc:DescribeZones",
                "vpc:ModifyVSwitchAttribute",
                "vpc:ModifyVpcAttribute",
                "vpc:TagResources",
                "vpc:UnTagResources",
                "rds:DescribeDBInstances",
                "slb:DescribeLoadBalancers",
                "rds:AllocateInstancePublicConnection",
                "rds:CreateAccount",
                "rds:CreateDBInstance",
                "rds:CreateDatabase",
                "rds:DeleteAccount",
                "rds:DeleteDBInstance",
                "rds:DescribeAccounts",
                "rds:DescribeAvailableInstanceClass",
                "rds:DescribeAvailableResource",
                "rds:DescribeBackupPolicy",
                "rds:DescribeClassList",
                "rds:DescribeDBInstanceAttribute",
                "rds:DescribeDBInstanceIPArrayList",
                "rds:DescribeDBInstanceNetInfo",
                "rds:DescribeDBInstanceSSL",
                "rds:DescribeDBInstances",
                "rds:DescribeDatabases",
                "rds:DescribeInstanceKeywords",
                "rds:DescribeRegions",
                "rds:DescribeSQLCollectorPolicy",
                "rds:DescribeSecurityGroupConfiguration",
                "rds:DescribeTags",
                "rds:GrantAccountPrivilege",
                "rds:ListTagResources",
                "rds:ModifyBackupPolicy",
                "rds:ModifyDBInstanceConnectionString",
                "rds:ModifyDBInstanceMaintainTime",
                "rds:ModifyDBInstanceSSL",
                "rds:ModifyDBInstanceSpec",
                "rds:ModifySQLCollectorPolicy",
                "rds:ModifySecurityGroupConfiguration",
                "rds:ModifySecurityIps",
                "rds:QueryPrice",
                "rds:ResetAccountPassword",
                "rds:TagResources",
                "rds:UntagResources",
                "ram:AttachPolicyToRole",
                "ram:CreatePolicy",
                "ram:CreateRole",
                "ram:DeletePolicy",
                "ram:DeleteRole",
                "ram:DetachPolicyFromRole",
                "ram:GetPolicy",
                "ram:GetRole",
                "ram:ListPoliciesForRole",
                "ram:UpdateRole",
                "oss:DeleteBucket",
                "oss:DeleteBucketEncryption",
                "oss:DeleteBucketTagging",
                "oss:DeleteObject",
                "oss:GetBucketAcl",
                "oss:GetBucketCors",
                "oss:GetBucketInfo",
                "oss:GetBucketLifecycle",
                "oss:GetBucketLogging",
                "oss:GetBucketPolicy",
                "oss:GetBucketReferer",
                "oss:GetBucketTagging",
                "oss:GetBucketVersioning",
                "oss:GetBucketWebsite",
                "oss:ListObjects",
                "oss:PutBucket",
                "oss:PutBucketAcl",
                "oss:PutBucketCors",
                "oss:PutBucketEncryption",
                "oss:PutBucketLifecycle",
                "oss:PutBucketLogging",
                "oss:PutBucketPolicy",
                "oss:PutBucketReferer",
                "oss:PutBucketTagging",
                "oss:PutBucketVersioning",
                "oss:PutBucketWebsite",
                "oss:ListBuckets",
                "ram:CreateAccessKey",
                "ram:PassRole",
                "ram:AssumeRole"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Action": "quotas:ListProductQuotas",
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Action": "ram:GetRole",
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Action": [
                "vpc:DescribeVSwitches",
                "vpc:DescribeVpcs"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Action": [
                "ros:CreateStack",
                "ros:GetStack"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Action": "oss:*",
            "Effect": "Allow",
            "Resource": "acs:oss:*:*:wps-dc-lite-*"
        }
    ],
    "Version": "1"
}
```

## 部署流程

### 部署步骤

#### 1. 搜索查找wps免维护版计算巢服务
在我的服务清单中直接使用，上线后需要在服务市场中查找

#### 2. 配置并执行创建服务
点击【正式创建】后配置如下内容：

- 配置区域和付费模式
    - 目前主要支持阿里云国内地域。

- 配置ECS主机（请选择8c32g及以上配置）
    - 请至少选择8c32g及以上配置
    - 请保存好设置的root密码
    - 数据磁盘请尽量给大一点，后续扩容不方便

- 配置可用区以及VPC（默认自动创建）

- 配置RDS数据库

- 配置OSS信息
    - 自动创建bucket
    - 名称将为“服务实例名称-填写的后缀”，例如wps-dc-lite-upbk-bucket1）


### 验证结果
- 创建成功后查看服务状态
    - 确认部署成功后机器会自动重启继续完成初始化（重新注册RDS数据库和OSS）
    - 控制台显示服务已准备好的终端界面后初始化才成功

- 获取访问信息
    - 使用public ip通过ssh工具登录节点，root账户密码为上面设置的ECS主机信息中的密码；

- 确认结果并清理敏感信息
    - 确认服务均正常后，请删除/data/.aliyun_info.bak文件，里面记录了rds和oss信息用户初始化过程。

### 使用WPS文档中心

请访问WPS官网了解如何使用：[使用文档](https://www.wps.cn)

## 问题排查

### 1. 账户和密码以什么形式提供？
目前要获取默认账户密码，只能ssh登陆节点，执行命令打印/tmp/platforminfo文件内容获取。
```
cat /tmp/platforminfo
```
其中登录ip为阿里云提供的public ip地址，root账户密码为创建服务时配置ecs主机处输入的密码；

请及时登陆并修改默认密码：
- 请使用"v7文档中心管理后台"栏目的admin账户和默认密码登陆云文档服务
- 请使用"运维平台/部署平台"栏目中的wpsadmin账户和默认密码登陆运维平台

## 联系我们

欢迎访问WPS官网（[https://www.wps.cn](https://www.wps.cn)）了解更多信息。

联系邮箱：[https://www.wps.cn](mailto:https://www.wps.cn)
