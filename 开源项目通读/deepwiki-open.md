# Deepwiki-open

## 仓库地址
https://github.com/AsyncFuncAI/deepwiki-open

## 项目概览
DeepWiki-Open 是一个基于AI的自动化文档生成工具，它能够自动为任何GitHub、GitLab或BitBucket代码仓库创建交互式的Wiki文档。

## 核心功能

- **自动代码分析**：分析代码结构和关系
- **智能文档生成**：使用AI生成comprehensive文档
- **可视化图表**：自动生成Mermaid图表解释架构和流程
- **RAG问答系统**：支持与代码库对话
- **深度研究**：多轮研究过程深入分析复杂主题
- **私有仓库支持**：通过个人访问令牌访问私有仓库

## 工作流程

1. 用户输入配置

    用户在前端界面提供目标代码仓库的 Git URL，并可以选择使用的大语言模型（如 GPT-4, Gemini, Ollama）和相关参数。

2. 代码仓库准备

    后端接收到请求后，首先需要获取代码仓库的完整内容。它会检查本地是否已有该仓库的缓存，如果没有，则从远程 Git URL 克隆到本地。

3. 代码解析与分块

    将整个代码仓库的源文件解析成结构化的文本数据，并分割成适合大语言模型处理的小块（Chunks）。

4. 向量化与索引

    将上一步得到的文本块转换为向量（Embeddings），并构建一个向量索引，以便进行快速的语义相似度搜索。这是 RAG（检索增强生成）的核心。

5. 检索与上下文构建

    当用户提出问题（如“生成技术文档”）时，系统将这个问题也进行向量化，并使用这个向量在第四步构建的索引中进行搜索。

6. 生成文档

    将用户原始问题和上一步检索到的相关代码块（上下文）一起打包，形成一个完整的提示（Prompt），发送给选定的大语言模型（LLM），最终生成技术文档。

## 技术解析

### 问题分解与文本生成

这个项目并非通过一次简单的提问就生成所有内容，而是通过一系列精心设计的、分阶段的 Prompt，与 LLM 进行多次交互，逐步构建出最终的完整文档。这种方法将一个复杂任务分解为多个更小、更可控的子任务，从而显著提高了最终结果的质量和结构性。

本系统分为两个场景：

#### 1. Wiki页面生成

Wiki页面生成就是生成技术文档的具体过程。这个任务只需要用户在一开始指定代码仓库和模型，后续将自动完成。

这个过程由前端页面 `RepoWikiPage` 驱动，分为两个主要阶段：1. 生成 Wiki 结构，2. 逐页生成内容。

1. 生成Wiki结构

    前端会获取代码仓库的文件树和 README，然后向 LLM 发起请求，要求它设计出 **Wiki 的整体结构**（像一本书的目录）。

    prompt内容大体如下：

    ```

    分析仓库内容
    输入：GitHub 仓库的文件树（file tree）和 README 文件。
    目标：理解项目的结构、功能和技术组成。
    设计 Wiki 结构
        输出一个 XML 格式的 Wiki 框架，包含：
            Wiki 总标题和简介
            若干个主章节（sections）和页面（pages）
            每个页面有描述、重要性等级、相关源文件、关联页面等信息
    支持两种模式
        简版 Wiki（concise）：4-6 个页面
        完整版 Wiki（comprehensive）：8-12 个页面，包含更细的模块如架构、部署、前端组件等
    强调可视化内容
        倾向于为适合画图的内容创建页面，例如：
            架构图
            数据流
            组件关系
            工作流程
            状态机
            类继承结构
    输出格式严格限定
        必须是合法的 XML
        不允许任何 Markdown 包裹或额外说明文字
        必须从 <wiki_structure> 开始，以 </wiki_structure> 结束
    语言可配置
        输出的 Wiki 内容使用指定的语言（如中文、英文、日文等）

    ```
    
    prompts 直接给LLM指定了生成的文章结构的格式，是一个XML，例如：

    ``` xml
        <wiki_structure>
    <title>[Overall title for the wiki]</title>
    <description>[Brief description of the repository]</description>
    <sections>
        <section id="section-1">
        <title>[Section title]</title>
        <pages>
            <page_ref>page-1</page_ref>
            <page_ref>page-2</page_ref>
        </pages>
        <subsections>
            <section_ref>section-2</section_ref>
        </subsections>
        </section>
        <!-- More sections as needed -->
    </sections>
    <pages>
        <page id="page-1">
        <title>[Page title]</title>
        <description>[Brief description of what this page will cover]</description>
        <importance>high|medium|low</importance>
        <relevant_files>
            <file_path>[Path to a relevant file]</file_path>
            <!-- More file paths as needed -->
        </relevant_files>
        <related_pages>
            <related>page-2</related>
            <!-- More related page IDs as needed -->
        </related_pages>
        <parent_section>section-1</parent_section>
        </page>
        <!-- More pages as needed -->
    </pages>
    </wiki_structure>

    ```

    前端接收到 LLM 返回的 XML 字符串后，会使用 DOM 解析器将其解析成一个结构化的 JavaScript 对象 (wikiStructure)，并存储在 React 的 state 中。这个对象就成了生成具体页面内容的“任务清单”。

