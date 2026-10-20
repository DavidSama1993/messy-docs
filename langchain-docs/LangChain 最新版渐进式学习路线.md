# LangChain 最新版渐进式学习路线

# LangChain 最新版渐进式学习路线（基于官方 Python 文档 v1 / v0\.3\+）

> 重要更新说明：
> 
> 1. 官方现状：LangChain Python v1 核心架构**以 LangGraph 为 Agent 底层**；旧版 `AgentExecutor` 官方标记为遗留实现，新项目不再推荐优先使用。
> 
> 2. 包结构：模块化拆分，每个模型厂商独立包，`init_chat_model` 是官方推荐统一模型入口函数。
> 
> 3. 依赖要求：最低 Python \>= 3\.9，Pydantic 2（v0\.3\+ 移除 Pydantic 1 兼容层）。
> 
> 4. 学习主线：Runnable/LCEL（现代编排核心）→ RAG → LangGraph Agent（官方首选 Agent 方案）。
> 
> 5. 环境：全程优先云端 API（DeepSeek），无需 conda、无需 PyTorch，本地模型属于可选拓展。
> 
> 6. 废弃内容提示：旧版 `Chain`、老式 `Memory` 类仅作了解，新项目优先 LCEL \+ `RunnableWithMessageHistory` / LangGraph Checkpointer。
> 
> 

## 总学习周期参考

- 阶段 1：基础环境与模型抽象：2\~3 天

- 阶段 2：Prompt \& Message 消息体系：2\~3 天

- 阶段 3：LCEL Runnable 核心（重中之重）：4\~5 天

- 阶段 4：对话记忆（RunnableWithMessageHistory）：2\~3 天

- 阶段 5：RAG 检索增强生成（生产高频场景）：5\~7 天

- 阶段 6：LangGraph 智能体（官方推荐 Agent，替代 AgentExecutor）：6\~8 天

- 阶段 7：可观测性、异常处理、链路调试：2\~3 天

- 阶段 8：工程化、API 服务、生产最佳实践：3\~5 天

- 阶段 9：高级拓展：Deep Agents、多智能体、评估体系：自选

---

## 阶段 1：LangChain 现代架构认知 \+ 环境搭建

### 学习目标

理解 LangChain 现代架构，搭建隔离环境，掌握 `init_chat_model` 统一模型初始化，跑通第一个 LLM 调用 Demo。

### 1\.1 核心概念（新版官方定义）

1. LangChain 定位：**大模型应用组件编排框架，不自带大模型推理能力**。

2. 包分层（新版模块化）：

    - `langchain-core`：Runnable、消息对象、提示词基础抽象，所有包底层依赖

    - `langchain`：高层 API、Agent 预制函数、应用层组件

    - `langchain-xxx`：模型厂商集成包（`langchain-deepseek` / `langchain-openai`），每个模型单独安装

    - `langgraph`：状态图、Agent 运行时、Checkpointer 持久化，Agent 必备独立包

    - `langsmith`：链路追踪、评测 SDK

3. 核心抽象：

    - `ChatModel`：对话大模型（优先使用，支持 tool calling）

    - `Embeddings`：文本向量化模型

    - `Runnable`：统一可执行单元（LCEL 一切皆 Runnable）

    - `Retriever`：检索器抽象

    - `Tool`：工具抽象，供 Agent 调用

4. 版本重要提醒：

    - Python \>= 3\.9；Pydantic v2；不再支持 Python 3\.8

    - 旧版 `AgentExecutor`：仅用于学习理解原理，**新项目优先 LangGraph ****`create_agent`**

### 1\.2 环境准备（推荐 venv，不强制 conda）

```bash
# 创建虚拟环境
python -m venv .venv
# linux/mac
source .venv/bin/activate
# windows
.venv\Scripts\activate

# 基础依赖（最新推荐）
pip install -U langchain langchain-core langchain-deepseek langgraph langsmith python-dotenv chromadb
```

新建 `.env` 文件存放密钥，禁止硬编码：

```env
DEEPSEEK_API_KEY=sk-xxxxxxxxxxxxxxxxxxxx
LANGSMITH_API_KEY=ls_xxxx
LANGSMITH_TRACING=true
```

### 1\.3 Demo：init\_chat\_model 初始化 DeepSeek（官方推荐写法）

```python
from langchain.chat_models import init_chat_model
from dotenv import load_dotenv
import os

load_dotenv()

llm = init_chat_model(
    model="deepseek-chat",
    model_provider="deepseek",
    temperature=0.2,
    max_tokens=1024
)

resp = llm.invoke("简单介绍 LangChain LCEL")
print(resp.content)
```

