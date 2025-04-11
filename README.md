# writtingNNs

这个项目手写复现神经网络，旨在

- 了解各网络结构细节
- 调研各经典或先进网络
- 对比相似网络的异同

主要偏向于 attention 相关的模型，以及个人感兴趣的模型



## 知识体系树

- **人工智能AI**
  - 非ML算法，如：传统图像处理、基于规则的句法分析、动力学建模、控制理论
  - 机器学习
    - 传统机器学习（SVM、随机森林等）
    - 深度学习
      - MLP
      - RNN
      - CNN
      - **Attention 注意力机制**
        - RNN/CNN+Attention
        - 图注意力神经网络
        - Transformer：一种组合了以下组件的工程模型
          - 多头注意力（Multi-Head Attention）
          - 位置编码（Positional Encoding）
          - 残差连接+层归一化（Residual+LayerNorm）
          - 前馈网络（FFN）



- **Transformer核心组件**：

  - 编码器（Encoder）：双向注意力，适合理解任务
  - 解码器（Decoder）：掩码注意力，适合生成任务

  -  据此分类的 Transformer 衍生模型

| **模型类型**        | **架构选择**          | **代表模型**              | **典型任务**      |
| :------------------ | :-------------------- | :------------------------ | :---------------- |
| **Encoder-Only**    | 仅用Transformer编码器 | BERT, RoBERTa, DeBERTa    | 文本分类/实体识别 |
| **Decoder-Only**    | 仅用Transformer解码器 | GPT系列, LLaMA, Bloom     | 文本生成          |
| **Encoder-Decoder** | 完整Transformer架构   | T5, BART, Transformer-NMT | 机器翻译/文本摘要 |



- **大模型**
  - 现在主流的大模型，都以Transformer结构为基础。可以说，大模型就是“参数量通常 ≥10B“的魔改版Transformer
  - **关键技术**
    - **Attention相关**
      - 缩放点积注意力
      - 多头注意力
      - 交叉注意力
    - **微调技术**
      - 全参数微调
      - **参数高效微调（PEFT）**
        - **LoRA（低秩适配）**
        - Adapter
        - Prefix-Tuning
    - 动态技术
      - MoE
    - 扩展技术
      - 分布式训练（如FSDP）
      - 量化技术（如GGML）
  - 市面常见大模型
    - 闭源
      - GPT-4（OpenAI）
      - Claude（Anthropic）
      - Deepseek（幻方量化）
    - 开源
      - LLaMA（Meta）
        - LLaMA-1（7B-65B参数）
        - LLaMA-2（优化对话能力）
      - Falcon（阿联酋TII）
      - BLOOM（BigScience）



- Attention、Transformer、大模型是层层递进的

| **维度**       | **注意力机制**        | **Transformer**          | **大模型**                    |
| :------------- | :-------------------- | :----------------------- | :---------------------------- |
| **抽象层级**   | 数学原理              | 模型架构                 | 系统级实现                    |
| **核心贡献**   | 相关性权重计算        | 注意力机制的工程化框架   | 规模化的Transformer应用       |
| **是否可独立** | 是（如RNN+Attention） | 否（必须包含注意力机制） | 否（必须基于Transformer变体） |
| **典型代表**   | Additive Attention    | 原始Transformer(2017)    | GPT-4/PaLM-2                  |



## 复现计划

- [ ] Transformer
- [ ] Diffusion

- [ ] AI生成语音（如TTS）

- [ ] AI绘图（如Stable Diffusion）
