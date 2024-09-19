# any-llm-response-format

在很多场景下，用户需要让大模型严格按照 JSON 格式来输出，以实现输出的结构化，便于后续逻辑进行解析。

- `response_format`是调用`OpenAI API`时设置的一个参数名，通过设置该参数可以在不需要额外提示词或格式化工具的情况下，就能够确保模型输出合法的 JSON 字符串。
- 但可惜的是，并不是所有的模型 API 都支持设置该参数或者存在相似功能的请求参数。
- 大部分的模型 API 都需要额外的提示词和格式化工具来辅助完成，使得主要的工作量在于解析模型结构和非结构化数据到结构化数据的转换。

所以我创建了本项目，提供了一系列工具和方法，能够帮助用户快速解析模型并生成引导提示词（Response format Prompt），并帮助数据从非结构化的文本转换到结构化的实例对象，适用于所有文本生成模型。

# 演示示例和详细解释

[请查阅 Response format - Example](./main.ipynb)

# 项目特点

- 快速生成引导提示词：解析模型和字段信息，生成对应的引导提示词，提示模型输出合法的 JSON 字符串。
- 大模型输出格式化工具：帮助解析大模型的输出内容，并格式化为 JSON 对象，对于不规范的内容会自动尝试修复。
- 非结构化的文本转换到结构化的实例对象：通过提示词引导大模型输出正确的 JSON 格式，解析并格式化得到 JSON 对象能够直接用来实例化为对象。

# 项目背景

在很多场景下，用户需要让模型严格按照 JSON 格式来输出，以实现输出的结构化，便于后续逻辑进行解析。
如`OpenAI API`可以通过设置`response_format`参数为`{'type': 'json_object'}`，能够来确保模型输出合法的 JSON 字符串，示例如下：

```python
response = client.chat.completions.create(
    model="deepseek-chat",
    messages=messages,
    response_format={
        'type': 'json_object'
    }
)
```

除此之外，可以将设置`response_format`参数为模型(`BaseModel`)，来确保模型以 JSON 格式输出相对应的模型结构化结果。

## OpenAI API 与 国内大语言模型 API 的差异

`OpenAI API`可以通过设置参数`response_format`，就能获取到正确的 JSON 格式，不需要在对话中增加额外的引导提示词和格式化工具。
但国内大部分的大语言模型 API 的请求参数中并不支持设置参数`response_format`，虽然国内许多大语言模型都支持通过`OpenAI SDK`进行调用，但如果调用时使用了 API 不支持的参数，就会发生错误。

## 示例以调用智谱 AI 的 API 为例

`智谱AI API`可以通过 `OpenAI SDK` 进行调用，但并不支持设置参数`response_format`。

通过查阅相关文档，得知虽然 GLM-4 系列模型都提供了 JSON Output 能力，但还需要结合提示词引导和格式化工具，才能获取到正确的 JSON 格式。