### 1\.4 接口基础：Runnable 标准接口（所有 Runnable 通用）

- `invoke(input)`：单次同步调用

- `stream(input)`：流式逐块返回

- `batch(inputs)`：批量调用

- `ainvoke / astream / abatch`：异步版本，服务端开发优先

- `with_config()`：配置回调、metadata、会话参数

- `with_fallbacks()`：模型降级容错

### 1\.5 阶段练习任务

1. 读取 `.env` 密钥，成功调用 DeepSeek；分别测试 invoke / stream

2. 观察返回对象 `AIMessage`：`content` / `tool_calls` / `response_metadata`

3. 修改模型参数 temperature、max\_tokens，观察输出变化

### 1\.6 验收标准

能够使用 `init_chat_model` 初始化模型，看懂消息对象，掌握流式输出基础用法。

---

## 阶段 2：Prompt \+ Message 消息体系

### 学习目标

掌握消息模型、提示模板、消息占位符；理解现代 LangChain 消息对象体系，这是 RAG、Agent 的底层基础。

### 2\.1 消息类型（langchain\_core\.messages）

- `SystemMessage`：系统角色指令

- `HumanMessage`：用户消息

- `AIMessage`：模型输出消息（包含 tool\_calls 字段）

- `ToolMessage`：工具执行返回结果，Agent 工具调用必需

- `BaseMessage`：所有消息基类

### 2\.2 Prompt 模板

1. `PromptTemplate`：纯文本字符串模板（简单场景）

2. `ChatPromptTemplate`：消息列表模板，**对话、Agent 场景首选**

3. `MessagesPlaceholder(variable_name)`：消息列表占位符，用于插入对话历史 / agent scratchpad

4. `.partial()`：预先固化部分模板变量，减少调用时传参

### 2\.3 最小示例

```python
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder

prompt = ChatPromptTemplate.from_messages([
    ("system", "你是专业 LangChain 讲师，回答简洁"),
    MessagesPlaceholder("history"),
    ("user", "{query}")
])

formatted = prompt.format_messages(history=[], query="什么是 Runnable")
print(formatted)
```

### 2\.4 阶段练习任务

1. 编写 ChatPromptTemplate，使用 MessagesPlaceholder 插入历史消息

2. 使用 partial 固化 system prompt

3. 区分 `format()`（字符串）和 `format_messages()`（消息对象列表）

### 2\.5 验收标准

可以动态组装消息列表，理解占位符和消息占位符的区别。

---

## 阶段 3：LCEL LangChain Expression Language（核心重中之重）

> LCEL 是 LangChain 现代应用的编排语法，**所有组件都是 Runnable**；官方推荐优先 LCEL 构建无环 DAG 链路。
> 
> 

### 3\.1 LCEL 核心原语

1. **RunnableSequence**：`|` 管道串行。上一级输出作为下一级输入；耗时累加 tA \+ tB

2. **RunnableParallel（别名 RunnableMap）**：同一个输入，并行分发到多个子 Runnable；输出字典；耗时 ≈ max\(tA, tB\)

    > LCEL 语法糖：`|` 后直接写 Python 字典，会**自动隐式转为 RunnableParallel**，RAG 标准写法
    > 
    > 

3. **RunnableLambda**：自定义 Python 函数包装为 Runnable，嵌入 LCEL 链路做自定义逻辑

4. **RunnablePassthrough**：透传输入，支持增加/修改字段；重组字典，RAG 高频使用

### 3\.2 RunnableParallel 示例（RAG 经典分叉）

```python
from langchain_core.runnables import RunnableLambda, RunnablePassthrough
from langchain.chat_models import init_chat_model
from langchain_core.prompts import ChatPromptTemplate
from dotenv import load_dotenv
load_dotenv()

llm = init_chat_model("deepseek-chat", model_provider="deepseek")

# 模拟检索器
retriever = RunnableLambda(lambda q: ["文档片段：RunnableParallel 介绍"])

rag_chain = (
    {"context": retriever, "question": RunnablePassthrough()}
    | ChatPromptTemplate.from_template("基于{context}回答问题：{question}")
    | llm
)

res = rag_chain.invoke("什么是 RunnableParallel")
print(res.content)
```

### 3\.3 LCEL 高级能力

- 链路流式输出，逐块返回字典片段（RunnableParallel 支持 stream）

- `with_fallbacks`：链路降级，模型调用失败自动切换备选模型