2. 逐页生成内容

    在获得 Wiki 结构后，前端会遍历“任务清单”中的每一页，为每一页发起一次新的 LLM 请求来生成其详细内容。

    对于每一页，前端都会创建一个新的、高度定制化的 Prompt。这个 Prompt 指示 LLM 扮演“专家技术作家”的角色，并对内容格式、图表使用（Mermaid）、代码片段、引用来源等方面提出了极其严格和详细的要求。

    prompt 内容大体如下：

    ```

    你作为一名资深技术写手和软件架构师，需要基于给定的软件项目中至少5个相关源码文件，生成一篇详尽且准确的技术wiki页面，内容围绕指定的“WIKI_PAGE_TOPIC”主题。
    具体要求包括：
        •页面开头必须用⁠<details>折叠块列出所有参考的源码文件（至少5个），格式严格要求。
        •紧接着用一级标题（#）写出页面主题。
        •内容结构要清晰，包含：
            1.简短的介绍，说明该主题在整个项目中的目的和概览。
            2.详细章节，分解主题的架构、组件、数据流、关键函数、类、接口等。
            3.广泛使用垂直方向的Mermaid图表（流程图、时序图、类图等）来可视化架构和流程。
            4.用Markdown表格总结重要信息，如组件描述、接口参数、配置选项、数据模型字段等。
            5.插入相关源码片段，格式规范，突出关键实现细节。
            6.对每条重要信息、图表、表格、代码片段都要精确引用对应源码文件和行号，且至少引用5个不同文件，保证内容来源真实可靠。
        •内容必须完全基于提供的源码，不能凭空推断或引入外部知识。
        •语言要专业、简洁、清晰，便于开发者理解。
        •最后可加简短总结，重申主题重要性。
    总结来说，这是一份严格指导如何基于源码生成高质量、结构化、可视化、且带有详尽引用的技术wiki文档的详细写作规范。

    ```

    这个 Prompt 同样被打包成请求体，通过 WebSocket 发送到后端的 `/generate_page_content` 接口。这个过程会在一个队列中对所有页面依次执行。前端每收到一个页面的生成内容（Markdown 文本），就会将其存储在 `generatedPages` 这个 state 对象中，以页面的 `id` 作为 key。

#### 2. Deep Research
    
允许用户自主提问，系统将基于构建好的RAG进行检索并生成回答。用户在 UI 上勾选 "Deep Research" 复选框 (`Ask.tsx`, line 682)，然后输入问题并点击发送，就能得到答案。

前端通过 useEffect 钩子和状态管理，实现了一个自动化的对话循环。

1. 接收第一轮响应：
   
   前端接收来自后端的完整流式响应，并更新到 `response state` 中。

2. 检查是否完成：
   
   当第一轮响应接收完毕后，`useEffect` 钩子被触发。它会调用 checkIfResearchComplete 函数，通过检查响应内容中是否包含 "## Final Conclusion" 等关键词，来判断整个研究是否已经结束。

3. 触发下一轮研究：

    如果研究未完成，并且迭代次数小于5，`useEffect` 会调用 `continueResearch` 函数。

