---
title: Anthropic - Harness design for long-running apps 共读笔记
date: 2026-03-28
tags:
  - anthropic
  - agents
  - harness
  - reading-notes
  - ai
aliases:
  - Harness design for long-running apps 共读笔记
---

# Anthropic - Harness design for long-running apps 共读笔记

原文：[[Anthropic - Harness design for long-running apps]]

## 原文摘录 + 中文译文 + 观点

### 1. 开头：问题升级

#### 原文

> Over the past several months I’ve been working on two interconnected problems: getting Claude to produce high-quality frontend designs, and getting it to build complete applications without human intervention. This work originated with earlier efforts on our frontend design skill and long-running coding agent harness, where my colleagues and I were able to improve Claude’s performance well above baseline through prompt engineering and harness design—but both eventually hit ceilings.

#### 中文译文

在过去几个月里，作者一直在处理两个相互关联的问题：

1. 如何让 Claude 产出高质量的前端设计  
2. 如何让它在**没有人工介入**的情况下构建完整应用

这项工作起源于他们更早之前在两个方向上的尝试：

- frontend design skill  
- long-running coding agent harness

在那些工作里，作者和同事们已经通过 prompt engineering 和 harness design，把 Claude 的表现显著提升到了 baseline 之上。  
但最终，这两条路都遇到了天花板。

#### 我的观点

这篇不是上一篇的重复版，而是在升级问题定义：

- 不是只解决 long-running coding
- 还要解决主观质量评估
- 还要解决“无人工介入”下的系统组织问题

---

### 2. GAN 启发：generator + evaluator

#### 原文

> To break through, I sought out novel AI engineering approaches that held across two quite different domains, one defined by subjective taste, the other by verifiable correctness and usability. Taking inspiration from Generative Adversarial Networks (GANs), I designed a multi-agent structure with a generator and evaluator agent. Building an evaluator that graded outputs reliably—and with taste—meant first developing a set of criteria that could turn subjective judgments like “is this design good?” into concrete, gradable terms.

#### 中文译文

为了突破这个瓶颈，作者开始寻找一种新的 AI engineering 方法，它必须能够同时适用于两个非常不同的领域：

- 一个是由**主观审美**决定的领域  
- 一个是由**可验证正确性与可用性**决定的领域

作者从 GAN（生成对抗网络）中获得灵感，设计了一种由 **generator** 和 **evaluator** 组成的多 agent 结构。  
而要让 evaluator 能够稳定、可靠、而且“有品味”地评估输出，前提是先建立一套评价标准，把类似“这个设计好不好看？”这样的主观判断，转成更具体、可评分的条目。

#### 我的观点

关键不是 GAN 这个比喻本身，而是一个很硬的原则：

> 不要让执行者兼任自己的裁判。

执行和评审拆开，系统质量会显著提升。

---

### 3. 从两 agent 到三 agent

#### 原文

> I then applied these techniques to long-running autonomous coding, carrying over two lessons from our earlier harness work: decomposing the build into tractable chunks, and using structured artifacts to hand off context between sessions. The final result was a three-agent architecture—planner, generator, and evaluator—that produced rich full-stack applications over multi-hour autonomous coding sessions.

#### 中文译文

随后，作者把这些方法迁移到长时间运行的自治式编码任务中，并沿用了他们此前 harness 工作中的两条经验：

1. 把构建任务拆解成可处理的小块  
2. 用结构化工件在不同 session 之间做上下文交接

最终形成的是一个三 agent 架构：

- **planner**  
- **generator**  
- **evaluator**

这个结构可以在持续数小时的自治编码 session 中，产出相当丰富的全栈应用。

#### 我的观点

这一步很关键：

- 不是让一个 agent 变得更万能
- 而是把系统拆成不同职责角色

也就是说，问题的解法越来越偏向 **组织结构设计**，而不是单点 prompt 技巧。

---

### 4. 朴素实现为什么不够

#### 原文

