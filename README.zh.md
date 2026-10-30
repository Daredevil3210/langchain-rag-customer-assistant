[English](README.md) | **简体中文**

# LangChain RAG Customer Assistant

基于 LangChain 与 Milvus 的客服知识库学习案例。使用本地 `knowledge.txt` 构建向量知识库，通过硅基流动嵌入模型检索相关片段，再将问题和上下文交给聊天模型生成回答。

本仓库整理自用户提供的尚硅谷 LangChain 教程案例，仅包含客服知识库 Notebook、知识文本和完整依赖清单及运行文档。知识文本中的产品、套餐和退款规则用于示例演示，不作为实际服务承诺。

Agent 没有绑定工具；`generate_answer(query)` 先显式检索，再把问题和上下文传入 Agent。Notebook 会打印检索片段、来源元数据、相似度分数与最终回答。这是学习 Notebook，并非已部署的客服服务。

## 文件结构

```text
langchain-rag-customer-assistant/
├── README.md
├── LICENSE
├── .gitignore
├── .env.example
├── knowledge.txt
├── requirements_full.txt
├── chapter10-RAG/
│   └── 05-案例：Atguigu Assistant客服知识库.ipynb
└── releases/
    └── v0.1.0.md
```

## 环境准备

1. 使用 Python 3.13 环境，在仓库根目录安装依赖：

```bash
python -m pip install -r requirements_full.txt
```

完整依赖清单保留了原教程的版本约束，也包含其他章节使用的组件，并非本案例的最小依赖集。本次发布未重新安装全部依赖进行兼容性验证。

2. 启动支持数据库操作的 Milvus 服务。Notebook 默认连接 `http://localhost:19530`；如果部署在其他地址，请修改 `MILVUS_URI`。

3. 复制配置模板，在本地填入自己的 API Key 和平台提供的 OpenAI 兼容接口地址：

```bash
cp .env.example .env
```

Windows PowerShell 可使用 `Copy-Item .env.example .env`。`.env` 已由 Git 忽略，真实凭据不包含在仓库中。

- `SILICONFLOW_API_KEY` / `SILICONFLOW_BASE_URL`：用于 `Pro/BAAI/bge-m3` 文本嵌入。
- `CLOSEAI_API_KEY` / `CLOSEAI_BASE_URL`：用于通过 `init_chat_model()` 初始化聊天模型；示例模型名称为 `gpt-5.4-mini`，请按实际账号支持情况调整。

在线嵌入和聊天模型调用需要网络连接，并可能产生服务费用。

## 运行案例

从 Notebook 所在目录启动 Jupyter，确保 `../knowledge.txt` 能正确定位：

```bash
cd chapter10-RAG
jupyter notebook
```

打开客服知识库 Notebook，按单元格顺序运行：

1. 配置 Milvus 地址、数据库、集合名称和知识文本路径。
2. 创建数据库和集合，使用 1024 维向量及 `COSINE` 相似度。
3. 初始化嵌入模型，加载知识文本，按 `chunk_size=200`、`chunk_overlap=80` 切分。
4. 生成向量，将正文、来源和片段编号等记录 upsert 到 Milvus，并执行 flush。
5. 初始化聊天模型和 Agent，将用户问题向量化并检索相关片段。
6. 将问题与检索上下文传入 Agent，输出回答。

默认数据库为 `rag_tutorial`，集合为 `docs`。初始化集合的单元格会删除并重建同名集合，请仅在专用练习数据库中运行；已有数据需要保留时，应先修改数据库或集合名称。

示例问题涉及七天退款被拒的情况。`generate_answer()` 最多检索 5 个片段，独立 `retrieve()` 函数默认检索 3 个；更换嵌入模型时，向量维度须与集合一致。

## 整理与验证

- 修正 `generate_answer()` 中的参数，将实际问题 `query` 传入 `retrieve()`。
- 清空 Notebook 历史输出和执行元数据，原始附件保留不变。
- 将 `requirements_full.txt` 从 UTF-16 转为 UTF-8，保留依赖版本与说明。
- 检查 Notebook 格式、代码语法、本地素材引用和发布文件中的常见敏感信息。
- 使用模拟嵌入模型、Milvus 和 Agent 检查问题、检索结果与生成提示词的传递。
- 未连接真实 Milvus 或调用在线模型进行端到端验证。

以上是原版本的整理与验证记录。本次文档更新未执行仓库脚本、安装依赖或调用在线服务；仓库未包含独立自动化测试套件。

首个版本的详细说明见 [v0.1.0 发布说明](releases/v0.1.0.md)。

## 许可证与来源

本仓库新增的整理内容采用 [MIT License](LICENSE)。原教程代码与知识文本的权利归原权利人所有，第三方内容的使用和再分发仍须遵循其原有授权。