- `with_retry`：重试机制

- 自定义回调，埋点耗时统计

### 3\.4 阶段练习任务

1. 串行管道：ChatPromptTemplate \| llm

2. 并行 RunnableParallel 多路执行，输出字典

3. RunnablePassthrough 实现字段透传、字典重组

4. 编写异步 astream 流式链路

### 3\.5 验收标准

区分 Sequence 与 Parallel；独立编写 RAG 分叉链路；掌握 RunnablePassthrough。

> 本章节是分水岭，RAG、Agent 全部建立在 LCEL 之上。
> 
> 

---

## 阶段 4：对话记忆（现代官方推荐方案）

### 学习目标

理解对话持久化两种方案：轻量 `RunnableWithMessageHistory`（LCEL 对话）；复杂场景使用 LangGraph Checkpointer。

> 旧版 `ConversationBufferMemory` 等类属于遗留 API，仅做了解，新项目优先下面两种方案。
> 
> 

### 4\.1 两种记忆方案选型

1. **RunnableWithMessageHistory**：适合简单多轮对话，基于 LCEL，独立会话存储（`BaseChatMessageHistory`），不需要 LangGraph

2. **LangGraph Checkpointer（MemorySaver）**：Agent / 长对话首选，状态持久、断点回溯、支持多会话，官方主推长期方案

### 4\.2 RunnableWithMessageHistory 示例（LCEL 多轮对话）

```python
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_core.runnables.history import RunnableWithMessageHistory
from langchain_community.chat_message_histories import InMemoryChatMessageHistory
from langchain.chat_models import init_chat_model
from dotenv import load_dotenv
load_dotenv()

llm = init_chat_model("deepseek-chat", model_provider="deepseek")

prompt = ChatPromptTemplate.from_messages([
    ("system", "你是聊天助手"),
    MessagesPlaceholder("history"),
    ("user", "{input}")
])

chain = prompt | llm

# 会话存储
store = {}
def get_history(session_id: str):
    if session_id not in store:
        store[session_id] = InMemoryChatMessageHistory()
    return store[session_id]

chain_with_history = RunnableWithMessageHistory(
    chain,
    get_history,
    input_messages_key="input",
    history_messages_key="history"
)

# 调用
res = chain_with_history.invoke(
    {"input": "你好"},
    config={"configurable": {"session_id": "sess_001"}}
)
print(res.content)
```

### 4\.3 阶段练习任务

1. 实现多会话隔离对话

2. 切换不同会话 id，验证会话独立

3. 了解：生产可替换为 Redis / Postgres 消息存储

### 4\.4 验收标准

基于 LCEL 搭建带会话隔离的多轮对话，区分两种记忆方案适用场景。

---

## 阶段 5：RAG 检索增强生成（生产落地最主流场景）

### 学习目标

完整掌握现代 RAG 全链路，理解每一步作用，基础 RAG 搭建，掌握官方推荐的检索优化方案。

> RAG 链路：文档加载 → 文本切分 → Embedding 向量化 → 向量库存储 → 检索器 Retriever → LCEL 组装上下文 → LLM 生成
> 
> 

### 5\.1 RAG 组件

1. Document Loaders：文档加载器（txt/md/pdf 等；pdf 推荐 PyPDFLoader）

2. TextSplitter：`RecursiveCharacterTextSplitter`（官方默认首选）；chunk\_size、chunk\_overlap 调参

3. Embeddings：文本转向量；支持云端 Embedding API（推荐，无需本地 GPU）、本地 Embedding（可选）

4. VectorStore：向量库；Chroma 本地开发，生产 PGVector / Pinecone

5. Retriever：向量库 `.as_retriever()`；支持相似度检索、MMR 多样性检索

### 5\.2 RAG 进阶优化（官方推荐优化方向）

1. 分块策略优化：语义分块、句子窗口检索

2. 查询改写：Multi\-Query、HyDE（假设文档嵌入）

3. 多路并行检索（RunnableParallel 同时查询多个检索源）

4. Rerank 重排：检索结果二次排序，过滤无关文档

5. 上下文压缩：过滤冗余片段，降低 token 消耗

### 5\.3 阶段练习任务

1. 最小 RAG Demo：加载 md 文档，切片存入 Chroma，检索问答

2. 使用 RunnableParallel 多路并行检索

3. 增加 HyDE 查询改写优化召回效果

### 5\.4 验收标准

独立搭建完整 RAG 链路；能够定位幻觉、召回不足问题；能调优 chunk 与检索参数。

---

