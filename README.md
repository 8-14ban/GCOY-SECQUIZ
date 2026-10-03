# GCOY-SECQUIZ

AI 安全刷题与间隔重复学习助手。内置 18 道覆盖 OWASP LLM Top 10（2025）与 Agent
特有风险面的题目，配合 SM-2 间隔重复算法安排复习，附 10 张知识卡。零依赖，单文件，Python 3.10+。

与 [agentscan](https://github.com/TSVMV/agentscan) 的检测面呼应：先在这里把概念刷熟，再到 agentscan 实战。

## 安装

```bash
git clone https://github.com/8-14ban/GCOY-SECQUIZ.git
```

## 快速开始

```bash
# 看知识卡
python3 gcoy_secquiz.py cards

# 刷题（5 题，含到期复习优先）
python3 gcoy_secquiz.py quiz --n 5

# 只复习到期题目
python3 gcoy_secquiz.py quiz --review

# 掌握度统计
python3 gcoy_secquiz.py stats

# 导出 HTML 战报
python3 gcoy_secquiz.py export --out report.html
```

## 子命令

| 命令 | 说明 |
| --- | --- |
| `quiz --n 5 [--review]` | 交互答题，到期题优先；`--review` 只刷到期 |
| `add "题干" --opts "A|B|C|D" --ans 2 --cat Defense --diff 1 --why "解析"` | 添加自定义题 |
| `stats` | 按分类统计题数/答题数/正确率/到期数 |
| `cards` | 打印 OWASP LLM Top 10 知识卡（风险 + 缓解） |
| `export --out report.html` | 单文件 HTML 战报，含知识卡 + 全题库 + 答题历史 |
| `selftest` | 自检 |

## 间隔重复算法

采用简化版 SM-2：

- 首次答对：1 天后复习
- 第二次答对：3 天后复习
- 之后：间隔 = 上次间隔 × 易度系数（初始 2.5）
- 答对：易度 +0.1（封顶 3.0）
- 答错：清零、次日复习、易度 -0.2（下限 1.3）

这样**记得牢的题自动拉长间隔，常错的题频繁出现**，把复习时间花在薄弱处。

## 题库覆盖

| OWASP LLM | 检测面 |
| --- | --- |
| LLM01 提示注入 | 间接注入、工具描述投毒 |
| LLM02 不安全输出 | 执行/渲染未净化 |
| LLM03 训练数据投毒 | 后门触发词 |
| LLM04 模型 DoS | 资源耗尽 |
| LLM05 供应链 | 第三方组件篡改 |
| LLM06 敏感信息泄露 | 训练数据外泄 |
| LLM07 插件设计缺陷 | 权限过宽 |
| LLM08 过度自主 | 无人确认执行 |
| LLM09 过度依赖 | 盲信结论 |
| LLM10 模型窃取 | 查询反推权重 |

另含 Agent 记忆投毒、MCP 加固等扩展题。

## 数据结构

```
questions.json    # 题库（含内置 18 题 + 自定义题）
progress.json     # 每题的 SM-2 状态与答题历史
```

纯 JSON，可手动编辑、版本管理、跨机器同步。

## 自检

```bash
python3 gcoy_secquiz.py selftest
```
