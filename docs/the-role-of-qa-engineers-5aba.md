# 质量保证工程师的角色

> 原文：<https://dev.to/angelariggs/the-role-of-qa-engineers-5aba>

*本文最初发表在我的博客上，[http://Angela Riggs . github . io](http://angelariggs.github.io/blog/role-of-qa-engineers)。*

* * *

每个人都同意开发人员的工作应该通过 QA，以确保工作的高质量，但是有多少公司有专门的 QA 工程师来推动这一愿景？这有点像围绕测试驱动开发的态度——这绝对是一个伟大的想法，人们支持这个理论，但在实践中它经常被忽视或事后想起。

我认为，对于 QA 的角色应该是什么，也可能缺乏理解或思考。质量保证工程师是科技公司的巨大财富，他们帮助确保和提高生产的质量。我自己的经历是一个大杂烩。我们的 sow 和 sprint 估计包括分配给开发人员、QA 和 pm 的时间。理论上，我估计每次冲刺的时间从 0%到 20%不等。有时候是 100%，全部放在倒数第二的冲刺中进行“QA 和硬化”。对于大多数项目来说，每个 sprint 20%是非常合理的，但是其他场景不仅不合理，它们还传达了一个 QA 不受重视的信息。

不幸的是，坚持高标准有时被认为是部署工作的唯一障碍。即使在一个紧密团结、沟通良好的团队中工作，也不总是很容易推后某人的工作。我曾经和一个开发人员一起工作，他肯定将 QA 视为一个障碍。他会在他的简历中遗漏一些步骤，当我不得不要求澄清时，他会明显地变得不耐烦。当我问问题，或者他的票没有通过问答时，他的第一反应总是“在我的电脑上工作。”其他常见的回答包括问我是否点击了保存，以及拒绝回答问题(“我恢复了所有功能，但这个拒绝恢复，这是意料之中的吗？”被问及“你不认为它应该恢复的话会恢复吗？”).这非常令人沮丧。当关于你工作的最响亮、最一致的声音认为你不称职时，这就形成了一个相当紧张的工作环境。

质量保证不仅仅是一个被动的角色，在票证排队时通过或失败。我们必须积极主动；不仅仅是解决问题，还要在问题出现之前发现问题。一个好的 QA 工程师必须在支持开发者和产品所有者之间找到平衡。如果一个开发人员或客户说“我认为这不对”，QA 工程师可以给出硬数据来确认或澄清，并提出改进建议。我们必须根据验收标准进行测试，但也要考虑客户或最终用户的隐含需求。

我选择退回那些满足明确的接受标准，但缺乏管理经验，或者没有满足潜在客户需求的票证。一个例子是为网站的 FAQ 部分创建一个可排序的列表。开发人员最初的实现是一个加权列表，内容管理员可以从下拉列表中选择不同的权重，并对 FAQ 列表进行重新排序。仅从票证的接受标准来看，我本可以将此票证转发给 deploy。相反，我选择要求一个不同的解决方案——权重选择没有在后端实时重新排序项目；在 FAQ 上选择一个体重并不能强制改变另一个的体重，所以如果你想改变一个的话，你必须改变所有的选择。我请求一个可拖动的排序，管理员可以重新排列他们的 FAQ 列表，很容易看到最终的结果会是什么样子。开发人员修改了它，把它送回给另一轮的 QA，然后它就被部署了——因为我的输入，因为我的输入被接受了，所以它是一个更好的特性。

作为 QA 工程师，我寻找改善管理员和用户体验的方法，找出测试的方法和工具，并在相互竞争的关注领域之间交流需求。QA 工程师是加强客户关系的桥梁，在客户和开发者之间建立信任，并确保项目成为骄傲的源泉。

QA 如何在你的团队或公司中发挥作用？你是如何迭代改进 QA 和你的 QA 工程师的角色的？T3】