# 智能体 5 模块架构图

```mermaid
graph TD
    %% 定义样式
    classDef llm fill:#f9f,stroke:#333,stroke-width:4px;
    classDef module fill:#bbf,stroke:#333,stroke-width:2px;
    classDef action fill:#afa,stroke:#333,stroke-width:2px;

    %% 核心 LLM
    LLM(("LLM 大语言模型<br/>(大脑/推理)")):::llm

    %% 五大核心模块
    Perception[" Perception 感知模块<br/>(输入: 用户Query/环境数据)<br/>(输出: 结构化意图)"]:::module
    Memory[" Memory 记忆模块<br/>(输入: 历史交互/外部知识)<br/>(输出: 上下文/长期记忆)"]:::module
    Planning[" Planning 规划模块<br/>(输入: 目标/上下文)<br/>(输出: 任务拆解步骤)"]:::module
    Tools[" Tools 工具模块<br/>(输入: API调用/搜索需求)<br/>(输出: 实时数据/执行结果)"]:::module
    Action[" Action 行动模块<br/>(输入: 最终决策/执行指令)<br/>(输出: 用户回复/系统操作)"]:::action

    %% 数据流向交互
    Perception -->|"1. 解析意图与实体<br/>(数据流)"| LLM
    Memory <-->|"2. 读写上下文与历史<br/>(双向数据流)"| LLM
    LLM -->|"3. 下发任务目标<br/>(数据流)"| Planning
    Planning -->|"4. 反馈拆解步骤<br/>(数据流)"| LLM
    LLM -->|"5. 请求调用工具<br/>(数据流)"| Tools
    Tools -->|"6. 返回实时数据<br/>(数据流)"| LLM
    LLM -->|"7. 输出最终指令<br/>(数据流)"| Action

    %% 交互关系
    Planning -.->|"指导"| Tools
    Tools -.->|"影响"| Action

    
#### 2. 文件：`scenario_analysis.md` (场景分析)

```markdown
# 场景分析：以“湛江现在能下海游泳吗？”为例

## 一、场景背景与用户需求
用户向智能体发送自然语言提问：“湛江现在能下海游泳吗？”
这是一个典型的**多跳推理（Multi-hop Reasoning）**任务。用户不仅仅是在问“湛江的天气”，而是在询问“基于天气、水温、风浪等综合因素，当前是否适合下海游泳”。纯 LLM 只能给出“夏季通常可以”这种通用且无实时数据的回答。而完整的智能体则能调用实时数据给出精准建议。

## 二、五大核心模块的协作流程
*   **第一步：感知模块 (Perception) 接收输入**
    *   **输入**：用户 Query “湛江现在能下海游泳吗？”
    *   **处理**：解析出关键实体 `Location: 湛江`，`Time: 现在`，`Intent: 评估游泳适宜性`。
    *   **输出**：结构化数据 `{ "city": "Zhanjiang", "intent": "swimming_check" }` 传递给 LLM。

*   **第二步：记忆模块 (Memory) 提供上下文**
    *   **输入**：LLM 请求历史记录和背景知识。
    *   **处理**：调取长期记忆（如：湛江属于亚热带季风气候），并检查短期记忆。
    *   **输出**：提供背景知识 `{"climate": "subtropical"}`，帮助 LLM 建立评估标准。

*   **第三步：规划模块 (Planning) 拆解任务**
    *   **输入**：LLM 接收到感知和记忆信息，发现信息不足。
    *   **处理**：将任务拆解为：① 查询湛江实时天气；② 查询海水温度；③ 综合评估。
    *   **输出**：任务列表 `["get_weather(Zhanjiang)", "get_sea_temp(Zhanjiang)"]` 返回给 LLM。

*   **第四步：工具模块 (Tools) 执行数据获取**
    *   **输入**：LLM 决定调用 `wttr.in` API。
    *   **处理**：发起 HTTP GET 请求：`https://wttr.in/Zhanjiang?format=j1`。
    *   **输出**：返回实时 JSON 数据（如风速 22km/h、体感温度 28℃）。

*   **第五步：LLM 综合评估与行动模块 (Action) 输出**
    *   **输入**：LLM 接收工具返回的实时数据。
    *   **处理**：LLM 结合评估标准进行推理：当前气温适宜，但风速偏高（22km/h），影响游泳安全。
    *   **输出**：行动模块将结果格式化输出给用户：“风速较高，不建议下海游泳，建议选择风力较小的时段。”

## 三、模块去中心化影响分析（加分项）
*   **去掉感知模块 (Perception)**：无法识别“湛江”和“游泳”意图，无法精准调用天气 API。
*   **去掉记忆模块 (Memory)**：每次对话独立，无法记住之前的评估标准（如风速阈值）。
*   **去掉规划模块 (Planning)**：LLM 可能直接调用天气 API 并直接回答，忽略了综合评估的步骤。
*   **去掉工具模块 (Tools)**：完全失去实时性，只能回答“夏季通常适合游泳”。
*   **去掉行动模块 (Action)**：无法将结果输出给用户，失去闭环。

## 四、总结
在这个场景中，5 大模块缺一不可。LLM 作为核心大脑，协调感知、记忆、规划和工具模块，将单纯的“聊天”升级为具有实时数据支撑的“智能决策”。