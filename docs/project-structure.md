# 目录结构说明

以下是随功能增长逐步采用的目标结构。当前仓库只建立文档骨架，不代表这些程序目录已经实现。

```text
sgy/
├── README.md
├── docs/
│   ├── project-structure.md
│   ├── data-model.md
│   ├── windows-local.md
│   └── excel-import-export.md
├── src/
│   └── finance_app/
│       ├── ui/                 # 桌面界面与页面
│       ├── services/           # 业务规则、对账和报表
│       ├── repositories/       # SQLite 持久化访问
│       ├── models/             # 领域对象与校验
│       └── imports/            # Excel 读取、映射、导出
├── migrations/                 # 数据库结构升级脚本
├── tests/                      # 单元与集成测试，不含真实用户数据
├── samples/                    # 脱敏或合成的表格模板
├── packaging/                  # Windows 打包配置、图标及版本信息
└── .gitignore                  # 忽略数据库、备份、构建物和敏感文件
```

## 边界约定

- UI 不直接操作数据库；调用 services，由 repositories 执行持久化。
- 业务金额采用整数分（或等价的精确十进制），不使用二进制浮点金额。
- 数据库变更必须有可重复执行、可检查的迁移脚本。
- Excel 导入先解析到预览/暂存层，用户确认后才写入正式业务记录。
- 日志不记录不必要的学员个人信息；导出文件和数据库不纳入 Git。
- 测试使用构造数据，样例文件不得含可识别真实学员的信息。
