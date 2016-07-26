title: behave教程
id: 1423
categories:
  - 不知所语
tags:
---

*   [behave教程](#behave)

        *   [Features](#features)
    *   [Feature文件](#feature)

            *   [Scenario大纲](#scenario)
        *   [Step数据](#step)
    *   [Python Step实现](#python-step)

            *   [Step参数](#step)
        *   [Context（上下文）](#context)
    *   [环境控制](#)
    *   [使用tag进行控制](#tag)
    *   [Works In Progress](#works-in-progress)
    *   [Debug](#debug)

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
    `</pre>

    这篇散文的“Given”, “When” 和 “Then” 部分构成了behave测试你的系统的实际的step。这些请看[Python step implementations](http://pythonhosted.org/behave/tutorial.html#python-step-implementations),一般来说：

    **Given**：在用户（或外部系统）与系统交互前（“When”中），_把系统置于一个已知状态_。避免在given中涉及用户交互。

    **When**:当我们执行用户（或外部系统）的关键动作。这是会（或者不会）引起一些状态改变的和系统的交互。

    **Then**:我们观察输出。

    你也可以使用“And”或“But”作为一个step - 这些会被behave用特么前一个step的名字重命名，所以：

    <pre>`Scenario: Stronger opponent
      Given the ninja has a third level black-belt
       When attacked by Chuck Norris
       Then the ninja should run for his life
        And fall off a cliff
    `</pre>

    在这个例子中，behave将会寻找定义为"Then fall off a cliff"的step。

    ### Scenario大纲

    有时一个scenario会运行许多的变量来设定一套已知状态、要执行的动作、期望输出，所有这些使用相同的基础动作。你可以使用一个Scenario Outline来归档：

    <pre>`Scenario Outline: Blenders
       Given I put &lt;thing&gt; in a blender,
        when I switch the blender on
        then it should transform into &lt;other thing&gt;

     Examples: Amphibians
       | thing         | other thing |
       | Red Tree Frog | mush        |

     Examples: Consumer Electronics
       | thing         | other thing |
       | iPhone        | toxic waste |
       | Galaxy Nexus  | toxic waste |
    `</pre>

    behave会运行scenario一次为了示例数据表中出现的每一行（不含标题）。

    ### Step数据

    有时，把数据表和你的step关联是有用处的。

    任何"""包起来的行组成的文本块将会和这个step关联。例如：

    <pre>`Scenario: some scenario
      Given a sample text loaded into the frobulator
         """
         Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do
         eiusmod tempor incididunt ut labore et dolore magna aliqua.
         """
     When we activate the frobulator
     Then we will find it similar to English
    `</pre>

    Python step代码可以通过传给每个step函数的Context变量的.text属性来调用该文本。

    你也可以通过在step后面有缩进的录入一个表格数据把它和step关联。这在把精确的需求数据载入model时是有用的。

    <pre>`Scenario: some scenario
      Given a set of specific users
         | name      | department  |
         | Barry     | Beer Cans   |
         | Pudey     | Silly Walks |
         | Two-Lumps | Silly Walks |

     When we count the number of people in each department
     Then we will find two people in "Silly Walks"
      But we will find one person in "Beer Cans"
    `</pre>

    Python step代码可以通过传给每个step函数的Context变量的.table属性来调用该文本。上面例子的table可以这样使用：

    <pre>`@given('a set of specific users')
    def step_impl(context):
        for row in context.table:
            model.add_user(name=row['name'], department=row['department'])
    `</pre>

    有很多方式使用table数据 - 参见[Tabe](http://pythonhosted.org/behave/api.html#behave.model.Table) API文档获得全部细节。

    ## Python Step实现

    ### Step参数

    你可能会发现你的feature step有时包含仅带有一些变量的共同短语。例如：

    <pre>`Scenario: look up a book
      Given I search for a valid book
       Then the result page will include "success"

    Scenario: look up an invalid book
      Given I search for a invalid book
       Then the result page will include "failure"
    `</pre>

    你可以定义一个Python step处理这两个Then子句（带有一个把一些文本存入context.response的Given step）：

    <pre>`@then('the result page will include "{text}"')
    def step_impl(context, text):
       if text not in context.response:
           fail('%r not in %r' % (text, context.response))

(默认情况下)behave有一些语法分析器：

**parse(默认值，基于：[parse](https://pypi.python.org/pypi/parse))**

**cfparse（[parse](https://pypi.python.org/pypi/parse)扩展，需要[parse_type](https://pypi.python.org/pypi/parse_type)）**

**re**

### Context（上下文）

## 环境控制

## 使用tag进行控制

## Works In Progress

## Debug