title: behave教程
id: 1421
categories:
  - 不知所语
tags:
---

# behave教程

首先，安装behave

现在建立“tutorial”目录。在该目录里创建“tutorial.feature”文件，包含：

    Feature: showing off behave

      Scenario: run a simple test
         Given we have behave installed
          when we implement a test
          then behave will test it for us!
    `</pre>

    创建“tutorial/steps”目录，在该目录中添加“tutorial.py”文件，包含：

    <pre>`from behave import *

    @given('we have behave installed')
    def step_impl(context):
        pass

    @when('we implement a test')
    def step_impl(context):
        assert True is not False

    @then('behave will test it for us!')
    def step_impl(context):
        assert context.failed is False
    `</pre>

    运行behave:

    <pre>`% behave
    Feature: showing off behave # tutorial/tutorial.feature:1

      Scenario: run a simple test        # tutorial/tutorial.feature:3
        Given we have behave installed   # tutorial/steps/tutorial.py:3
        When we implement a test         # tutorial/steps/tutorial.py:7
        Then behave will test it for us! # tutorial/steps/tutorial.py:11

    1 feature passed, 0 failed, 0 skipped
    1 scenario passed, 0 failed, 0 skipped
    3 steps passed, 0 failed, 0 skipped, 0 undefined
    `</pre>

    现在，继续阅读以学习如何利用behave。

    ## Features

    behave操作的目录包括：
    1\. feature文件，由你的业务分析员、发起人或任何和你的行为场景有关的人，和
    2\. “steps”目录，包含scenario的Python step实现。

    你可以可选的包含一些环境控制文件（steps、scenarios、features或者 shooting match（这是啥？））。

    features目录的最小要求是：

    <pre>`features/
    features/everything.feature
    features/steps/
    features/steps/steps.py
    `</pre>

    一个复杂的目录可能是：

    <pre>`features/
    features/signup.feature
    features/login.feature
    features/account_details.feature
    features/environment.py
    features/steps/
    features/steps/website.py
    features/steps/utils.py
    `</pre>

    如果你在运行时遇到问题，或者想知道behave在寻找你的feature时做了什么，你可以使用“-v”命令行开关。

    ## Feature文件

    feature文件用自然语言格式描述一个feature或者feature的一部分，典型的例子是期望输出。他们是纯文本文件（UTF-8编码），看起来像：

    <pre>`Feature: Fight or flight
      In order to increase the ninja survival rate,
      As a ninja commander
      I want my ninjas to decide whether to take on an
      opponent based on their skill levels

      Scenario: Weaker opponent
        Given the ninja has a third level black-belt
         When attacked by a samurai
         Then the ninja should engage the opponent

      Scenario: Stronger opponent
        Given the ninja has a third level black-belt
         When attacked by Chuck Norris
         Then the ninja should run for his life

这篇散文的“Given”, “When” 和 “Then” 部分构成了behave测试你的系统的实际的step。这些请看[Python step implementations](http://pythonhosted.org/behave/tutorial.html#python-step-implementations),一般来说：

**Given**：在用户（或外部系统）与系统交互前（“When”中），_把系统置于一个已知状态_。避免在given中涉及用户交互。

**When**:当我们执行用户（或外部系统）的关键动作。这是会（或者不会）引起一些状态改变的和系统的交互。

**Then**:我们观察输出。