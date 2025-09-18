 ### 概念
- 产品信息树：领域 - 产品 - 子产品 - 模块 - 租户
- 三库一致：代码库、发布库、生产环境代码
- 燃尽图：随着时间的减少工作量的剩余情况

# 敏捷协同
1. RR(Raw Requirement)：原始需求
2. IR(IT Requirement)：IT需求
3. US(User Story)：用户故事
4. Task/BUG：开发任务/测试缺陷，记录人天工作量
- 产品特性关联：功能特性、非功能特性（DFX：网络安全、数据库迁移、性能优化）
- 迭代(Sprint)容量管理：按工作项，提前录入每个角色的每天投入时长
- 角色分工：PO、SM、团队（BA、SE、TE）
- 需求变更记录：管理迭代期间的所有需求变化

# 开发
- 环境资源管理：DEV -> SIT -> UAT -> PROD
- 代码管理：Git、Committer QC(Quality Control)
- 代码指标：UT覆盖率(line/branch/method)、圈复杂度、代码重复率、冗余代码密度
- 流水线：Merge Requests门禁、服务级版本流水线
- 生命周期：alpha -> beta -> RC -> GA -> EOL(End Of Life)
- 开源管理：统一化开源镜像仓、开源扫描与成分确认、源码修改Patch

# 测试
- API测试、兼容性测试、性能测试

# 安全合规
- 漏洞扫描：CVE扫描

# 版本发布
1. 质量红线报告
2. 上线包转发布库
3. 发起变更申请