> We've previously shown that harness design has a substantial impact on the effectiveness of long running agentic coding. ... But some problems remained persistent. For more complex tasks, the agent still tends to go off the rails over time.

#### 中文译文

他们之前已经证明，harness design 会显著影响 long-running agentic coding 的效果。  
但即便如此，有些问题依然长期存在。  
当任务变得更复杂时，agent 还是很容易随着时间推移而逐渐失控。

#### 我的观点

这再次说明：

- baseline agent 能做一些事
- 但系统一旦变长、变复杂、变主观
- 朴素单 agent 方案会迅速露出天花板

---

### 5. context anxiety 与 reset vs compaction

#### 原文

> First is that models tend to lose coherence on lengthy tasks as the context window fills ... Some models also exhibit "context anxiety" ... Context resets ... address both these issues.

> This differs from compaction ... While compaction preserves continuity, it doesn't give the agent a clean slate ... In our earlier testing, we found Claude Sonnet 4.5 exhibited context anxiety strongly enough that compaction alone wasn't sufficient ... Opus 4.5 largely removed that behavior on its own, so I was able to drop context resets from this harness entirely.

#### 中文译文

第一种失败模式是：当 context window 逐渐被填满时，模型在长任务里会逐渐失去连贯性。  
有些模型还会表现出一种 **“context anxiety”**——当模型感觉自己快要接近上下文上限时，会开始**过早收尾、草率结束工作**。  
他们发现，**context reset** 可以同时解决这两个问题：

- 清空当前 context window  
- 启动一个新的 agent  
- 再配合一个结构化交接物，把上一个 agent 的状态和下一步工作传给下一个 agent

这和 compaction 不一样。  
compaction 是把更早的对话内容原地压缩总结，让**同一个 agent** 在被缩短的历史之上继续工作。  
虽然 compaction 能保留连续性，但它不能给 agent 一个真正的 clean slate，所以 context anxiety 依然可能存在。  

作者后面又补了一点：在早期的 Sonnet 4.5 上，compaction 不够；但到了 Opus 4.5，这种焦虑明显缓解了，所以这次实验可以直接用连续 session + automatic compaction，不再需要 context reset。

#### 我的观点

这里最容易误解的一点是：

> 不是 compaction 变神了，而是模型本身更稳了，所以 compaction 终于够用了。

核心区别：

- `compaction` 解决“装不下”  
- `reset` 解决“状态污染 / 焦虑 / 跑偏”

所以这不是理论谁更高级，而是取决于：

- 当前模型稳不稳  
- 当前任务长不长  
- 当前任务是不是容易跑偏

---

### 6. self-evaluation：自评天然偏宽松

#### 原文

> A second issue, which we haven’t previously addressed, is self-evaluation. When asked to evaluate work they've produced, agents tend to respond by confidently praising the work—even when, to a human observer, the quality is obviously mediocre.

> However, even on tasks that do have verifiable outcomes, agents still sometimes exhibit poor judgment ... Separating the agent doing the work from the agent judging it proves to be a strong lever to address this issue.

#### 中文译文

第二个他们此前没有真正解决的问题，是 **self-evaluation（自我评估）**。  
当 agent 被要求评估自己做出来的工作时，它通常会很自信地夸赞自己的结果——即使在人类看来，这个质量其实明显只是平庸，甚至并不好。

而且这不只是设计这种主观任务才会发生。  
即使在那些**有可验证结果**的任务里，agent 也依然可能判断失真。  
把“做事的 agent”和“评判结果的 agent”拆开，是一个非常有效的杠杆。

#### 我的观点

这是全篇我最认同的原则之一：

> 自评偏宽松，是通用问题，不是艺术领域特例。

所以以后不能默认让同一个 agent：

- 做事  
- 验收  
- 还顺便宣布自己做得很好

---

### 7. 前端设计：把主观质量变成可评分维度

#### 原文

> - **Design quality** ...  
> - **Originality** ...  
> - **Craft** ...  
> - **Functionality** ...

#### 中文译文

作者给前端设计定义了 4 个评分标准：

