
这个项目总体上就是[[2026-07-06]]哈哥所说的软件工程革命：
- 继承分治网络
- AI是执行中心
# Adapter

实现了自己的adapter[[2026-07-10]]

# Capability Pack

可复用的技术底座。
一个 Capability Pack = 镜像（工具链） + token request（内部平台调用权限）;
Agent 通过 link 引用,Pack 改镜像所有 Agent 跟随。

新增engineer_profiles表

# Playbook

markdown

这个岗位的活怎么干、按什么流程走，不写具体是哪个业务、哪个仓、哪个平台参数

一个 Playbook = 一个岗位角色的标准作业流程(SOP,业务无关),与 Capability Pack 平级。
Agent 引用一份岗位手册,运行时整份合并进指令;改一次正文,所有引用它的 agent 下轮运行自动生效。

与上层capacity pack同级的抽象

# Agent-Flow

一个模版 = 一段 markdown,指引 CEO 去招人 / 招一个 team(招一个人 = 选能力 + 建角色;招一个 team = 招多个人 + 编排协作流水线)。

# Thoughts

Why？
- 真实的场景是：业务多而复杂，凭证复杂，强流程
- 这三个的抽象，而不是直接一个Agent：容易复用，避免组合爆炸，而且岗位SOP的更新速度大于工具

Problem？
- 还没有深刻接受理解业务
- 还没有上手用过本地化后的paperclip
