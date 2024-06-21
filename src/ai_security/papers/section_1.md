## SECGPT: An Execution Isolation Architecture for LLM-Based Systems


### 链接
[SECGPT: An Execution Isolation Architecture for LLM-Based Systems](https://arxiv.org/pdf/2403.04960)

### 简要内容概况
本文提出了SECGPT，一种针对基于大型语言模型（LLM）的系统执行隔离架构。随着LLM的扩展，例如ChatGPT，它们开始支持第三方应用程序，这些应用程序利用自然语言进行自动化执行，但存在安全和隐私风险。SECGPT的核心思想是通过隔离应用程序的执行和更精确地调节它们在隔离环境外的交互来减轻这些风险。

### 重点内容解读
#### 关键技术点
- **执行隔离**：SECGPT将每个应用程序在隔离环境中执行，以减少它们之间的潜在攻击面。
- **精确交互**：通过一个名为hub的中央可信接口来管理应用程序间的交互，确保交互在用户授权下进行。
- **hub-and-spoke架构**：SECGPT采用hub-and-spoke架构，hub作为中心节点，spoke代表隔离的应用程序实例。
- **安全通信协议**：提出了一种inter-spoke communication (ISC)协议，允许spoke之间通过hub安全地交换消息。

### 实验评估
- 通过与非隔离系统VANILLAGPT的比较，SECGPT在安全性、功能性和性能方面进行了评估。
- 在安全性方面，SECGPT成功抵御了多种针对第三方应用程序的攻击。
- 在性能方面，SECGPT在大多数测试查询中的性能开销低于非隔离系统的0.3倍。

### 小结
SECGPT展示了一种创新的方法来增强基于LLM的系统的安全性和隐私保护，同时保持了系统的功能性和可接受的性能开销。通过执行隔离和精确的交互控制，SECGPT为第三方应用程序提供了一个安全的运行环境，这对于保护用户数据和系统安全至关重要。