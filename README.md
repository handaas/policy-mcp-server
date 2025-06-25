# 招投标大数据服务

[该MCP服务提供全面的政策相关信息查询功能，包括政府补贴、政策优惠、政策支持等信息，帮助用户了解企业的政策扶持情况。](https://www.handaas.com/)

## 主要功能

- 🔍 企业关键词模糊搜索
- 💰 获批项目统计分析
- 🎯 政策详情查询
- 📜 政策搜索功能
- 🏛️ 政府项目分布分析

## 环境要求

- Python 3.10+
- 依赖包：python-dotenv, requests, mcp

## 本地快速启动

### 1. 克隆项目
```bash
git clone https://github.com/handaas/policy-mcp-server
cd policy-mcp-server
```

### 2. 创建虚拟环境&安装依赖

```bash
python -m venv mcp_env && source mcp_env/bin/activate
pip install -r requirements.txt
```

### 3. 环境配置

复制环境变量模板并配置：

```bash
cp .env.example .env
```

编辑 `.env` 文件，配置以下环境变量：

```env
INTEGRATOR_ID=your_integrator_id
SECRET_ID=your_secret_id
SECRET_KEY=your_secret_key
```

### 4. streamable-http启动服务

```bash
python server/mcp_server.py streamable-http
```

服务将在 `http://localhost:8000` 启动。

#### 支持启动方式 stdio 或 sse 或 streamable-http

### 5. Cursor / Cherry Studio MCP配置

```json
{
  "mcpServers": {
    "handaas-mcp-server": {
      "type": "streamableHttp",
      "url": "http://127.0.0.1:8000/mcp"
    }
  }
}
```

## STDIO版安装部署

### 设置Cursor / Cherry Studio MCP配置

```json
{
  "mcpServers": {
    "policy-mcp-server": {
      "command": "uv",
      "args": ["run", "mcp", "run", "{workdir}/server/mcp_server.py"],
      "env": {
        "PATH": "{workdir}/mcp_env/bin:$PATH",
        "PYTHONPATH": "{workdir}/mcp_env",
        "INTEGRATOR_ID": "your_integrator_id",
        "SECRET_ID": "your_secret_id",
        "SECRET_KEY": "your_secret_key"
      }
    }
  }
}
```

## 使用官方Remote服务

### 1. 直接设置Cursor / Cherry Studio MCP配置

```json
{
  "mcpServers": {
    "policy-mcp-server":{
      "type": "streamableHttp",
      "url": "https://mcp.handaas.com/policy/policy_bigdata?token={token}"  
      }
  }
}
```

### 注意：integrator_id、secret_id、secret_key及token需要登录 https://www.handaas.com/ 进行注册开通平台获取


## 可用工具

### 1. policy_bigdata_fuzzy_search
**功能**: 企业关键词模糊查询

根据提供的企业名称、人名、品牌、产品、岗位等关键词模糊查询相关企业列表。

**参数**:
- `matchKeyword` (必需): 匹配关键词 - 查询各类信息包含匹配关键词的企业
- `pageIndex` (可选): 分页开始位置
- `pageSize` (可选): 分页结束位置 - 一页最多获取50条数据

**返回值**:
- `total`: 总数
- `resultList`: 结果列表，包含企业基本信息

### 2. policy_bigdata_approved_project_stats
**功能**: 获批项目统计分析

查询企业所获批项目的统计信息，包括不同级别的申报项目数量、项目级别分布、归口主管机构信息，以及近年项目获批趋势和补贴金额变化趋势。

**参数**:
- `matchKeyword` (必需): 匹配关键词 - 企业名称/注册号/统一社会信用代码/企业id
- `keywordType` (可选): 主体类型枚举 - name/nameId/regNumber/socialCreditCode

**返回值**:
- `count`: 总项目数量
- `ppeLevelStat`: 项目级别分布
  - `nationalProjectCount`: 国家级项目数量
  - `provincialProjectCount`: 省级项目数量
  - `municipalProjectCount`: 市级项目数量
  - `districtProjectCount`: 区级项目数量
- `ppeAgencyStat`: 项目归口分布
  - `agency`: 主管机构
  - `count`: 项目数量
- `ppeYearProjectStat`: 获批项目趋势
  - `year`: 年份
  - `count`: 项目数量
- `ppeYearAmountStat`: 补贴金额趋势
  - `year`: 年份
  - `amount`: 补贴金额

### 3. policy_bigdata_policy_info
**功能**: 政策详情查询

根据政策id查询特定政策的详细信息，包括发布机构、内容、相关项目、资助金额、申报时间等。

**参数**:
- `matchKeyword` (必需): 政策id

**返回值**:
- `pnTitle`: 政策标题
- `pnAgency`: 发布机构
- `pnType`: 政策类型
- `pnPublishDate`: 发布时间
- `pnRegion`: 发布地区
- `pnText`: 政策正文
- `pnOriginUrl`: 政策原文链接
- `pnFileList`: 附件列表
  - `filename`: 文件名称
  - `url`: 文件链接
- `relatedProjects`: 可能关联项目
  - `name`: 项目名称
  - `agency`: 主管机构
  - `maxGrantMount`: 资助金额（元）
  - `declaredLevel`: 项目级别（1：国家级，2：省级，3：市级，4：区级）
  - `projectProgress`: 项目进展（1：未开始，2：申报中，3：申报结束，4：已公示，5：已公开）
  - `declaredStartTime`: 申报开始时间
  - `declaredEndTime`: 申报结束时间
  - `region`: 地区

### 4. policy_bigdata_policy_search
**功能**: 政策搜索功能

根据关键词、政策类型和地区信息，检索符合条件的政策法规、申报指南或公示公告。

**参数**:
- `matchKeyword` (必需): 匹配关键词 - 政策法规/申报指南/公示公告关键词
- `pnType` (可选): 政策类型 - 全部/申报指南/公示公开/其他政策
- `agency` (可选): 发布机构
- `address` (可选): 地区 - 支持省份/城市/区县，格式：[["省份"],["省份","市","区县"]]
- `policyPubStartTime` (可选): 发布开始日期
- `policyPubEndTime` (可选): 发布结束日期
- `pageSize` (可选): 分页大小 - 一页最多获取50条数据
- `pageIndex` (可选): 页码 - 从1开始

**返回值**:
- `total`: 总数
- `resultList`: 结果列表
  - `pnId`: 政策id
  - `pnTitle`: 政策标题
  - `pnAgency`: 发布机构
  - `pnType`: 政策类型
  - `pnPublishDate`: 发布时间
  - `pnRegion`: 发布地区
  - `pnText`: 政策内容

## 使用场景

1. **政策申报**: 企业了解可申请的政策支持和补贴项目，寻找合适的申报机会
2. **合规评估**: 评估企业享受政策支持的合规性和项目执行情况
3. **投资分析**: 投资者评估企业的政策优势和获得政府支持的能力
4. **竞争分析**: 了解竞争对手的政策扶持情况和项目获批记录
5. **政府监管**: 政府部门监督政策执行效果和资金使用情况
6. **政策研究**: 研究特定行业或地区的政策趋势和支持力度
7. **商业咨询**: 为企业提供政策导向和申报建议

## 使用注意事项

1. **企业全称要求**: 在调用需要企业全称的接口时，如果没有企业全称则先调取policy_bigdata_fuzzy_search接口获取企业全称
2. **政策时效性**: 关注政策的有效期和执行时间，及时更新信息
3. **补贴金额**: 补贴金额可能为累计值或年度值，需注意统计口径
4. **政策变动**: 政策信息可能会发生变动，建议定期更新
5. **地区格式**: 地区查询需要按照指定格式输入，支持多级地区筛选
6. **项目级别**: 注意区分国家级、省级、市级、区级项目的不同申报条件

## 使用提问示例

### policy_bigdata_approved_project_stats (获批项目统计分析)
3. 华为获得了多少个政府补贴项目？都是什么级别的？
4. 比亚迪近年来的政府扶持项目趋势如何？补贴金额有多少？
5. 腾讯的政策扶持项目主要来自哪些主管机构？

### policy_bigdata_policy_info (政策详情查询)
6. 这个政策ID的具体内容是什么？有哪些申报要求？
7. 某个具体政策的资助金额和申报时间是什么时候？

### policy_bigdata_policy_search (政策搜索功能)
8. 最近有哪些针对新能源汽车的政策支持？
9. 深圳市今年发布了哪些科技创新相关的申报指南？
10. 有哪些国家级的人工智能产业扶持政策？