4. 构建下一轮的 Prompt:

    在 `continueResearch` 函数内部，前端会构建一个新的对话历史。它将上一轮 LLM 的完整回答作为 assistant 角色添加到历史记录中。

    然后，它会自动添加一条新的 user 消息，内容固定为 `[DEEP RESEARCH] Continue the research`。这相当于前端在替用户自动“追问”，指令 LLM 继续进行下一步研究。

5. 发送新请求：
   
   前端将这个包含了新追问的、更长的对话历史，再次发送到同一个后端接口 `/api/chat/stream/route.ts`，开始新一轮的研究。

之所以采用看似复杂的“多轮提问”流程，而不是简单的一问一答，是为了解决一个核心难题：如何让大语言模型（LLM）对一个复杂的代码仓库进行既有深度、又有广度的、结构清晰的分析。

Deep Research 流程将一个模糊的大问题，变成了一个结构化的研究项目，确保了分析的每一个关键方面都被深入探讨，最终产出了一份远比单次提问要专业和全面的技术文档。

针对这个流程的一些细节进行解答：

1. 如何将用户问题拆分为多个子问题？

    前端本身不拆分问题，而是引导 LLM 自己完成拆分。这发生在整个流程的第一轮。

    当用户勾选 "Deep Research" 并提问时，前端在 `handleConfirmAsk` 函数中，将用户的原始问题前加上一个特殊标记 `[DEEP RESEARCH]`。

    这个被标记的初始问题被发送到后端。

    后端的 `System Prompt`（系统提示词，位于 `websocket_wiki.py`）会指导 LLM，当看到 [DEEP RESEARCH] 标记时，它的首要任务不是直接回答问题，而是生成一个“研究计划”。

    下面是这个 System Prompt的大体内容：

    ```
    🎯 定义一个“深度代码分析流程”的多轮研究机制
    它根据当前对话的阶段（首次、中间迭代、最终结论）动态生成不同的系统提示，指导 AI 如何逐步深入地分析某个 GitHub 仓库中用户指定的具体技术主题。

    🔍 核心目标：
    对用户提出的一个具体的 技术主题（如某个文件、功能模块、配置项等）进行 多轮深度代码分析
    每一轮都聚焦于该主题，逐步挖掘细节
    最终输出一份 结构清晰、内容详实、基于源码引用的结论
    🧩 主要组成部分总结如下：
    1. 角色定位
    你是一个专业的 代码分析师（expert code analyst）
    专注于分析特定的 GitHub 仓库，理解其结构和实现细节
    2. 多阶段流程设计
    分为四个主要阶段（由 is_first_iteration、is_final_iteration 和迭代次数控制）：

        第一轮	制定研究计划，明确研究主题与关键方向

        中间轮次	基于前序进展，深入挖掘具体子问题，填补空白

        最终轮次	综合所有发现，形成完整、聚焦的最终结论

        默认情况（非流程模式）	快速回答问题，直接给出准确、简洁的答案

    3. 每轮输出规范
    使用 Markdown 格式提高可读性
    引用具体文件路径和代码行号作为依据
    不允许泛泛而谈或重复已知信息
    必须始终聚焦于用户最初的问题
    4. 语言要求
    输出语言根据用户设定（如中文、英文等）

    ```

    LLM 返回的这个计划，通常是一个 Markdown 格式的列表（例如 ## Research Plan），就相当于把原始的复杂问题拆解成了多个具体的子问题或研究步骤。
    
2. 如何维护每一轮的子问题答案？

    系统通过一个不断增长的**对话历史（conversationHistory）**来维护所有上下文。

    实现逻辑:

    在每一轮研究结束时，前端会将 LLM 的完整回答（例如“研究计划”或“研究更新1”）存储起来。
    当需要开始下一轮研究时，continueResearch 函数会被调用。
    在此函数中，它会构建一个新的对话历史 newHistory。这个历史包含了之前所有的问答记录，并在末尾追加两条新消息：
    一条 assistant 消息，内容是上一轮 LLM 的完整回答。
    一条新的 user 消息，内容是固定的指令：[DEEP RESEARCH] Continue the research。
    这个包含了全部上下文的、更长的 newHistory 会被发送给 LLM，确保 LLM 在进行下一步时，能“记得”之前所有已经完成的工作。

