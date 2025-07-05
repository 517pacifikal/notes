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