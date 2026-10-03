# 核心数据模型草案

> 字段与关系用于讨论和原型设计；接入现有业务表格前需核对真实口径。所有表建议包含内部主键、创建/更新时间。金额使用人民币分的整数存储，展示时换算为元。

## 实体与关系

| 实体 | 主要字段（草案） | 用途 |
|---|---|---|
| Student 学员 | id, student_no, name, status, notes | 学员档案；联系方式按最小必要原则保存 |
| Course 班级/课程 | id, name, level, lesson_minutes, status | 启蒙、级位、冲段、段位等教学班级 |
| Product 套餐 | id, name, product_type, price_cents, included_lessons, validity_start/end | 按次、年费、半年费等售卖项目及适用规则 |
| Enrollment 报名 | id, student_id, course_id, product_id, start/end, status | 记录学员购买何种套餐及有效期 |
| Payment 缴费 | id, enrollment_id, paid_at, amount_cents, method, external_ref, status | 实收款项；一笔报名可分多次付款 |
| LessonSession 课次 | id, course_id, starts_at, duration_minutes, status | 实际开课记录 |
| Attendance 出勤/核销 | id, session_id, student_id, enrollment_id, lesson_units, status | 记录出勤及对应套餐扣次 |
| Refund 退款 | id, payment_id, refunded_at, amount_cents, reason, status | 关联原收款并保留审计轨迹 |
| Expense 收支 | id, occurred_at, direction, category, amount_cents, method, counterparty, memo | 房租、工资、水电、宽带及其他经营收支 |
| Reconciliation 对账批次 | id, period_start/end, created_at, status, notes | 留存某一期间对账的范围、结果及处理状态 |
| AuditEvent 操作记录 | id, occurred_at, actor, action, entity_type/id, summary | 追踪新增、更正、导入、导出及冲销操作 |

## 关键规则（待业务确认）

- 缴费是现金流事实，套餐售价/应收与实收可能不同，应分别记录并能说明优惠或差异。
- 退款不能直接抹掉收款；应引用原缴费，限制退款累计不超过该笔可退金额，并保留原因。
- 课次核销关联具体学员、课次与报名，避免只改套餐余额而无法追溯。
- 年费、半年费、赠课、按次等套餐的可用次数/期限规则需确认；无限次课程不应伪装成固定课次余额。
- 对账记录应能从原始收支与缴费重算，不能只保存一个手工填写的汇总数。
- 个人账户与公司账户、现金/微信/支付宝/银行卡等资金渠道需按实际对账需求确定。

## 约束与隐私

- 金额字段非负；方向/状态/支付方式采用受控枚举，并对每次状态变更留痕。
- 重要业务记录采用作废、冲销或更正事件，避免不可追溯的硬删除。
- 唯一键需支持导入幂等，例如来源文件批次 + 源行标识；不得仅凭姓名判定重复学员。
- 生产数据库及备份保存在用户指定的本地数据目录，不进入代码仓库。
