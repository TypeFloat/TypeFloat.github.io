---
title: ChatGPT提示工程
tags: "gpt-prompt"
key: gpt-prompt
---

这一篇笔记是参考了有吴恩达教授和OpenAI合作出品的提示工程教学，[视频戳这儿](https://learn.deeplearning.ai/chatgpt-prompt-eng/lesson/1/introduction)可以观看，我把关键内容进行了整理，方便回顾和查阅。<!--more-->

# 如何使用API

openai开发了便于使用ChatGPT的API接口工具包，直接使用`pip install openai`即可。

```python
import openai import os
from doten import load_doten, find_ dotenv
= load doten(find doten()) # read local .env file
openai.api_key = os •getenv ( 'OPENAI_API_KEY')

def get_completion(prompt, model="gpt-3.5-turbo"):
    messages = [{"role":"user", "content": prompt}]
    response = openai. ChatCompletion.create(
    	model=model,
        messages=messages,
    	temperature=0,
    )
    return response.choices[0].message["content"]
```

下述的所有示例都是通过调用API实现的，当然，也可以直接在网页端进行提问，一样可以获得准确的回答。

##提示工程的基本原则

## 1. 指令尽可能的明确和具体

尽可能明确和具体的指令将减少模型输出无关或不正确的响应，并且更长的提示可以提供更多的上下文，来帮助模型理解任务需要。

### 方法一

使用特定的分隔符，例如`"""`、`---`、`<>`、`<tag></tag>`，例如想使用ChatGPT来提取一段话的摘要，我们需要给模型提供两部分的提示，一部分是任务要求，一部分是文本内容，使用特定的分隔符来表示文本内容可以帮助模型更好的区分提示，并且可以防止恶意的攻击。

```python
text = f"""
You should express what you want a model to do by \
providing instructions that are as clear and \
specific as you can possibly make them. \
This will guide the model towards the desired output, \
and reduce the chances of receiving irrelevant \
or incorrect responses. Don't confuse writing a \
clear prompt with writing a short prompt. \
In many cases, longer prompts provide more clarity \
and context for the model, which can lead to \
more detailed and relevant outputs.
"""
prompt = f"""
Surmarize the text delimited by triple backticks \ into a single sentence.
'''{text}'''
"""
response = get_completion(prompt)
print(response)
```

### 方法二

尝试让模型给出结构化的输出，例如`HTML`或`JSON`，例如想使用ChatGPT来提取一段话中的信息，可以尝试让模型以JSON格式进行输出，这样做的另一个好处是，其返回的结果可以直接读取作进一步的处理。

```python
prompt = f"""
Generate a list of three made-up book titles along \
with their authors and genres.
Provide them in JSON format with the following keys:
book_id, title, author, genre.
"""
response = get_completion(prompt)
print(response)
```

### 方法三

要求模型检查是否满足条件，如果不满足条件，则指示并停止尝试完全完成任务，即直接告诉模型，对于不合规的输入或边缘情况怎么进行处理。例如下述例子，想通过ChatGPT整理一段教程中的步骤，如果存在分步，则给出，如果不存在，就输出没有分步。

```python
text = f"""
Making a cup of tea is easy! First, you need to get some \
water boiling. While that's happening, \
grab a cup and put a tea bag in it. Once the water is \
hot enough, just pour it over the tea bag. \
Let it sit for a bit so the tea can steep. After a \
few minutes, take out the tea bag. If you \
like, you can add some sugar or milk to taste. \
And that's it! You've got yourself a delicious \
cup of tea to enjoy.
"""
prompt = f"""
You will be provided with text delimited by triple quotes.
If it contains a sequence of instructions, \
re-write those instructions in the following format:
Step 1 - ..
Step 2 -
Step N -
If the text does not contain a sequence of instructions, then simply write \"No steps provided.\"

'''{text}'''
"""
response = get_completion(prompt)
print("Completion for Text:")
print(response)
```

### 方法四

在模型执行任务之前，给出一个正确的示例。

```python
prompt = f"""
Your task is to answer in a consistent style.

<child>: Teach me about patience.

<grandparent>: The river that carves the deepest \
valley flows from a modest spring; the \
grandest symphony originates from a single note; \
the most intricate tapestry begins with a solitary thread.

<child>: Teach me about resilience.
"""
response = get_completion(prompt)
print(response)
```

## 2. 给模型更多的时间进行思考

给模型更多时间进行思考的意思并不是让模型的运行速度变慢，这由不得我们控制，而是让引导模型进行更多的计算。

### 方法一

指定完成任务所需要的步骤。尽可能细致的引导模型分步完成任务，而不是直接给他一个最终目标，这样会让模型进行更多的计算，从而获取更正确的回答。

```python
text = f"""
In a charming village, siblings Jack and Jill set out on \
a quest to fetch water from a hilltop \
well. As they climbed, singing joyfully, misfortune \
struck-Jack tripped on a stone and tumbled \
down the hill, with Jill following suit. I Though slightly battered, the pair returned home to \
comforting embraces. Despite the mishap, \
their adventurous spirits remained undimmed, and they \
continued exploring with delight.
"""

prompt = f"""
Perform the following actions:
1 - Summarize the following text delimited by triple \
backticks with 1 sentence.
2 - Translate the Mummary into French.
3 - List each name n the French summary.
4 - Output a json object that contains the following \
keys: french summary, num_names.
Separate your answers with line breaks.

Text:
```{text}```
"""
response = get_ompletion(prompt)
print("Completion for prompt:")
print(response)
```

### 方法二

在需要让ChatGPT判断一件事情是否正确时，先让他自己进行思考，而不是直接给出我们的答案让他进行评判。

```python
prompt = f"""
Your task is to determine if the student's solution \
is correct or not.
To solve the problem do the following:
- First, work out your own shlution to the problem.
- Then compare your solution to the student's solution \
and evaluate if the student's solution is correct or not.
Don't decide if the student's solution is correct until you have done the problem yourself.

Use the following format:
Question:
'''
question here
'''
Student's solution:
'''
student's solution here
'''
Actual solution:
'''
steps to work out the solution and your solution here
'''
Is the student's solution the same as actual solution \
just calculated:
'''
yes or no
'''
Student grade:
'''
correct or incorrect
'''

Question:
'''
I'm building a solar power installation and I need help \
working out the financials.
- Land costs $100 / square foot
- I can buy solar panels for $250 / square foot
- I negotiated a contract for maintenance that will cost \
me a flat $100k per year, and an additional $10 / square foot
What is the total cost for the first year of operations \
as a function of the number of square feet.
'''
Student's solution:
'''
Let × be the size of the installation in square feet.
Costs:
1. Land cost: 100x
2. Solar panel cost: 250x
3. Maintenance cost: 100,000 + 100x
rotal cost: 100x + 250x + 100,000 +100x =450x + 100.000
'''
Actual solution:
"""
response = get_completion(prompt)
print(response)
```

但也许是因为给出的提示过于复杂了，模型并没有每次都按照既定的输出格式进行输出。

# 模型的局限性

即使模型在训练中获得了大量的知识，但它并不是记住了自己所看到的信息，因此它并不了解知识的边界。这意味着，当模型在尝试回答关于晦涩主题的问题时，会编造一些听起来合理但实际上不正确的内容，这种现象称为幻觉。例如以下的示例：

```python
prompt = f"""
Tell me about AeroGlide Ultraslim Smart Toothbrush by Boie
"""
response = get_completion(prompt)
print(response)
```

事实上，现实中并不存在该产品，但模型的回答依然非常真实。

减少这种幻觉的方法之一是引导模型找到相关的引用，也就是我们需要给定一段正确的信息文本，来让模型进行提取。例如通过搜索获取一些真实存在的信息，再交给GPT进行总结。



课程的剩下几章是关于具体应用的示例，例如总结、推理、转换、扩展、聊天机器人，这里就不在展开赘述。