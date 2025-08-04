# 扣子 AI销售工作流 - 简化版

## 工作流概述

这是一个简化的AI销售工作流，包含5个核心节点，适合快速搭建和测试。

## 节点配置

### 1. 开始节点
**输入变量**：
- `customer_info` (string, 必填): 客户信息
- `inquiry` (string, 必填): 咨询内容

### 2. 客户分析节点 (大模型)
**模型**: 豆包·function call 32k
**输入**: `{{customer_info}}`
**提示词**:
```
你是客户分析专家。根据客户信息：{{customer_info}}，分析客户等级。

输出JSON格式：
{
  "level": "A/B/C级客户",
  "budget": "预算范围",
  "priority": "优先级",
  "strategy": "销售策略"
}
```
**输出**: `customer_analysis` (object)

### 3. 需求分析节点 (大模型)
**模型**: 豆包·function call 32k
**输入**: 
- `{{inquiry}}`
- `{{customer_analysis}}`

**提示词**:
```
你是需求分析师。基于咨询内容：{{inquiry}} 和客户分析：{{customer_analysis}}，分析客户需求。

输出JSON格式：
{
  "needs": ["需求1", "需求2", "需求3"],
  "pain_points": ["痛点1", "痛点2"],
  "urgency": "紧急程度",
  "budget_range": "预算范围"
}
```
**输出**: `needs_analysis` (object)

### 4. 方案推荐节点 (大模型)
**模型**: 豆包·function call 32k
**输入**:
- `{{needs_analysis}}`
- `{{customer_analysis}}`

**提示词**:
```
你是AI产品专家。基于需求分析：{{needs_analysis}} 和客户分析：{{customer_analysis}}，推荐AI解决方案。

产品选项：
1. 智能客服机器人 (5-20万)
2. AI数据分析平台 (10-50万) 
3. 流程自动化系统 (15-80万)
4. 定制AI解决方案 (50-200万)

输出JSON格式：
{
  "recommended_product": "推荐产品",
  "price_range": "价格范围",
  "implementation_time": "实施周期",
  "key_benefits": ["收益1", "收益2", "收益3"],
  "next_steps": "下一步行动"
}
```
**输出**: `recommendation` (object)

### 5. 结束节点
**回答模式**: 使用设定的内容直接回答
**流式输出**: 开启

**输出变量**:
- `customer_analysis`: `{{customer_analysis}}`
- `needs_analysis`: `{{needs_analysis}}`
- `recommendation`: `{{recommendation}}`

**回答内容**:
```markdown
# 🎯 AI销售分析报告

## 📊 客户分析
**客户等级**: {{customer_analysis.level}}
**预算范围**: {{customer_analysis.budget}}
**销售策略**: {{customer_analysis.strategy}}

## 🔍 需求分析
**核心需求**: {{needs_analysis.needs}}
**主要痛点**: {{needs_analysis.pain_points}}
**紧急程度**: {{needs_analysis.urgency}}

## 💡 解决方案推荐
**推荐产品**: {{recommendation.recommended_product}}
**投资范围**: {{recommendation.price_range}}
**实施周期**: {{recommendation.implementation_time}}

### 核心收益
{{recommendation.key_benefits}}

### 下一步行动
{{recommendation.next_steps}}

---
*AI销售工作流 v1.0*
```

## 节点连接顺序
1. 开始节点 → 客户分析节点
2. 客户分析节点 → 需求分析节点
3. 需求分析节点 → 方案推荐节点
4. 方案推荐节点 → 结束节点

## 测试数据
**客户信息**: `北京某科技公司，500人规模，主营软件开发，年营收5000万，寻求AI解决方案提升效率`

**咨询内容**: `我们希望引入AI客服系统，提升客户服务效率，减少人工成本，预算在100万左右，希望3个月内实施完成`

## 使用说明
1. 按照上述配置创建各个节点
2. 设置好节点间的连接关系
3. 使用测试数据进行试运行
4. 根据输出结果调整提示词
5. 发布工作流供正式使用

这个简化版本更容易理解和实施，适合快速验证AI销售工作流的核心功能。