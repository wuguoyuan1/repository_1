```mermaid
flowchart TD
  %% ============ 输入 =============
  subgraph Inputs["   输入   "]
      A1([Issue Report]):::data
      A2([Repository-level Project<br/>(Source, Docs, Tests)]):::data
      A3([Existing Knowledge Base<br/>(可增量更新)]):::data
  end

  %% ============ 离线：KB 构建 ============
  subgraph Offline["离线阶段：知识库构建"]
      B1[代码切片<br/>(函数/类/文件)]:::proc
      B2[结构抽取<br/>(调用图、依赖、CFG/DFG)]:::proc
      B3[多模态索引<br/>• 语义向量<br/>• 关键词倒排<br/>• 图索引]:::proc
      B4[历史演化分析<br/>(SZZ 找 bug 导入行、热度、覆盖率)]:::proc
      B5([更新 Knowledge Base]):::store
      A2 -->|解析| B1
      A2 --> B2
      B1 --> B3
      B2 --> B3
      B3 --> B5
      B4 --> B5
      A3 -->|增量合并| B5
  end

  %% ============ 在线：缺陷定位 ============
  subgraph Online["在线阶段：LLM-RAG 缺陷定位"]
      C1[Issue 理解<br/>• 信息抽取<br/>• 查询生成]:::proc
      C2[分层检索<br/>Level-1 模块<br/>Level-2 文件<br/>Level-3 函数]:::proc
      C3[候选代码片段<br/>(top-k 函数)]:::data
      C4[LLM 推理 / 重排<br/>(RAG Prompt)]:::proc
      C5([输出：最可能 Bug 的<br/>文件 & 函数 + 解释]):::out
      C6[[开发者确认<br/>Fix Commit]]:::data
      C7([回写 KB 作为新监督<br/>(自监督学习/权重更新)]):::store
      A1 --> C1
      C1 --> C2
      B5 -->|检索| C2
      C2 --> C3
      C3 --> C4
      A1 -->|上下文| C4
      C4 --> C5
      C5 --> C6
      C6 -->|真实修复位置| C7
      C7 --> B5
  end

  %% ============ 样式 ============
  classDef proc fill:#dfe7fd,stroke:#4c6ef5,color:#000;
  classDef data fill:#fff4e6,stroke:#f08c00,color:#000;
  classDef store fill:#e6fcf5,stroke:#12b886,color:#000;
  classDef out fill:#ffdce0,stroke:#d6336c,color:#000;
