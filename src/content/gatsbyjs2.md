---
title: Gatsbyjs
date: 2020-08-02 21:40:33
categories: 技术博客
tags:
    - IT、小程序、H5App
toc: true
thumbnail: http://cdn.kunkunzhang.top/gatsbyjs.png
---

　　其他静态blog

<!--more-->   

## ByteMd

### 安装

https://github.com/bytedance/bytemd



```react
import gfm from '@bytemd/plugin-gfm'
import { Editor, Viewer } from '@bytemd/react'

const plugins = [
  gfm(),
  // Add more plugins here
]

const App = () => {
  const [value, setValue] = useState('')

  return (
    <Editor
      value={value}
      plugins={plugins}
      onChange={(v) => {
        setValue(v)
      }}
    />
  )
}
```



## Nextra

https://github.com/shuding/nextra
