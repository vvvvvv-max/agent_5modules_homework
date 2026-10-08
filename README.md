# 智能体 5 模块架构图与场景分析作业

## 一、作业目标
1. 巩固智能体核心功能模块（LLM、Perception、Memory、Planning、Tools、Action）的概念。
2. 体验用 AI 编程工具辅助开发全流程。
3. 完成对应 PPT 章节的实战练习。

## 二、实验内容与产出
*   **agent_5modules_diagram.md**：包含 Mermaid 格式的智能体 5 模块架构图，标注了输入、输出及数据流方向。
*   **scenario_analysis.md**：以“湛江现在能下海游泳吗？”为题，详细分析了 5 模块的协作流程，并对比了去掉任一模块的影响。
*   **weather_agent.ipynb**：实现了调用真实天气 API 并让 LLM 给出智能建议的完整 Python 代码。

## 三、AI 协作记录 (AI Collaboration Disclosure)
*   **使用的 AI 工具**：DeepSeek (V3)
*   **AI 生成的代码/文本**：
    *   `weather_agent.ipynb` 中的 API 请求逻辑、异常处理框架及 LLM 调用代码。
    *   `agent_5modules_diagram.md` 中的 Mermaid 代码逻辑及样式。
    *   `scenario_analysis.md` 中的场景分析结构框架和部分文案润色。
*   **我修改/补充的部分**：
    *   根据实际 API 返回结果，调整了 `weather_info` 字典的字段名（如 `windspeedKmph`），确保代码能正确解析 JSON。
    *   增加了针对 `KeyError` 和 `Timeout` 的异常捕获，提高了代码健壮性。
    *   结合实验代码（`weather_resp` 的 JSON 结构），在场景分析中细化了工具模块返回的具体字段，使其更贴合实际运行结果。
    *   补充了 >1500 字的场景分析，详细对比了去掉每个模块的影响。
*   **使用的 Prompt 示例**：
    *   "请帮我写一段 Python 代码，调用 wttr.in API 获取湛江天气，并使用 DashScope API 让 LLM 判断是否适合游泳。要求包含异常处理和 .env 加载。"
    *   "生成一个 Mermaid 架构图，LLM 居中，周围环绕 Perception, Memory, Planning, Tools, Action 五个模块，并标注数据流方向。"

## 四、总结与反思
通过本次实验，我掌握了在 VS Code 中结合 Jupyter Notebook 和 AI 工具进行开发的方法。在实际运行中，我发现网络请求的超时设置非常关键，否则程序容易卡死。同时，我深刻理解了 Tools 模块对于智能体获取实时信息的重要性，没有 Tools 的 LLM 只能凭空想象，无法解决“现在能不能游泳”这种强实时性的问题。