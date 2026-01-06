NaturalGAIA & LightManus: A Verifiable Benchmark & Hierarchical GUI Agent Framework<div align="center">用于长时序 GUI 任务的层次化多 Agent 框架与可验证评估基准</div>📖 简介 (Introduction)本项目包含论文 "NaturalGAIA: A Verifiable Benchmark and Hierarchical Framework for Long-Horizon GUI Tasks" 的官方实现与数据集。它由两部分核心组成：NaturalGAIA: 一个植根于真实人类意图的可验证评估数据集。它包含 1,780 个样本，通过解耦逻辑因果路径与语言叙述，严格模拟了具有认知非线性和上下文依赖性的自然人类意图。LightManus: 一个分层协作的 Agent 框架。LightManus 负责动态拓扑规划和上下文演化，而执行端（如 Jarvis, Mobile-Agent-E, PC-Agent）则通过混合视觉-结构感知确保执行精度。🌟 核心特性🗺️ 动态拓扑规划 (LightManus)：自动将复杂的长时序任务分解为原子任务序列，并管理上下文演化。📱 全平台支持：集成多种操作 Agent，覆盖移动端（Android）和桌面端（Windows/macOS/Linux）。Mobile-Agent-E: 针对移动端应用优化的自动化 Agent。PC-Agent: 针对桌面环境的自动化操作。Jarvis: 强大的 Android 设备控制与观察模块。✅ 可验证性 (Verifiability)：通过 AnswerValidationAgent 引入基于结果的验证机制，解决传统 GUI 评估中过程正确但结果未知的难题。📊 完整评估基准：提供 NaturalGAIA 数据集，支持对 Agent 在高保真真实环境下的表现进行量化评估。🏗️ 架构设计系统采用分层架构设计，确保规划与执行的解耦与高效协作：graph TD
    User[用户指令] --> Decomposer[TaskDecomposer (LightManus)]
    Decomposer -->|原子任务序列| Executor[TaskExecutionAgent]
    Executor -->|路由分发| Operator[TaskOperator]
    
    subgraph "Operation Agents (执行层)"
        Operator -->|Mobile Task| MAE[Mobile-Agent-E]
        Operator -->|Android Control| Jarvis[Jarvis Agent]
        Operator -->|Desktop Task| PC[PC-Agent]
    end
    
    MAE & Jarvis & PC -->|执行反馈| Executor
    Executor -->|最终结果| Validator[AnswerValidationAgent]
    Validator -->|验证报告| Result[最终输出]
