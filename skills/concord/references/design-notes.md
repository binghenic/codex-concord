# 设计说明

Concord 是使用宿主会话与文件工具的协作 Skill，没有安装代理框架，也不提供运行时锁或后台调度器。

以下公开项目提供设计参考，不是依赖：

- [CrewAI](https://github.com/crewAIInc/crewAI)：角色团队、角色模型配置和管理者协调；参考 [Agent](https://github.com/crewAIInc/crewAI/blob/main/lib/crewai/src/crewai/agent/core.py) 与 [Crew](https://github.com/crewAIInc/crewAI/blob/main/lib/crewai/src/crewai/crew.py) 的职责区分。
- [MetaGPT](https://github.com/FoundationAgents/MetaGPT)：通过规格与开发产物连接不同职责。

本 Skill 的消息编号、唯一写入者和基线身份是本地协作约定，不能据此声称上述项目或宿主已提供跨会话强制控制。
