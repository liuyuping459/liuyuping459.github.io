---
layout: post
title: "Drools Demo"
date: 2015-06-10 16:50:10
author: liuyp
categories: drools
tags: drools
---

* content
{:toc}

## 应用drools规则引擎做产生式规则不确定性推理

*drools规则引擎，能以rete算法为核心进行高效的规则匹配。我们可以借助其高效的匹配能力，完成产生式规则的推理工作。
rete网在首次加载完成之后，其加载和推理的时间随着规则的增加，仅有很小幅度的增加。




推理需要连续的匹配动作，所以我们要控制drools循环不断地进行规则匹配，并记录每一步过程。每次匹配成功，都做相应的记录，并将该规则的后件加入事实库，同时关闭该规则。再用现有的事实库寻求下一个能成功匹配的规则，直至没有规则可以匹配，获得我们的记录，就获得了推理链。至于匹配过程中同时有多条规则可以匹配的问题，由agenda机构处理，我们不用过多担心。

产生式规则的不确定性推理，我们要考虑到规则前件的可信度，前件的权重，和规则的可信度，这些相关的计算和处理，都可以在每次匹配成功后做相应记录的时候完成。
---
## 实现

*我做了一个实现的[Demo](https://github.com/liuyuping459/FuzzyReasoningDemo)，托管在[我的github](https://github.com/liuyuping459)。其中实现了上述的推理能力。

我们可以在控制台输入相应的事实，就可以得到根据我们给定的规则获得的推理链。由于我手里没有能拿来公开演示的真实规则，所以[Demo](https://github.com/liuyuping459/FuzzyReasoningDemo)中只是用了并没有实际意义的名称和字母组成的规则，只是作为示例。

### 关于规则

		rule "7"		//规则编号
		salience 4		//配合循环控制规则
		no-loop true	//防止规则重复执行
	    
	    when
	    	$message10 : Message(a == 10)	//规则前件匹配元素10、15
	    	$message15 : Message(a == 15)
	    	$assistant : Assistant()	//获取记录推理链的辅助类
	    
	    then
	    	//System.out.println(7);

			Map<Integer, Double> factAndWeight = new HashMap<Integer, Double>();
	 		factAndWeight.put(10, 0.6);		//前件元素10，占前件权重60%
	 		factAndWeight.put(15, 0.4);		//前件元素15，占前件权重40%
	 		$assistant.inferenceOneStep(26, factAndWeight, 95);		//后件元素26，规则可信度95%

	 		insert(new Message(0));
		end

---