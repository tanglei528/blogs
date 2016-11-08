---
title: aws ec2 与 s3 关联
date: 2016-11-02 16:20:08
tags: aws
categories: 所有文章
---
# 需求
  在ec2里创建虚机，这些虚机需要往s3存储桶里写数据

## 操作步骤
<!--more-->
### 创建S3存储桶
#### 服务 --> 所有AWS服务 --> s3 
<img src="/img/aws/s3-1.png">
#### 创建存储桶 
<img src="/img/aws/s3-2.png">


### 创建策略
#### 服务 --> 所有AWS服务 --> IAM 
<img src="/img/aws/aws-service.png">
#### 策略 --> 创建策略
<img src="/img/aws/celue1.png">
#### 策略生成器
<img src="/img/aws/celueshengchengqi.png">
#### Amazon S3 服务、操作、ARN
<img src="/img/aws/celue2.png">
#### 下一步
<img src="/img/aws/celue3.png">
#### 填写策略名称
<img src="/img/aws/celue4.png">

### 创建角色
#### 服务 --> 所有AWS服务 --> IAM --> 角色 --> AWS 服务角色 --> Amazon EC2
<img src="/img/aws/role1.png">
#### 选择前面创建的策略
<img src="/img/aws/role2.png">
#### 创建
<img src="/img/aws/role3.png">

### 创建虚机
#### 在配置实例选项卡里，选择IAM角色为前面创建的角色
<img src="/img/aws/aws-instance.png">

### 至此，EC2的云主机与s3的存储关联上