- **Design quality（设计整体性）**：是否是一个统一整体，而不是零件拼装  
- **Originality（原创性）**：有没有定制化决策，而不是模板/默认样式/AI 套路  
- **Craft（工艺 / 做工）**：字体层级、间距一致性、对比度等基本功  
- **Functionality（功能性）**：拿掉审美之后，它是不是依然可用

作者还特别强调了：

- design quality 和 originality 权重更高  
- 因为 Claude 默认已经比较擅长 craft 和 functionality  
- 但在 design 和 originality 上容易滑向“安全但平庸”

#### 我的观点

这个部分最有意思的是：

- evaluator 不是纯检查器
- 它还是**方向塑形器**

标准本身就会反过来塑造 generator 的输出风格。

---

### 8. 评分语言会直接改变输出气质

#### 原文

> The wording of the criteria steered the generator in ways I didn't fully anticipate. Including phrases like "the best designs are museum quality" pushed designs toward a particular visual convergence ...

#### 中文译文

作者发现，评分标准的措辞本身，会以一种他没有完全预料到的方式影响 generator。  
例如加入“最好的设计应该达到 museum quality”这样的表达后，产出的设计会被推向某种特定的视觉收敛方向。  
这说明：**评价标准相关的 prompt 语言，本身就在直接塑造输出结果的气质。**

#### 我的观点

这说明 criteria 不是中性的：

- 它不仅在评估结果  
- 也在定义什么叫高级  
- 甚至会把 generator 推向某种风格收敛

---

### 9. Scaling to full-stack coding：planner / generator / evaluator

#### 原文

> The generator-evaluator loop maps naturally onto the software development lifecycle, where code review and QA serve the same structural role as the design evaluator.

> **Planner:** ... expand a 1-4 sentence prompt into a full product spec ... focus on product context and high level technical design rather than detailed technical implementation.

> **Generator:** ... work in sprints, picking up one feature at a time from the spec.

> **Evaluator:** ... used the Playwright MCP ... graded each sprint against bugs and criteria ... each criterion had a hard threshold.

#### 中文译文

作者把 generator-evaluator 结构迁移到了全栈开发生命周期里：

- code review / QA 在这里扮演的，就是设计评审 evaluator 的结构角色

然后系统被拆成三种 persona：

- **Planner**：把 1-4 句 prompt 扩展成完整 spec；重点约束交付物，而不是过早写死实现路径  
- **Generator**：按 sprint 工作，每次只推进一个 feature  
- **Evaluator**：用 Playwright MCP 去像用户一样测应用，并按 bug、功能性、视觉、代码质量等标准进行验收

#### 我的观点

这一步最值钱的不是“多 agent”，而是三层分工特别清楚：

- planner 负责把模糊目标变清楚  
- generator 负责产出  
- evaluator 负责挑刺和验收

这比让一个 agent 同时兼任三种角色要健康得多。

---

### 10. sprint contract：在高层 spec 和可测实现之间加一层协议

#### 原文

> Before each sprint, the generator and evaluator negotiated a sprint contract: agreeing on what "done" looked like for that chunk of work before any code was written ... The generator proposed what it would build and how success would be verified, and the evaluator reviewed that proposal ... The two iterated until they agreed.

#### 中文译文

每个 sprint 开始前，generator 和 evaluator 会先协商一份 **sprint contract**：

- 在代码开始前就先说清楚，这一轮的 done 到底意味着什么  
- generator 提出：这轮做什么、怎么验证  
- evaluator 负责审核是否合理  
- 双方迭代到一致为止

#### 我的观点

我觉得这是这篇最容易被低估的点之一。  
它本质是在：

- 高层长期目标（spec）  
- 当前实现动作

之间，再插入一层**本轮可验收协议**。

这会极大减少“做偏了但自己不知道”的问题。

---

### 11. file-based communication：文件不是日志，是协议层

#### 原文

> Communication was handled via files ... The generator then built against the agreed-upon contract before handing the work off to QA.

#### 中文译文

agent 之间通过文件通信：