3. 何时终止追问并总结结果？

    终止条件是“双保险”：一是基于 LLM 的回答内容进行智能判断，二是设置一个最大迭代次数作为兜底。

    智能判断：每次接收完一轮完整的回答后，checkIfResearchComplete 函数会被调用。它会检查回答内容中是否包含明确的结束标志，如 ## Final Conclusion、This concludes our research 等。如果包含，则将 researchComplete 状态设为 true，循环终止。

    最大迭代次数：系统设定了一个上限（5次迭代）。在 useEffect 钩子和 WebSocket 的 onClose 回调中，都会检查 researchIteration 是否达到了 5。如果达到，无论 LLM 是否给出了结束语，都会强制终止循环，并将 researchComplete 设为 true。

4. 如何总结并反馈给用户？

    总结工作同样交给了 LLM。前端负责在合适的时机（最后一轮）让 LLM 进行总结，并为用户提供友好的展示界面。

    实现逻辑:

    触发总结: 当进入最后一轮迭代时（无论是智能判断的最后一轮还是强制的第5轮），后端 Prompt (websocket_wiki.py) 会切换为“最终总结”模式，明确指示 LLM：“仔细回顾整个对话历史，综合所有发现，生成一份全面的最终报告”。
    结果反馈: LLM 生成的包含所有阶段（计划、更新、总结）的完整 Markdown 文本，被存储在 response state 中。
    分阶段展示: 为了避免信息过载，extractResearchStage 函数会将这个长文本解析成多个 ResearchStage 对象并存入 researchStages 数组。前端界面会根据这个数组渲染出导航按钮（上一阶段/下一阶段），让用户可以方便地回顾研究的每一个步骤。

### 与模型的交互

这个项目的交互设计非常现代化，优先使用 WebSocket 实现实时、双向的流式通信，并提供了 HTTP 作为备用方案。

#### 第一步 前端发送问题

前端会收集所有必要信息，构建一个名为 `requestBody` 的 JSON 对象。这个对象的数据结构由 `websocketClient.ts `中的 `ChatCompletionRequest` 接口定义。

内容：包括仓库 URL、完整的对话历史 `messages`、用户选择的模型提供商 `provider` 和具体模型 `model` 等。

```typescript

export interface ChatMessage {
  role: 'user' | 'assistant' | 'system';
  content: string;
}

export interface ChatCompletionRequest {
  repo_url: string;
  messages: ChatMessage[];
  filePath?: string;
  token?: string;
  type?: string;
  provider?: string;
  model?: string;
  language?: string;
  excluded_dirs?: string;
  excluded_files?: string;
}

```

建立 **WebSocket** 连接： 前端会调用 `websocketClient.ts` 中的 `createChatWebSocket` 函数。

问：为什么优先使用**WebSocket**?

WebSocket 协议非常适合用于现代 LLM 应用，尤其是在需要实时、交互式体验的场景中。它有以下优势：

1. 真正的全双工通讯

    HTTP: 是一个“请求-响应”模型。客户端发送一个请求，服务器返回一个响应，然后连接就关闭了（除非使用 Keep-Alive，但本质仍是单向的）。如果服务器有新的信息想主动推送给客户端，它做不到，只能等待客户端下一次轮询。

    WebSocket: 一旦连接建立，客户端和服务器之间就形成了一条持久的、双向的通道。任何一方都可以随时向对方发送数据，无需重新建立连接。

    支持复杂的交互式会话 (Interactive Sessions) 对于像 `Deep Research` 这样的多轮对话场景，WebSocket 的优势更加明显。

    状态保持：连接是持久的，服务器可以更容易地将会话状态与某个 WebSocket 连接关联起来。

    中断与控制：如果需要，客户端可以随时通过这个双向通道发送一个“停止生成”的控制消息，服务器收到后可以立即中断对 LLM 的请求。用 HTTP 实现这一点会非常困难。

2. 低延迟

    HTTP: 每次通信都需要完整的请求头，这会增加开销，尤其是在频繁的小数据包通信中。

    WebSocket: 初始握手时有一次类似 HTTP 的请求头，但之后的数据帧（frames）头部非常小，大大减少了网络开销，使得数据传输更快，延迟更低。

    WebSocket 完美匹配流式响应 (Streaming) LLM 生成回答是一个逐字（token）进行的过程。使用 WebSocket，后端每从 LLM API 收到一个新的 token，就可以立即通过这个持久的通道将其推送到前端。

    用户体验：用户能看到“打字机”一样的实时生成效果，而不是盯着一个加载动画等待几秒甚至几十秒才能看到完整答案。这极大地提升了交互的即时感和流畅性。
    
    HTTP 实现流式响应：虽然也可以通过 `Transfer-Encoding: chunked `实现，但它在客户端处理起来更复杂，且不如 WebSocket 的**事件驱动模型（onmessage）**来得直接和优雅。