🚀 快速开始1. 环境准备# 克隆项目
git clone [https://github.com/KeLes-Coding/NaturalGAIA.git](https://github.com/KeLes-Coding/NaturalGAIA.git)
cd NaturalGAIA

# 创建并激活 Conda 环境 (推荐)
conda create -n naturalgaia python=3.9
conda activate naturalgaia

# 安装核心依赖
pip install -r requirement.txt

# 安装子模块依赖 (根据需要选择)
pip install -r src/Agent/Operation_Agent/Mobile-Agent-E/requirements.txt
pip install -r src/Agent/Operation_Agent/PC-Agent/requirements.txt
2. 配置设置项目提供了一个配置迁移脚本，用于快速生成配置文件。# 从模板生成 config.yaml
python migrate_config.py
编辑根目录下的 config.yaml，填入您的 LLM API Key 和相关路径配置：# config.yaml 示例片段
lightmanus:
  # 任务加载路径
  task_loader:
    json_path: "task/0101.json"

  # 任务分解器模型配置
  task_decomposer:
    api_url: "[https://dashscope.aliyuncs.com/compatible-mode/v1/chat/completions](https://dashscope.aliyuncs.com/compatible-mode/v1/chat/completions)"
    api_key: "sk-xxxxxxxxxxxxxxxx"
    model: "qwen-vl-max"  # 推荐使用具备多模态能力的模型

  # 答案验证器配置
  answer_validator:
    api_url: "[https://dashscope.aliyuncs.com/compatible-mode/v1/chat/completions](https://dashscope.aliyuncs.com/compatible-mode/v1/chat/completions)"
    api_key: "sk-xxxxxxxxxxxxxxxx"
    model: "deepseek-v3"

# 各个 Agent 的开关与配置
mobile_agent_e:
  enabled: true
pc_agent:
  enabled: true
3. 准备任务数据在 task/ 目录下创建或修改 JSON 任务文件（参考 task/0101.json）。NaturalGAIA 基准格式如下：{
  "Task": "打开计算器，计算 123 乘以 456，并验证结果",
  "Task_ID": "0101",
  "Answer": "56088",
  "atomic_tasks": [
    {
      "atomic_tasks_ID": 1,
      "atomic_tasks_answer": "56088",
      "atomic_tasks_description": "打开计算器应用并计算 123 * 456"
    }
  ]
}
4. 运行框架启动 LightManus 主程序：python run_light_manus.py
程序将自动读取 config.yaml 中指定的任务文件，进行分解、执行和验证。📁 项目结构NaturalGAIA/
├── config.template.yaml        # 配置文件模板
├── config.yaml                 # 运行配置文件（由 migrate_config.py 生成）
├── migrate_config.py           # 配置初始化工具
├── run_light_manus.py          # 程序主入口
├── requirement.txt             # 核心依赖列表
├── NaturalGAIA_ACL_260106.pdf  # 相关论文
│
├── src/
│   ├── config_loader.py        # 配置加载模块
│   └── Agent/
│       ├── task_decompose_agent.py    # 任务分解 (Planner)
│       ├── task_execution_agent.py    # 任务执行调度
│       ├── task_operator_agent.py     # Agent 路由与操作
│       ├── answer_validation_agent.py # 结果验证
│       ├── task_roader.py             # 任务读取器
│       │
│       └── Operation_Agent/           # 具体执行 Agent 集合
│           ├── Mobile-Agent-E/        # 移动端 Agent 实现
│           │   ├── MobileAgentE/
│           │   ├── run.py
│           │   └── requirements.txt
│           │
│           └── PC-Agent/              # 桌面端 Agent 实现
│               ├── PCAgent/
│               ├── run_pc_agent.py
│               └── requirements.txt
│
└── task/                       # 任务数据与基准测试用例
    ├── 0101.json
    ├── 0208.json
    └── ...
🤖 支持的 Agent 详情Mobile-Agent-E (src/Agent/Operation_Agent/Mobile-Agent-E)专为移动环境设计的高效 Agent，具备以下能力：多模态感知：结合截图与 XML 布局信息。图标定位：icon_localization.py 和 text_localization.py 提供精确的 UI 元素定位。自我进化：支持通过 run_tasks_evolution.sh 进行策略演进。PC-Agent (src/Agent/Operation_Agent/PC-Agent)跨平台的桌面自动化解决方案：跨平台支持：同时包含 pywin.py (Windows) 和 pymac.py (macOS) 实现。视觉增强：利用 OCR 和视觉大模型进行屏幕理解和操作。📊 NaturalGAIA Benchmark本项目提供的基准测试旨在衡量 Agent 在真实世界复杂任务中的表现。主要评估指标包括：WPSR (Weighted Pathway Success Rate)：加权路径成功率，不仅考量最终结果，还考量执行路径的正确性。SR (Success Rate)：任务完成成功率。要运行基准测试集，请将配置文件中的 task_loader 指向 task/ 目录下的相应测试集文件。🤝 引用如果您在研究中使用了 NaturalGAIA 或 LightManus，请引用我们的论文：@article{naturalgaia2026,
  title={NaturalGAIA: A Verifiable Benchmark and Hierarchical Framework for Long-Horizon GUI Tasks},
  author={Anonymous},
  journal={ACL Submission},
  year={2026}
}
📄 许可证本项目采用 MIT License 授权。