## 四、如何使用

文本生成模型将接收的信息作为提示（Prompt），并返回一个根据提示信息生成的输出。百炼支持 OpenAI SDK、DashScope SDK、HTTP 接入方式。

### 1. 消息类型

- **系统消息**（System Message）：用于告知模型要扮演的角色或行为。默认值是 “You are a helpful assistant”。
- **用户消息**（User Message）：您输入给模型的文本。
- **助手消息**（Assistant Message）：模型的回复。

### 2. 快速开始

#### API 使用前提

已获取 API Key 并完成配置 API Key 到环境变量。

#### 示例代码


HTTP 请求：
curl -X POST https://dashscope.aliyuncs.com/compatible-mode/v1/chat/completions \
-H "Authorization: Bearer $DASHSCOPE_API_KEY" \
-H "Content-Type: application/json" \
-d '{
    "model": "deepseek-r1",
    "messages": [
        {
            "role": "user", 
            "content": "9.9和9.11谁大"
        }
    ]
}'


3. 返回结果
{
    "choices": [
        {
            "message": {
                "content": "9.9比9.11大。\n\n**解析：**\n1. **统一小数位数：** 将9.9补位为9.90，与9.11对齐位数。\n2. **逐位比较：**\n   - 整数部分均为9，相等。\n   - 第一位小数：9（来自9.90）> 1（来自9.11），此时已可判定大小。\n3. **结论：** 9.90 > 9.11，即 **9.9 > 9.11**。\n\n**常见误解提醒：**  \n若直接比较小数点后的数字（如误认为“11 > 9”），可能得出错误结论。需注意小数位的实际值（十分位、百分位等），补零对齐后再比较。",
                "reasoning_content": "\n嗯，用户问的是9.9和9.11谁大。这个问题看起来好像挺简单的，但可能有些小陷阱，特别是涉及到小数点的比较。首先，我需要确认用户是否在问数字的大小比较，还是有没有其他可能的含义，比如日期之类的。不过根据数字的写法，应该是数字比较。\n\n首先，我需要明确这两个数的结构。9.9和9.11。这时候可能有些人会直接比较小数点后的数字，比如9和11，认为11比9大，所以9.11更大。但这种比较方法其实是错误的，因为小数点后的位数不同，需要将它们转换成相同位数来比较。\n\n正确的做法应该是将两个数统一到相同的小数位数。比如，9.9可以写成9.90，而9.11保持不变。这样的话，比较的话就是9.90和9.11。这时候，从小数点后第一位开始比较，第一位都是9，接着第二位是0和1。因为0小于1，所以9.90小于9.11，也就是9.9小于9.11。\n\n不过，用户可能有不同的理解，特别是如果他们对小数比较不熟悉的话，可能会误以为9.11更大，因为11比9大。这时候需要解释清楚位数的不同带来的影响，比如小数点后的第一位是十分位，第二位是百分位，所以9.9实际上是9又十分之九，而9.11是9又百分之十一，换算成小数的话，0.9等于0.90，而0.11是0.11，显然0.90更大，所以这个时候反而9.9更大？\n\n啊，这里好像我之前的分析有问题。这时候需要再仔细计算一下。因为当我们将9.9写成9.90，而9.11是9.11的话，比较的话应该从高位到低位依次比较。比如，整数部分都是9，所以相同。然后第一位小数，9和1比较，9大于1，所以这时候9.90已经比9.11大了，所以结论是9.9大于9.11？\n\n等等，这里好像矛盾了。这说明我之前犯了错误。为什么会这样呢？原来在比较小数时，应该逐位比较。例如：\n\n9.9 和 9.11\n\n将9.9写成9.90：\n\n比较整数部分：都是9，相等。\n比较第一位小数：9（来自9.90）和1（来自9.11）。因为9 > 1，所以整个数9.90大于9.11，所以9.9大于9.11。\n\n这样一来，之前的结论是错误的，正确的结论应该是9.9更大。这说明我需要仔细检查自己的思路，避免犯低级错误。\n\n可能用户在这里容易混淆，因为9.11的小数部分有两位，可能以为0.11比0.9大，但实际上0.9是0.90，而0.90大于0.11。所以正确的比较是9.9更大。\n\n总结来说，正确的答案是9.9比9.11大。需要确保在比较的时候注意小数位数，补全后再逐位比较。\n",
                "role": "assistant"
            },
            "finish_reason": "stop",
            "index": 0,
            "logprobs": null
        }
    ],
    "object": "chat.completion",
    "usage": {
        "prompt_tokens": 19,
        "completion_tokens": 797,
        "total_tokens": 816
    },
    "created": 1739069910,
    "system_fingerprint": null,
    "model": "deepseek-r1",
    "id": "chatcmpl-e55cdb8a-9ce1-9662-b87c-cf3da706e4f3"
}

百炼提供的 DeepSeek API 默认不会记录您的历史对话信息。多轮对话功能可以让大模型“拥有记忆”，满足如追问、信息采集等需要连续交流的场景。如果您使用 DeepSeek-R1 类模型，会收到reasoning_content字段（思考过程）与content（回复内容），您可以将content字段通过{'role': 'assistant', 'content':API 返回的content}添加到上下文，无需添加reasoning_content字段。

流式输出

DeepSeek-R1 类模型可能会输出较长的思考过程，为了降低超时风险，建议您使用流式输出方式调用 DeepSeek-R1 类模型。