- 一个 agent 写文件  
- 另一个 agent 读文件并回应  
- generator 基于最终 agreed-upon contract 去实现，再交给 QA

#### 我的观点

这里的文件不是“工作日志”，而是：

- 协议载体  
- 状态工件  
- 交接媒介

这和我们后来讨论的 `progress.txt` 很一致：

> 文件的价值不是记录发生过什么，而是让下一个人不用猜现在是什么状态。

---

### 12. harness vs solo：贵很多，但提升也很真实

#### 原文

> The harness was over 20x more expensive, but the difference in output quality was immediately apparent.

#### 中文译文

这套 harness 的成本高出了 **20 倍以上**，但产出质量差异也立刻就能看出来。

#### 我的观点

Anthropic 在这里证明的不是“这套系统便宜”，而是：

> 当任务真的复杂、真的高价值时，多 agent + 多轮评审 + 明确验收，能够把产出从“像个 demo”推进到“核心路径真的能用”。

---

### 13. evaluator 真正的价值：不是兜底，而是把实现拉回 spec

#### 原文

> Reading through the logs, it was clear that the evaluator kept the implementation in line with the spec ... The evaluator's findings were specific enough to act on without extra investigation.

#### 中文译文

作者在读日志后发现，evaluator 确实在把实现不断拉回 spec。  
而且它抓出来的问题足够具体，具体到 generator 不需要再额外调查就能直接修。

#### 我的观点

这一点非常关键：

- 低质量评审只会说“这里不太对”  
- 高质量评审会给出**可执行反馈**

Anthropic 这里做出来的是后者。

---

### 14. QA 不会自动变强，需要刻意调教

#### 原文

> Out of the box, Claude is a poor QA agent ... it would identify legitimate issues, then talk itself into deciding they weren't a big deal and approve the work anyway ... The tuning loop was to read the evaluator's logs, find examples where its judgment diverged from mine, and update the QA's prompt ...

#### 中文译文

Claude 开箱即用时并不是一个好的 QA agent。  
它会：

- 识别出真实问题  
- 但又自己说服自己，觉得问题不严重  
- 最后把工作放行

作者的调优方法是：

- 去读 evaluator 日志  
- 找出“它的判断和我不一致”的例子  
- 再回头更新 QA prompt

#### 我的观点

这里最重要的不是“prompt 怎么写”，而是这个调优闭环：

> 拿人类评审标准去校准 evaluator 的判断边界。

这其实和训练一个 reviewer 很像。

---

### 15. harness 的每个组件都是“能力补丁”

#### 原文

> every component in a harness encodes an assumption about what the model can't do on its own ... those assumptions are worth stress testing ... because they may be incorrect, and because they can quickly go stale as models improve.

#### 中文译文

harness 里的每一个组件，其实都隐含着一个假设：

> 模型自己做不好某件事，所以我们要用一个结构来补它。

而这些假设都值得被反复做压力测试，因为：

1. 它们可能从一开始就是错的  
2. 随着模型能力提升，它们会很快过时

#### 我的观点

这是全篇我最想拿走的系统观之一：

> harness 组件不是神圣的，它们只是当前时点的能力补丁。

所以不能神化某个结构。

---

### 16. simplify harness：先删，再看谁是真正 load-bearing

#### 原文

> I moved to a more methodical approach, removing one component at a time and reviewing what impact it had on the final result.

#### 中文译文

作者后来改用一个更系统的方法：

- 每次只移除一个组件  
- 然后观察它对最终结果到底有什么影响

#### 我的观点

这非常工程化，也非常值得借：

- 不要一口气砍很多  
- 不要凭感觉判断谁重要  
- 要逐个去掉、逐个验证

只有这样，才能找出真正 **load-bearing** 的组件。

---

### 17. Opus 4.6：旧脚手架开始失去承重性

#### 原文

> Opus 4.6 ... plans more carefully, sustains agentic tasks for longer ... better code review and debugging skills ... improved substantially on long-context retrieval. These were all capabilities the harness had been built to supplement.

