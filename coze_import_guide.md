# 扣子(Coze) AI销售工作流导入指南

## 概述

本指南将帮助您将AI销售工作流导入到扣子(Coze)平台，实现从客户识别到成交的全流程自动化。

## 前置条件

1. **扣子账号**: 注册并登录 [扣子平台](https://www.coze.cn/)
2. **权限确认**: 确保账号具有创建工作流的权限
3. **配置文件**: 下载 `coze_ai_sales_workflow.json` 文件

## 导入步骤

### 方式一：直接创建工作流（推荐）

#### 第一步：创建新工作流
1. 登录扣子平台
2. 进入「工作流」页面
3. 点击「创建工作流」
4. 填写工作流名称：`AI销售工作流`
5. 填写描述：`智能AI销售助手，从客户识别到成交的全流程自动化工作流`

#### 第二步：按顺序创建节点

##### 1. 开始节点配置
- 默认已存在，需要添加输入变量：
  - 变量名：`customer_info`
  - 类型：`string`
  - 标签：`客户基本信息`
  - 必填：是
  - 描述：`请输入客户的基本信息（公司名称、行业、规模等）`
  
  - 变量名：`inquiry_content`
  - 类型：`string`
  - 标签：`咨询内容`
  - 必填：是
  - 描述：`客户的具体咨询内容和需求描述`

##### 2. 添加"客户分类"大模型节点
- 节点类型：`大模型`
- 节点名称：`客户分类`
- 模型选择：`豆包·function call 32k`
- 处理方式：`单次`
- 输入配置：
  - 名称：`customer_data`
  - 值：`{{customer_info}}`
- 用户提示词：
```
# 角色
你是一位专业的客户分析专家，擅长根据客户信息评估其价值等级和购买潜力。

## 任务
基于提供的客户信息：{{customer_data}}，进行客户分类和评估。

## 分类标准
- A类客户：大型企业，预算充足，决策权明确，有明确AI需求
- B类客户：中型企业，有一定预算，需求相对明确
- C类客户：小型企业或个人，预算有限，需求模糊

## 输出要求
请严格按照以下JSON格式输出：
```json
{
  "customer_level": "A/B/C",
  "potential_value": "高/中/低",
  "budget_estimate": "预估预算范围",
  "decision_timeline": "预估决策周期",
  "key_concerns": "主要关注点",
  "recommended_approach": "推荐的销售策略"
}
```
- 输出配置：
  - 名称：`classification_result`
  - 类型：`object`

##### 3. 添加"需求分析"大模型节点
- 节点类型：`大模型`
- 节点名称：`需求分析`
- 模型选择：`豆包·function call 32k`
- 输入配置：
  - `inquiry`: `{{inquiry_content}}`
  - `customer_class`: `{{classification_result}}`
- 用户提示词：
```
# 角色
你是一位资深的需求分析师，擅长深入挖掘客户的真实需求和痛点。

## 任务
基于客户咨询内容：{{inquiry}} 和客户分类：{{customer_class}}，进行深度需求分析。

## 分析维度
1. 业务痛点识别
2. 技术需求梳理
3. 预算和时间约束
4. 成功标准定义
5. 决策影响因素

## 输出格式
```json
{
  "core_needs": ["核心需求1", "核心需求2", "核心需求3"],
  "pain_points": ["痛点1", "痛点2", "痛点3"],
  "technical_requirements": "技术要求描述",
  "budget_sensitivity": "价格敏感度分析",
  "urgency_level": "紧急程度",
  "success_criteria": "成功标准",
  "potential_obstacles": "潜在阻碍因素"
}
```
- 输出配置：
  - 名称：`needs_analysis_result`
  - 类型：`object`

##### 4. 添加"产品推荐"大模型节点
- 节点类型：`大模型`
- 节点名称：`产品推荐`
- 模型选择：`豆包·function call 32k`
- 输入配置：
  - `needs`: `{{needs_analysis_result}}`
  - `customer_level`: `{{classification_result}}`
- 用户提示词：
```
# 角色
你是AI产品专家，深度了解各类AI解决方案的特点和适用场景。

## 产品库
1. **智能客服机器人**
   - 适用场景：客户服务优化、降低人力成本
   - 价格范围：5-20万
   - 实施周期：1-2个月
   - 技术特点：24/7在线、多轮对话、情感识别

2. **AI数据分析平台**
   - 适用场景：商业决策支持、数据洞察
   - 价格范围：10-50万
   - 实施周期：2-3个月
   - 技术特点：自动化报表、预测分析、可视化

3. **智能流程自动化**
   - 适用场景：业务流程优化、效率提升
   - 价格范围：15-80万
   - 实施周期：3-6个月
   - 技术特点：工作流自动化、文档处理、审批流程

4. **定制AI解决方案**
   - 适用场景：特殊需求、深度定制
   - 价格范围：50-200万
   - 实施周期：6-12个月
   - 技术特点：专属算法、定制化接口、个性化功能

## 任务
基于客户需求分析{{needs}}和客户等级{{customer_level}}，推荐最合适的产品组合。

## 输出格式
```json
{
  "recommended_products": [
    {
      "product_name": "产品名称",
      "match_score": "匹配度评分(1-10)",
      "reasons": "推荐理由",
      "expected_benefits": "预期收益",
      "investment_range": "投资范围",
      "implementation_plan": "实施建议"
    }
  ],
  "total_investment": "总投资估算",
  "roi_prediction": "投资回报预测",
  "risk_assessment": "风险评估"
}
```
- 输出配置：
  - 名称：`product_recommendation_result`
  - 类型：`object`

##### 5. 添加"报价生成"大模型节点
- 节点类型：`大模型`
- 节点名称：`报价生成`
- 模型选择：`豆包·function call 32k`
- 输入配置：
  - `products`: `{{product_recommendation_result}}`
  - `customer_info`: `{{classification_result}}`
- 用户提示词：
```
# 角色
你是专业的商务报价专家，能够生成具有竞争力和吸引力的报价方案。

## 定价策略
- 基础价格：产品标准价格
- 客户等级折扣：A类客户5%，B类客户8%，C类客户12%
- 批量折扣：多产品组合额外5-10%折扣
- 紧急项目：加急需求增加10-15%
- 定制开发：根据复杂度单独核算

## 任务
基于推荐产品{{products}}和客户信息{{customer_info}}，生成详细报价方案。

## 输出格式
```json
{
  "quote_number": "报价编号",
  "valid_until": "报价有效期",
  "items": [
    {
      "product_name": "产品名称",
      "description": "产品描述",
      "quantity": "数量",
      "unit_price": "单价",
      "discount": "折扣",
      "subtotal": "小计"
    }
  ],
  "total_before_discount": "折扣前总额",
  "total_discount": "总折扣",
  "final_total": "最终总价",
  "payment_terms": "付款条件",
  "delivery_schedule": "交付计划",
  "warranty_terms": "质保条款",
  "special_offers": "特别优惠"
}
```
- 输出配置：
  - 名称：`quote_result`
  - 类型：`object`

##### 6. 添加"提案生成"大模型节点
- 节点类型：`大模型`
- 节点名称：`提案生成`
- 模型选择：`豆包·function call 32k`
- 输入配置：
  - `customer_analysis`: `{{classification_result}}`
  - `needs_analysis`: `{{needs_analysis_result}}`
  - `product_recommendation`: `{{product_recommendation_result}}`
  - `quote`: `{{quote_result}}`
- 用户提示词：
```
# 角色
你是资深商务提案专家，擅长撰写专业、有说服力的商业提案。

## 提案结构
1. 执行摘要
2. 客户需求分析
3. 解决方案概述
4. 技术实施方案
5. 项目实施计划
6. 投资回报分析
7. 风险评估与应对
8. 商务条款
9. 公司资质介绍

## 任务
基于以下信息生成专业提案：
- 客户分析：{{customer_analysis}}
- 需求分析：{{needs_analysis}}
- 产品推荐：{{product_recommendation}}
- 报价信息：{{quote}}

## 输出要求
以Markdown格式输出完整提案内容，要求：
1. 结构清晰，逻辑严密
2. 内容专业，针对性强
3. 数据详实，有说服力
4. 格式美观，易于阅读
```
- 输出配置：
  - 名称：`proposal_content`
  - 类型：`string`

##### 7. 添加"跟进策略制定"大模型节点
- 节点类型：`大模型`
- 节点名称：`跟进策略制定`
- 模型选择：`豆包·function call 32k`
- 输入配置：
  - `customer_profile`: `{{classification_result}}`
  - `proposal_sent`: `true`
- 用户提示词：
```
# 角色
你是经验丰富的销售策略专家，擅长制定个性化的客户跟进策略。

## 跟进原则
- A类客户：24小时内首次跟进，后续每2-3天跟进一次
- B类客户：48小时内首次跟进，后续每周跟进一次
- C类客户：72小时内首次跟进，后续每两周跟进一次

## 任务
基于客户档案{{customer_profile}}，制定详细的跟进策略。

## 输出格式
```json
{
  "follow_up_schedule": [
    {
      "day": "跟进日期",
      "action": "跟进动作",
      "content": "沟通内容",
      "goal": "预期目标"
    }
  ],
  "communication_channels": ["邮件", "电话", "微信", "上门拜访"],
  "key_messages": "核心沟通要点",
  "success_indicators": "成功指标",
  "escalation_triggers": "升级触发条件",
  "alternative_strategies": "备选策略"
}
```
- 输出配置：
  - 名称：`follow_up_plan`
  - 类型：`object`

##### 8. 添加"ROI计算器"代码节点
- 节点类型：`代码`
- 节点名称：`ROI计算器`
- 语言：`Python`
- 输入配置：
  - `quote_info`: `{{quote_result}}`
  - `customer_info`: `{{classification_result}}`
- 代码内容：
```python
import json
import math

def main(args):
    # 获取输入参数
    quote_data = args.get('quote_info', {})
    customer_data = args.get('customer_info', {})
    
    try:
        # 解析JSON字符串如果需要
        if isinstance(quote_data, str):
            quote_data = json.loads(quote_data)
        if isinstance(customer_data, str):
            customer_data = json.loads(customer_data)
        
        # 基本参数
        total_investment = float(quote_data.get('final_total', 0))
        customer_level = customer_data.get('customer_level', 'C')
        
        # 根据客户等级预估年收益
        if customer_level == 'A':
            annual_savings = total_investment * 0.8  # 大客户效率提升显著
            risk_factor = 0.1
        elif customer_level == 'B':
            annual_savings = total_investment * 0.6  # 中等客户
            risk_factor = 0.15
        else:
            annual_savings = total_investment * 0.4  # 小客户
            risk_factor = 0.2
        
        # 计算ROI指标
        payback_period = total_investment / annual_savings if annual_savings > 0 else float('inf')
        roi_1_year = ((annual_savings - total_investment) / total_investment) * 100 if total_investment > 0 else 0
        roi_3_year = ((annual_savings * 3 - total_investment) / total_investment) * 100 if total_investment > 0 else 0
        
        # NPV计算（假设折现率为10%）
        discount_rate = 0.1
        npv = -total_investment
        for year in range(1, 4):
            npv += annual_savings / ((1 + discount_rate) ** year)
        
        # 生成结果
        result = {
            "total_investment": total_investment,
            "annual_savings": annual_savings,
            "payback_period_months": round(payback_period * 12, 1),
            "roi_1_year_percent": round(roi_1_year, 2),
            "roi_3_year_percent": round(roi_3_year, 2),
            "npv_3_year": round(npv, 2),
            "risk_assessment": {
                "risk_level": "低" if risk_factor < 0.15 else "中" if risk_factor < 0.25 else "高",
                "success_probability": round((1 - risk_factor) * 100, 1)
            },
            "recommendation": "推荐投资" if roi_1_year > 20 and payback_period < 2 else "需要评估" if roi_1_year > 0 else "不建议投资"
        }
        
        return {"roi_analysis": result}
        
    except Exception as e:
        return {"roi_analysis": {"error": str(e), "total_investment": 0}}

# Coze工作流入口函数
def handler(args):
    return main(args)
```
- 输出配置：
  - 名称：`roi_analysis`
  - 类型：`object`

##### 9. 配置结束节点
- 回答模式：`使用设定的内容直接回答`
- 流式输出：`开启`
- 输出变量配置：
  - `customer_classification`: `{{classification_result}}`
  - `needs_analysis`: `{{needs_analysis_result}}`
  - `product_recommendations`: `{{product_recommendation_result}}`
  - `quote_details`: `{{quote_result}}`
  - `proposal`: `{{proposal_content}}`
  - `follow_up_strategy`: `{{follow_up_plan}}`
  - `roi_analysis`: `{{roi_analysis}}`

- 回答内容模板：
```markdown
# AI销售工作流执行结果

## 📊 客户分析
```json
{{customer_classification}}
```

## 🎯 需求分析
```json
{{needs_analysis}}
```

## 💡 产品推荐
```json
{{product_recommendations}}
```

## 💰 报价方案
```json
{{quote_details}}
```

## 📈 ROI分析
```json
{{roi_analysis}}
```

## 📋 商业提案
{{proposal}}

## 🎯 跟进策略
```json
{{follow_up_strategy}}
```

---

**工作流执行完成** ✅

您可以基于以上分析结果，进行下一步的销售活动。如需调整任何环节，请重新运行相应的工作流节点。
```

#### 第三步：连接节点
按照以下顺序连接各个节点：
1. 开始节点 → 客户分类
2. 客户分类 → 需求分析  
3. 需求分析 → 产品推荐
4. 产品推荐 → 报价生成
5. 报价生成 → 提案生成
6. 客户分类 → 跟进策略制定
7. 报价生成 → ROI计算器
8. 提案生成 → 结束节点
9. 跟进策略制定 → 结束节点
10. ROI计算器 → 结束节点

#### 第四步：测试工作流
1. 点击「试运行」
2. 输入测试数据：
   ```
   客户基本信息：某科技公司，员工500人，主营软件开发，年营收5000万
   咨询内容：希望引入AI客服系统，提升客户服务效率，预算100万左右
   ```
3. 检查各节点输出是否正常

#### 第五步：发布工作流
1. 确认测试通过后，点击「发布」
2. 填写发布信息和版本说明

### 方式二：JSON导入（如果平台支持）

如果扣子平台支持JSON导入功能：
1. 登录扣子平台
2. 进入工作流页面
3. 选择「导入工作流」
4. 上传 `coze_ai_sales_workflow.json` 文件
5. 确认导入

## 使用说明

### 输入格式
启动工作流时，需要提供两个必填参数：

1. **客户基本信息**：包含公司名称、行业、规模、联系方式等
2. **咨询内容**：客户的具体需求和咨询问题

### 输出内容
工作流将提供以下完整的销售支持：

1. **客户分析**：客户等级分类和价值评估
2. **需求分析**：深度挖掘客户真实需求和痛点
3. **产品推荐**：基于需求匹配的AI解决方案推荐
4. **报价方案**：详细的价格明细和商务条款
5. **商业提案**：专业的解决方案提案文档
6. **ROI分析**：投资回报率计算和风险评估
7. **跟进策略**：个性化的客户跟进计划

## 常见问题

### Q1: 工作流运行失败怎么办？
**A**: 
1. 检查各节点的输入输出配置是否正确
2. 确认提示词格式无误
3. 检查节点连接是否完整
4. 查看错误日志定位具体问题

### Q2: 如何调整产品库？
**A**: 
修改"产品推荐"节点的用户提示词中的产品库部分，添加或修改产品信息。

### Q3: 如何自定义报价策略？
**A**: 
修改"报价生成"节点的用户提示词中的定价策略部分。

### Q4: 如何优化客户分类标准？
**A**: 
修改"客户分类"节点的提示词，调整分类标准和评估维度。

## 技术支持

如果在导入或使用过程中遇到问题，可以：
1. 查看扣子平台官方文档
2. 联系扣子平台技术支持
3. 在相关技术社区寻求帮助

## 更新日志

### v1.0.0 (2024-01-20)
- 初始版本发布
- 包含完整的销售工作流功能
- 支持客户分类、需求分析、产品推荐、报价生成等核心功能