3. 减少服务器负载和网络拥塞

    HTTP 轮询: 为了模拟实时性，客户端需要不断地向服务器发送请求（“有新数据吗？”“有新数据吗？”）。这会产生大量无意义的请求，极大地增加了服务器的负载和网络流量。

    WebSocket: 只需要维持一个 TCP 连接。没有数据时，连接就静默地保持着，几乎不消耗资源。只有在真正有新数据时才会进行传输。

#### 第二步 后端处理请求并与 LLM 交互 (Server <-> LLM)

Python 后端（基于 FastAPI）接收到请求后，开始执行核心的 RAG 和 LLM 调用逻辑。

1. 接收与解析请求：

    WebSocket 端点：websocket_wiki.py 中的 `websocket_endpoint` 函数负责监听和处理 WebSocket 连接。它接收到 JSON 数据后，会解析出 `messages`、`provider`、`model` 等信息。
    
    HTTP 端点：如果通过 HTTP 访问，FastAPI 的路由（如 `/api/chat/stream`）会接收请求。

2. 构建最终的Prompt：

    后端并不会直接将用户的问题发给 LLM。它会执行 **RAG** 流程：

    - 调用 rag.py 中的 `get_retriever` 来获取与当前对话最相关的代码片段（上下文）。

    - 将**系统指令**（System Prompt，定义在 `rag.py` 中，要求 LLM 扮演代码助手并使用 Markdown）、检索到的**代码上下文**、以及用户的**对话历史**，组合成**一个完整的、信息量极大的 Prompt**。

3. 与不同来源的LLM通信

    抽象基类 `ModelClient`: 是项目设计的精妙之处。它没有为每个 LLM 提供商写一套重复的逻辑，而是**设计了一个统一的抽象层**，并用具体实现类继承它。

    具体实现累类:
    
       - `OpenAIClient`:
            
            支持平台: OpenAI 官方 API 以及任何与 OpenAI API 格式兼容的第三方服务（例如，一些本地模型框架）。

            如 gpt-4o, gpt-4-turbo, gpt-3.5-turbo 等。可以通过 `model` 参数指定

            可以通过设置 OPENAI_BASE_URL 环境变量指向你的本地服务地址（例如 `http://localhost:11434/v1`），然后选择 OpenAI 提供商来调用本地模型。

            对 Ollama 的支持是通过 `OpenAIClient` 实现的。Ollama 服务在启动后，默认会在 `http://localhost:11434` 暴露一个与 OpenAI API 格式兼容的接口。因
    
       - `AzureAIClient`:

            支持平台: Microsoft Azure OpenAI Service。

            支持部署在 Azure 上的各种 OpenAI 模型。在 Azure 中，你不是直接指定模型名称（如 gpt-4o），而是指定你的“部署名称 (deployment name)”。

            需要设置 `AZURE_OPENAI_API_KEY`, `AZURE_OPENAI_ENDPOINT`, 和 `AZURE_OPENAI_VERSION` 环境变量。它同时支持 API Key 和 Azure AD 两种认证方式。

       - `OpenRouterClient`: 

            支持平台: OpenRouter.ai。OpenRouter 是一个模型聚合器，它允许你通过一个统一的 API 调用来自不同提供商（如 Google, Anthropic, Mistral AI, Meta 等）的多种模型。

            支持非常广泛的模型列表，几乎涵盖了市面上所有主流的开源和闭源模型。调用时需要指定模型全名，例如 `google/gemini-pro`, `anthropic/claude-3-opus`, `mistralai/mixtral-8x7b-instruct` 等

       - `BedrockClient`:

          支持平台: Amazon Bedrock。这是亚马逊云（AWS）提供的托管式基础模型服务。

#### 第三步 接收LLM响应并返回给前端(LLM->Server->Client)