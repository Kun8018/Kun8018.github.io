---
title: ChatGPT
date: 2020-03-06 21:40:33
categories: 技术博客
tags:
    - IT,Web，C++
toc: true
thumbnail: 
---

​       chatGPT/大模型/AI很火，同时感觉也很有用，涉及相关

<!--more-->

## 相关工具

### Chinese-Vicuna

对中文进行优化之后对vicuna

https://github.com/Facico/Chinese-Vicuna

### FastChat

独立部署，可达到90%的chat GPT4

https://github.com/lm-sys/FastChat



### LoRa

训练大模型的工具

https://github.com/microsoft/LoRA



### MiniGPT-4

目前使用Vicuna-7B

https://github.com/Vision-CAIR/MiniGPT-4

### gpt4-free

https://github.com/xtekky/gpt4free



### ecoute

收到语音之后马上转换为语音回答

https://github.com/SevaSk/ecoute



## Node

### openapi-typescript

将openapi的schema转换为typescript的类型

https://github.com/drwpow/openapi-typescript



## openchat

自定义chatgpt模型

https://openchat.so/

https://github.com/openchatai/OpenChat



## Nextjs

安装ai包

```shell
npm install ai
```

使用

```react
import { OpenAIStream, StreamingTextResponse } from 'ai'
import { Configuration, OpenAIApi } from 'openai-edge'

// Create an OpenAI API client (that's edge friendly!)
const config = new Configuration({
  apiKey: process.env.OPENAI_API_KEY
})
const openai = new OpenAIApi(config)
 
// IMPORTANT! Set the runtime to edge
export const runtime = 'edge'
 
export async function POST(req: Request) {
  // Extract the `messages` from the body of the request
  const { messages } = await req.json()
 
  // Ask OpenAI for a streaming chat completion given the prompt
  const response = await openai.createChatCompletion({
    model: 'gpt-3.5-turbo',
    stream: true,
    messages
  })
  // Convert the response into a friendly text-stream
  const stream = OpenAIStream(response)
  // Respond with the stream
  return new StreamingTextResponse(stream)
}
```

react使用hooks

```react
'use client'

import { useChat } from 'ai/react'

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit } = useChat()

  return (
    <div>
      {messages.map(m => (
        <div key={m.id}>
          {m.role}: {m.content}
        </div>
      ))}

      <form onSubmit={handleSubmit}>
        <input
          value={input}
          placeholder="Say something..."
          onChange={handleInputChange}
        />
      </form>
    </div>
  )
}
```



https://vercel.com/blog/introducing-the-vercel-ai-sdk
