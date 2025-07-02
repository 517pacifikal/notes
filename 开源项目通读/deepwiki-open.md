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

### 1. 仓库克隆
   
将要阅读的仓库克隆到本地。这个项目无论项目来源是什么，最终都要将其存储到本地来进行解析。

项目实现了三级缓存机制：

- 仓库级缓存

    完整的Git仓库克隆,保留所有文件和Git历史,支持增量更新（通过Git pull）

    **生产时机**：当用户在前端输入一个仓库地址（GitHub/GitLab/Bitbucket）并发起解析请求时，后端首先会检查本地是否已存在该仓库的克隆。如果没有，就会调用 download_repo 方法将仓库克隆到本地（如 ~/.adalflow/repos/owner_repo/）。

    **使用时机**：后续所有的文件读取、代码分析、文档生成、向量化等操作，都是基于本地克隆的仓库进行的。每次解析同一个仓库时，都会优先复用本地已有的克隆，避免重复下载。

- 向量级缓存

    **TODO 简单学习词向量相关的知识**
    
    将下面这些信息进行了向量化存储：

    - 源代码文件：.py, .js, .ts, .java, .cpp, .c, .go, .rs 等
    - 配置文件：package.json, requirements.txt, Dockerfile，docker-compose.yml 等
    - 文档文件：README.md, .md, .txt, .rst 等
    - 数据文件：.json, .yaml, .yml, .xml 等（配置相关）
    
    DeepWiki 使用的是 FAISS (Facebook AI Similarity Search) 作为向量数据库技术。
    
    **生产时机**：仓库克隆后，系统会递归读取所有需要的文件（代码、文档等），对内容进行分割和预处理，然后调用嵌入模型（如 OpenAI 或 Ollama，通过 get_embedder() 实现）将文本转为向量。所有向量和元数据会被存储到本地的 FAISS 数据库（如 ~/.adalflow/databases/owner_repo.pkl）。

    **使用时机**：在生成文档时，系统会用 RAG 检索技术，从向量数据库中找出与当前主题/问题最相关的代码片段或文档，作为上下文输入给大模型。在用户进行“代码问答”或“深度研究”时，RAG 检索也会实时用到向量数据库，提升回答的准确性和相关性。如果数据库已存在且未过期，后续解析同一仓库时会直接复用，无需重新向量化。

- Wiki级缓存

    Wiki级缓存保存了针对某个仓库、某种语言、某个模型生成的完整Wiki结构和内容，包括：

    - Wiki结构信息（如目录、页面列表、描述等）
    - 每个Wiki页面的详细内容（AI生成的文档、标题、关联文件、重要性、相关页面等）
    - 仓库元信息（如owner、repo、repo类型、repoUrl等）
    - 生成时用到的AI模型和提供商信息

    缓存的形式是JSON文件的形式，具体存储文档的内容。

    **生产时机**：当大模型（如 OpenAI、Gemini、Ollama 等）根据仓库内容和RAG检索到的上下文生成结构化Wiki文档后，系统会将这些内容（包括目录结构、每个页面的内容、相关元信息等）序列化为JSON文件，存储在本地（如 ~/.adalflow/wikicache/deepwiki_cache_github_owner_repo_en.json）。

    **使用时机**：用户再次访问同一仓库、同一语言、同一模型时，系统会优先读取JSON缓存，直接返回生成好的Wiki文档，极大提升响应速度。前端页面展示Wiki内容、导出Markdown/JSON、页面跳转等，都是基于这个缓存数据。只有在缓存不存在或用户主动要求重新生成时，才会重新走一遍生成流程。

