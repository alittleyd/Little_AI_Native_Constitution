# 初次勘查报告（CTO 抽检）

范围与方法（聚焦 2025+）
- 以 2025-01-01 之后有改动的项目为主，快速扫描目录：`lb_shop`、`tp5.1-lubang`、`lubang-crm`、`zt-app`、`lbtek-admin-new`、`lbtek-test`、`lubon_admin`、`admin`
- 重点关注：项目结构、依赖版本、超大类/服务、全局函数、迁移文件（2025 年）与测试覆盖情况

1. 直观感受
- 一句话：**多项目并行、版本割裂的“多头单体”，业务逻辑巨型化。**

2. 架构健康度
- 有框架但缺少清晰架构边界，主要表现为“Controller/Service/Model 巨无霸 + 全局函数”。
- 典型 God Class/巨型文件（行数级别）：
  - `lb_shop/app/Services/MultilingualEsSearchService.php` ~2199 行
  - `lb_shop/app/Services/Api/V1/OrderService.php` ~1703 行
  - `tp5.1-lubang/application/index/service/KdService.php` ~3234 行
  - `lubang-crm/app/Http/services/IndexService.php` ~2710 行
  - `zt-app/app/Services/Kingdee.php` ~2579 行
  - `lubang-crm/app/Models/User.php` ~1780 行
- `common.php` 类型全局 helper 在多项目中存在（如 `lb_shop/app/Http/common/common.php`），隐式耦合、难以追踪调用链。
- 同一业务在多个代码库重复实现（KdService / OrderService / CartService 等），规则分散、版本漂移风险高。
- 框架/运行时版本割裂：Laravel 11/10/9 与 ThinkPHP 5.1 并存（PHP 8.2/8.1/8.0/7.4）。

3. 潜在风险（新增功能的最大风险）
- **业务规则多处实现 + 超大服务类**：修改牵一发而动全身，容易漏改或改错分支。
- **测试覆盖薄弱**：`lb_shop` 约 31 个测试文件；`lubang-crm`/`zt-app` 各约 4 个；`tp5.1-lubang` 基本无测试目录。
- **副作用不可见**：全局函数与巨型服务类让影响范围难评估，回归成本高。
- **仓库膨胀**：vendor、dist、打包产物入库，依赖漂移/回滚难、仓库体积大。
- **配置泄露风险**：多个项目 `.env` 内存在真实 DB/Redis/Mail 凭据（非空）。

4. 数据库设计（2025+ 可见部分）
- `lb_shop` 2025 年迁移显示大量 `clone` / `lang` 表（如 `product_list_clone`、`product_list_attr_clone`、`category_lang`、`product_list_lang`）。
- 普遍缺少外键约束，`list_id`/`cate_id` 等仅建索引；`lang` 多语言表缺少唯一约束，易产生重复语言数据。
- 广泛使用 `create_time/update_time/delete_time` 整型时间戳与 `delete_time` 软删字段，和 Laravel 默认 timestamps/softDeletes 不一致，数据一致性与维护成本高。
- 反范式字段明显：如 `recommend_article_ids`/`recommend_list_ids` 以字符串保存多 ID，后续查询/索引/一致性成本高。

结论（给 CTO 的一句话）
- 这是一个“多版本框架 + 巨型服务类 + 数据重复”的遗留体系；短期可运行，长期扩展与统一成本高。

---

# DianTech 技术审计终审判决书

依据
- 以《MY-ENGINEERING-PHILOSOPHY v20250121.md》为准绳，且该系统承担“金钱/订单”核心责任（Level 3 场景）。

1. Level 定级
- 实际工程纪律落在 **Level 2 下限、局部 Level 1**；但业务责任是 **Level 3**。
- 结论：**确实在用 Level 1/2 的方式做 Level 3 的事**。

2. 违宪证据（反模式 3：偷偷变化）
- Level 3 责任已成立：支付/订单依赖明确存在（`lb_shop/composer.json`、`tp5.1-lubang/composer.json`）。
- 仍在使用 Level 1/2 纪律：超大服务类/巨型文件（`lb_shop/app/Services/Api/V1/OrderService.php`、`tp5.1-lubang/application/index/service/KdService.php`、`lubang-crm/app/Http/services/IndexService.php`、`zt-app/app/Services/Kingdee.php`）。
- 隐式耦合：全局 helper 仍大量存在（`lb_shop/app/Http/common/common.php`）。
- 工程可交付性不足：测试覆盖极薄（`lb_shop/tests`、`lubang-crm/tests`、`zt-app/tests`；`tp5.1-lubang`未见测试目录）。
- 生产痕迹与安全风险并存：多项目 `.env` 里存在真实凭据（`lb_shop/.env`、`lubang-crm/.env`、`zt-app/.env`、`lubon_admin/.env`）。

3. OSI 思维（分层审查）
- 实际分层不清晰，外部系统集成、数据访问、业务逻辑在单一 Service 中交织（`lb_shop/app/Services/KdService.php`、`tp5.1-lubang/application/index/service/OrderService.php`）。
- 结论：**未形成清晰的 OSI 七层映射，呈现“层间混杂”**。

4. 最终判决
- [ ] 建议重构 (Refactor)
- [x] 建议重写 (Rewrite)
- [ ] 建议销毁 (Destroy)

裁定理由
- 核心交易系统承担 Level 3 责任，但工程纪律未升级到 Level 3；重构成本已接近重写成本，且风险不可控。
- 建议采用 **分阶段重写（Strangler 模式）**：先重写订单/支付核心域，旧系统冻结新增需求，逐步替换。

---

# 团队能力判断（基于代码证据的推断）

结论（一句话）
- 三人团队的执行力尚可，但**工程纪律与架构治理能力不足以支撑 Level 3 责任**。

判断依据（非个人评价，仅据代码特征）
- 能力表现：能在多个系统中交付可运行的业务（多框架并行、集成外部系统、业务覆盖广）。
- 工程纪律弱：核心逻辑集中于超大 Service/Model，缺乏清晰边界与拆分策略。
- 可交付性不足：测试稀薄、文档与交付物缺失，难以满足 Level 3 的可审计/可回归要求。
- 组织协作迹象：同一业务在多仓库重复实现，说明跨项目治理/统一规范缺失。

建议定位
- 现有团队整体更接近 **Level 2 执行型团队**；若继续承担 Level 3 责任，需要补足 **架构/质量/测试负责人** 角色。
