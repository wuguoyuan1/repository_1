```mermaid
flowchart TD
  %% ============ 输入 =============
  subgraph Inputs["输入"]
      A1([Issue Report])
      A2([Repository-level Project (Source, Docs, Tests)])
      A3([Existing Knowledge Base (可增量更新)])
  end

  %% ============ 离线：KB 构建 ============
  subgraph Offline["离线阶段：知识库构建"]
      B1[代码切片 (函数/类/文件)]
      B2[结构抽取 (调用图、依赖、CFG/DFG)]
      B3[多模态索引 语义向量 / 倒排 / 图索引]
      B4[历史演化分析 (SZZ 找 bug 导入行)]
      B5([更新 Knowledge Base])

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
      C1[Issue 理解]
      C2[分层检索]
      C3[候选代码片段 top-k]
      C4[LLM 推理 / 重排]
      C5([输出：Bug 文件和函数])

      A1 --> C1
      C1 --> C2
      B5 -->|检索| C2
      C2 --> C3
      C3 --> C4
      A1 --> C4
      C4 --> C5
  end
```


