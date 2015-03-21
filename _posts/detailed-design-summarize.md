title: '如何做详细设计之总结'
date: 2015-01-03 23:11:44
categories:
	- detailed design
tags:
	- Design pattern
	- java
	- detailed design
---
如何做详细设计系列经过“两年”的时间终于写完了，今天主要为这个系列做下简单的总结。
####详细设计没有“公式”
我们每个人从事的行业，负责的功能模块都不尽相同，不同的业务场景不同的项目特点都会产生不同的设计。可能有人会问，设计模式不是解决特定场景下的“公式”吗？设计模式是前人在具体的实践中，总结出来的解决特定业务场景的一般方法。它也不是公式，仍需要你结合自己的业务场景，具体分析。所以本系列叫如何做详细设计，只是在过去一年中，对自己工作经验的一些总结。你在本系列中看到的所有示例，都是我去年工作中的一部分。在本系列中的观点，是我在当前阶段的对详细设计的认知。也许若干年后甚至几个月后，我也会有不同的看法。所以对如何做详细设计需要有自己的看法，自己的见解。如果你对如何做详细设计有了自己的看法，或者对本系列中的观点有些其他见解，希望能和我讨论。<!--more-->
####详细设计中的“百家争鸣”
在做详细设计时，经常会遇到处理一个问题有多种详细设计方案，而且这些方案都能解决当前的问题。在我们团队中经常会出现这种情况。这也是正常现象，就和每个人写的代码可能都不一样，是一样的问题。这时我们需要分析各种详细设计方案的优缺点。当然这种分析就需要一个人的说服能力，至少需要说服团队中大于一半的人。这样看到一个好的软件工程师也需要有比较好的说服能力，^_^。当然这种说服能力也需要一定的知识贮备和设计经验。即使“争鸣”的最后没有采用你的方案也没关系，如果你的方案是正确的，随着业务的发展，也会慢慢的演变成你的方案。所以详细设计就是这么的有趣。好方案的终归是会被采用的。
####关于本系列
本系列分了六篇来讲如何做详细设计，而且每一节话题都比较大，如果想把每个话题讨论的深入并且更加详细，其实每个话题都可以做为一个系列来写。特别是后面的理解业务的本质、重构、设计模式这三个话题，理解业务的本质其实就是如何做需求分析，重构就是如何做应对需求变化重构详细设计，设计模式就是如何应用设计模式，这三个话题每个话题都是一本书。本系列中的这个三个话题，主要是从在工作中使用过的场景做些简单的应用的阐述。并没有讲具体的怎么操作。确实如何操作讲起来太难了，如何结合业务场景，结合项目情况来确定如何具体的实践。
如果你认真的看过本系列中的每一篇文章，希望看后能对你有一点小小的启发。本系列文章也是在抛砖引玉，如果你对详细设计也有自己的思考，欢迎和我讨论交流。共同进步才是硬道理。
在本系列中，我几乎每个系列都在讲需要多思考、多实践、多总结，本系列也是我一年来的思考、实践的总结。所以思考、实践、总结才是学习如何做详细设计的根本。
####推荐书
* 重构-改善既有代码的设计
* 设计模式
* 大话设计模式（入门级的）
* 重构与模式

####致谢
本系列内容是对过去一年的工作经验的总结和思考，需要感谢znyin，每次对我们的设计check都提出很好的建议，并且给我们分析为什么要这么做。还记得第一次在团队中做详细设计时，当时Dao层的接口命名非常不好，znyin花了一晚上的时间和我一起讨论每个方法的命名，思考如何给每个方法取一个“好”名字。还有几次讨论问题，错过了晚上班车时间，还开车送我回家。正式这种对技术专注、执着、严谨的态度，深深影响了我。需要感谢团队，感谢过去一年中所有帮助我的人。同样感谢每个本系列的读者，感谢您的关注。