## 阶段 6：LangGraph 智能体 Agent（官方新版推荐，重点）

> 官方重要声明：新项目**不再推荐 AgentExecutor**，优先使用 LangGraph 构建 Agent；LangChain v1 内置 `create_agent`（前身 `create_react_agent`）快速搭建 ReAct Agent，底层基于 LangGraph 状态图，自带持久化、human\-in\-loop、流式、断点回溯能力。
> 
> 

### 6\.1 核心概念

1. Tool：使用 `@tool` 装饰器定义，自动生成 Pydantic 参数 schema，适配模型原生 tool calling

2. Tool Calling：DeepSeek、GPT 等原生支持函数调用，优先原生 tool calling，不推荐字符串解析 ReAct

3. StateGraph：LangGraph 状态图，定义状态结构、节点、边、条件分支

4. Checkpointer：状态持久化，`MemorySaver` 内存持久化，生产 Redis/Postgres

5. `create_agent`：LangChain v1 预制 Agent，开箱即用 ReAct 循环，替代旧版 AgentExecutor

### 6\.2 快速上手预制 Agent 示例（create\_agent）

```python
from langchain.chat_models import init_chat_model
from langchain_core.tools import tool
from langchain.agents import create_agent
from langgraph.checkpoint.memory import MemorySaver
from dotenv import load_dotenv
load_dotenv()

llm = init_chat_model("deepseek-chat", model_provider="deepseek")

@tool
def calculator(a: float, b: float, op: str) -> float:
    """计算器，支持加减乘除
    Args:
        a: 第一个数字
        b: 第二个数字
        op: 运算符 + - * /
    """
    match op:
        case "+": return a + b
        case "-": return a - b
        case "*": return a * b
        case "/": return a / b
        case _: raise ValueError("不支持运算符")

tools = [calculator]
checkpointer = MemorySaver()
agent = create_agent(model=llm, tools=tools, checkpointer=checkpointer)

config = {"configurable": {"thread_id": "thread_001"}}
resp = agent.invoke(
    {"messages": [("human", "123 * 456 等于多少")]},
    config=config
)
print(resp["messages"][-1].content)
```

### 6\.3 LangGraph 进阶（自定义状态图）

1. 手动编写 StateGraph，自定义状态字段

2. 自定义节点：LLM 节点、工具节点、条件边（判断是否继续调用工具）

3. Human\-in\-the\-loop：工具调用前人工审核确认

4. 多 Agent 协作：规划 Agent、检索 Agent、评审 Agent，子图 SubGraph

### 6\.4 Agent \+ RAG 组合

将 RAG 检索封装为自定义 Tool，Agent 自主决定何时检索文档。

### 6\.5 阶段练习任务

1. 预制 Agent：开发计算器工具 Agent，观察 tool calling 循环

2. 自定义 StateGraph 从零手写 Agent 循环（理解底层原理）

3. 接入 MemorySaver，验证对话状态持久化、断点续跑

4. 实现 Human\-in\-loop 人工确认节点

### 6\.6 验收标准

- 区分旧 AgentExecutor 与 LangGraph Agent 优劣与适用边界

- 掌握预制 `create_agent` 快速开发

- 能从零编写简单 StateGraph，理解状态、节点、条件边

---

## 阶段 7：可观测性、异常处理、链路调试

### 学习目标

学会调试 RAG/Agent 链路，捕获异常，接入 LangSmith 追踪，定位幻觉、工具调用失败、召回问题。

### 7\.1 LangSmith（官方追踪平台）

- 自动追踪每一条 Runnable 输入输出、耗时、token 消耗、工具调用栈

- 链路可视化、标签管理、数据集评测、Prompt A/B 测试

- 开启方式：环境变量 `LANGSMITH_TRACING=true` \+ `LANGSMITH_API_KEY`

### 7\.2 容错与异常

1. `with_fallbacks()`：模型降级

2. `with_retry()`：自动重试网络异常

3. RunnableLambda 内部 try\-except 捕获子任务异常（RunnableParallel 单个子任务异常会导致整体失败，必须子任务内捕获）

4. Tool 内部异常捕获、自定义错误返回给大模型

### 7\.3 练习任务

1. 项目开启 LangSmith，查看 RunnableParallel、Agent 完整 trace

2. 给链路增加 fallback 降级策略

3. 给工具增加异常捕获，防止 Agent 流程崩溃

### 7\.4 验收标准

可以使用 LangSmith 定位链路问题，给链路增加重试、降级容错。

---

## 阶段 8：工程化与生产部署

### 学习目标