#### 中文译文

Opus 4.6 在这些方面明显增强了：

- 规划更谨慎  
- 更能持续处理长任务  
- 更擅长 code review 和 debugging  
- long-context retrieval 更强

而这些恰恰就是旧 harness 原本试图去补的地方。

#### 我的观点

不是 harness 突然没用了，而是：

- 一部分旧补丁失去承重性了  
- 系统应该随模型升级一起重构

---

### 18. evaluator 不是固定必选项，而是边界任务加速器

#### 原文

> The practical implication is that the evaluator is not a fixed yes-or-no decision. It is worth the cost when the task sits beyond what the current model does reliably solo.

#### 中文译文

实践上的结论是：

> evaluator 不是一个固定的“要 / 不要”问题。  
> 只有当任务超出当前模型单独稳定完成的能力边界时，它的成本才值得支付。

#### 我的观点

这句非常成熟。  
它意味着：

- evaluator 不是高级系统的必备件  
- 它是“边界任务加速器”

如果模型自己就能稳稳完成，上 evaluator 就可能只是多花钱、多加时延。  
但如果任务处在能力边界上，它就很值。

---

### 19. 更新版 harness 结果：builder 跑更久，QA 抓最后一公里问题

#### 原文

> The run was still lengthy and expensive ... builder ... ran coherently for over two hours without the sprint decomposition ...

> The QA agent still caught real gaps ... clips can't be dragged/moved ... no instrument UI panels ... no visual effect editors ...

#### 中文译文

更新版 harness 依然长且贵，但 builder 在没有 sprint 拆分的情况下，仍能连贯地跑超过两小时。  
同时 QA 仍然抓出了真实缺口，例如：

- clip 不能在时间轴上拖拽移动  
- 没有真正的乐器 UI 面板  
- 没有图形化效果器编辑界面  
- 录音仍是 stub  
- clip resize / split 没实现

#### 我的观点

这说明在更强模型上：

- generator 已经可以扛更多活  
- 但 QA 仍然有最后一公里的真实价值

---

### 20. 尾声：harness 空间不会缩小，只会移动

#### 原文

> From this work, my conviction is that the space of interesting harness combinations doesn't shrink as models improve. Instead, it moves, and the interesting work for AI engineers is to keep finding the next novel combination.

#### 中文译文

作者最后的核心判断是：

> 随着模型变强，有意思的 harness 组合空间并不会缩小。  
> 它只是会不断移动。  
> 而 AI 工程师真正有意思的工作，就是持续去找到下一种新的有效组合。

#### 我的观点

这句是全文最成熟的结论。  
它反对两种天真想法：

- “模型强了，就不需要 harness 了”  
- “一旦搭出一套 harness，以后就永远通用”

真正正确的看法是：

- harness 不是固定答案  
- harness 是一组随着模型和任务边界不断迁移的工程组合

---

## 我的整体总结

如果把这篇文章压成几个最重要的判断，我会记这 7 条：

1. **执行与评审要分离**，不要让 agent 自己当自己的裁判。  
2. **planner / generator / evaluator** 是一种非常健康的职责拆分。  
3. **sprint contract** 非常值钱，它在高层 spec 和可测实现之间建立了“本轮 done 协议”。  
4. **文件通信 / progress / contract** 本质上是协议层，不是日志层。  
5. **每个 harness 组件都是能力补丁**，必须持续验证它是否还在承重。  
6. **evaluator 不是固定必选项**，而是边界任务的增益器。  
7. **harness 不会随着模型变强而消失，只会迁移到新的边界上。**

## 对我们当前协作最直接的启发

结合上一篇 *Effective harnesses for long-running agents*，我觉得对我们最直接的启发有 4 条：

1. **多轮任务要有状态交接工件**，不能只靠聊天记录。  
2. **没有验证证据，不轻易宣布完成。**  
3. **重要任务不要让执行者兼任最终验收者。**  
4. **任何工作流结构都要定期重审哪些组件还在真正承重。**