curl -X POST https://dashscope.aliyuncs.com/compatible-mode/v1/chat/completions \
-H "Authorization: Bearer $DASHSCOPE_API_KEY" \
-H "Content-Type: application/json" \
-d '{
    "model": "deepseek-r1",
    "messages": [
        {
            "role": "user", 
            "content": "9.9和9.11谁大"
        }
    ],
    "stream": true,
    "stream_options": {
        "include_usage": true
    }
}'

data: {"choices":[{"delta":{"content":"","reasoning_content":"","role":"assistant"},"finish_reason":null,"index":0,"logprobs":null}],"object":"chat.completion.chunk","usage":null,"created":1739071776,"system_fingerprint":null,"model":"deepseek-r1","id":"chatcmpl-a351a53b-00a6-9cf6-9c64-ce491bc462a8"}

data: {"choices":[{"delta":{"content":"","reasoning_content":"\n"},"finish_reason":null,"index":0,"logprobs":null}],"object":"chat.completion.chunk","usage":null,"created":1739071776,"system_fingerprint":null,"model":"deepseek-r1","id":"chatcmpl-a351a53b-00a6-9cf6-9c64-ce491bc462a8"}

data: {"choices":[{"delta":{"content":"","reasoning_content":"好的"},"finish_reason":null,"index":0,"logprobs":null}],"object":"chat.completion.chunk","usage":null,"created":1739071776,"system_fingerprint":null,"model":"deepseek-r1","id":"chatcmpl-a351a53b-00a6-9cf6-9c64-ce491bc462a8"}

data: {"choices":[{"delta":{"content":"","reasoning_content":"，"},"finish_reason":null,"index":0,"logprobs":null}],"object":"chat.completion.chunk","usage":null,"created":1739071776,"system_fingerprint":null,"model":"deepseek-r1","id":"chatcmpl-a351a53b-00a6-9cf6-9c64-ce491bc462a8"}

data: {"choices":[{"delta":{"content":"","reasoning_content":"我现在"},"finish_reason":null,"index":0,"logprobs":null}],"object":"chat.completion.chunk","usage":null,"created":1739071776,"system_fingerprint":null,"model":"deepseek-r1","id":"chatcmpl-a351a53b-00a6-9cf6-9c64-ce491bc462a8"}

......

data: {"choices":[{"delta":{"content":"{","reasoning_content":""},"finish_reason":null,"index":0,"logprobs":null}],"object":"chat.completion.chunk","usage":null,"created":1739071797,"system_fingerprint":null,"model":"deepseek-r1","id":"chatcmpl-a2282bc4-2a01-99c5-a0a6-c1862a058f19"}

data: {"choices":[{"delta":{"content":"9","reasoning_content":""},"finish_reason":null,"index":0,"logprobs":null}],"object":"chat.completion.chunk","usage":null,"created":1739071797,"system_fingerprint":null,"model":"deepseek-r1","id":"chatcmpl-a2282bc4-2a01-99c5-a0a6-c1862a058f19"}

data: {"choices":[{"delta":{"content":".","reasoning_content":""},"finish_reason":null,"index":0,"logprobs":null}],"object":"chat.completion.chunk","usage":null,"created":1739071797,"system_fingerprint":null,"model":"deepseek-r1","id":"chatcmpl-a2282bc4-2a01-99c5-a0a6-c1862a058f19"}

data: {"choices":[{"delta":{"content":"9","reasoning_content":""},"finish_reason":null,"index":0,"logprobs":null}],"object":"chat.completion.chunk","usage":null,"created":1739071797,"system_fingerprint":null,"model":"deepseek-r1","id":"chatcmpl-a2282bc4-2a01-99c5-a0a6-c1862a058f19"}

data: {"choices":[{"delta":{"content":"}\n","reasoning_content":""},"finish_reason":null,"index":0,"logprobs":null}],"object":"chat.completion.chunk","usage":null,"created":1739071797,"system_fingerprint":null,"model":"deepseek-r1","id":"chatcmpl-a2282bc4-2a01-99c5-a0a6-c1862a058f19"}

data: {"choices":[{"delta":{"content":"\\]","reasoning_content":""},"finish_reason":null,"index":0,"logprobs":null}],"object":"chat.completion.chunk","usage":null,"created":1739071797,"system_fingerprint":null,"model":"deepseek-r1","id":"chatcmpl-a2282bc4-2a01-99c5-a0a6-c1862a058f19"}

data: {"choices":[{"finish_reason":"stop","delta":{"content":""},"index":0,"logprobs":null}],"object":"chat.completion.chunk","usage":null,"created":1739071797,"system_fingerprint":null,"model":"deepseek-r1","id":"chatcmpl-a2282bc4-2a01-99c5-a0a6-c1862a058f19"}

data: {"choices":[],"object":"chat.completion.chunk","usage":{"prompt_tokens":13,"completion_tokens":1436,"total_tokens":1449},"created":1739071797,"system_fingerprint":null,"model":"deepseek-r1","id":"chatcmpl-a2282bc4-2a01-99c5-a0a6-c1862a058f19"}

data: [DONE]

注意事项

稳定性：如果执行后没有响应、响应超时或者报错An internal error has occured, please try again later or contact service support，请尝试重试或者更换其他DeepSeek模型，也可以尝试使用Qwen最新模型qwen-max-2025-01-25。
高峰期任务可能排队或失败，阿里云百炼持续扩容中，调用失败请稍后重试。
DeepSeek-R1 类模型
不支持的功能
Function Calling、JSON Output、对话前缀续写、上下文硬盘缓存。
不支持的参数
temperature、top_p、presence_penalty、frequency_penalty、logprobs、top_logprobs。
设置这些参数都不会生效，即使没有输出错误提示。
不建议设置 System Message。