把 LCEL / LangGraph 应用封装为线上服务，处理并发、密钥、异步、容器化。

### 8\.1 工程最佳实践

1. 密钥管理：环境变量 / 密钥管理服务，禁止代码硬编码 API Key

2. 异步优先：`ainvoke` / `astream`，高并发后端服务必须异步

3. 服务封装：FastAPI 封装 LangGraph/LCEL 链路，实现流式 SSE 接口

4. 向量库选型：Chroma 仅本地开发；生产 PGVector / Pinecone

5. Checkpointer 生产持久化：Postgres / Redis Checkpointer，不要使用 MemorySaver 上线

6. 限流、token 统计、输入内容安全校验

### 8\.2 练习任务

1. FastAPI 封装 RAG 或 Agent，对外提供流式接口

2. Dockerfile 打包 Python 应用

### 8\.3 验收标准

将 LangGraph/LCEL 应用封装为后端 API，支持流式响应，理解生产环境组件选型。

---

## 阶段 9：高级拓展（自选）

### 9\.1 进阶方向

1. Deep Agents：LangGraph 官方深度智能体，内置规划、文件沙箱、子 Agent，适合复杂长周期任务

2. 结构化输出：`with_structured_output`，Pydantic 模型约束 LLM 输出结构化 JSON

3. Agent 评估框架：RAGAS 评测 RAG；LangSmith 评测 Agent

4. 本地模型集成：vLLM \+ LangChain（需要 PyTorch/CUDA，私有化场景）

5. 多模态 Agent：图像输入，多模态模型工具调用

### 9\.2 架构选型决策表

|场景|推荐方案|
|---|---|
|简单问答、无环 RAG 链路|LCEL|
|带循环、工具调用、多轮 Agent|LangGraph create\_agent|
|长对话、会话持久化、人工介入|LangGraph \+ Checkpointer|
|简单多轮对话（无工具）|LCEL \+ RunnableWithMessageHistory|

### 9\.3 常见坑（新版官方文档重点提示）

1. RunnableParallel：任意子 Runnable 抛出异常会整体失败；异常捕获必须写在子 Runnable 内部

2. Tool 调用失败：优先优化 tool 的 docstring 描述，保证参数 schema 清晰

3. Token 超限：长对话使用 LangGraph Checkpointer \+ 消息截断/摘要策略

4. 幻觉问题：RAG 优化召回质量、重排、上下文压缩，不要只依赖提示词

5. LangGraph 状态：消息列表持续膨胀，生产需要消息窗口裁剪

---

## 附录：官方资源清单

### 官方文档链接

1. LangChain Python 官方文档：https://python\.langchain\.com/docs/introduction/

2. LCEL 教程：https://python\.langchain\.com/docs/concepts/lcel/

3. LangGraph 官方文档：https://langchain\-ai\.github\.io/langgraph/

4. Agent 迁移指南（AgentExecutor 迁移 LangGraph）：https://python\.langchain\.com/docs/how\_to/migrate\_agent/

### 官方仓库

1. LangChain Cookbook：大量最新可运行示例（RAG、LangGraph Agent）

2. LangGraph Cookbook：多 Agent、Human\-in\-loop 样例

### 学习自检清单（完成全部阶段后自查）

* [ ] LCEL 管道、RunnableParallel、RunnablePassthrough 原理与编码

* [ ] ChatPromptTemplate、MessagesPlaceholder、消息对象体系

* [ ] RunnableWithMessageHistory 多会话对话

* [ ] RAG 完整链路与检索优化手段

* [ ] LangGraph create\_agent 工具调用 Agent 开发

* [ ] 自定义 StateGraph，节点、条件边、Checkpointer 持久化

* [ ] LangSmith 链路追踪调试

* [ ] FastAPI 封装 LangGraph 服务，流式输出

* [ ] 区分旧版 AgentExecutor 与 LangGraph 适用场景

---

## 项目目录模板

```text
langchain-study/
├── .env
├── requirements.txt
├── chapter01_model_init/
├── chapter02_prompt_message/
├── chapter03_lcel_runnable/
├── chapter04_memory/
├── chapter05_rag/
├── chapter06_langgraph_agent/
├── chapter07_observability/
├── chapter08_production_api/
└── chapter09_advanced/
```

## requirements\.txt

```text
langchain>=1.0
langchain-core>=0.3
langchain-deepseek
langgraph>=1.0
langsmith
python-dotenv
chromadb
pydantic>=2.0
fastapi
uvicorn
```

> （注：部分内容由豆包工作 AI 生成）
