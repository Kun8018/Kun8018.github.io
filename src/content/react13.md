---
title: React（十三）
date: 2020-06-02 21:40:33
categories: IT
tags:
    - IT，Web,Node，React
toc: true
thumbnail: https://cdn.kunkunzhang.top/redux.jpeg
---

​      基于react的拓展性的项目

<!--more-->

## SSR

### react api

### react dom api

renderToReadableStream: 将 React 树渲染到 [可读网络流。](https://web.nodejs.cn/en-US/docs/Web/API/ReadableStream)

```javascript
const stream = await renderToReadableStream(reactNode, options?)
```

renderToPipeableStream: 将 React 树渲染为可管道 [Node.js 流。](https://nodejs.cn/api/stream.html)

`renderToStaticMarkup` 将非交互式 React 树渲染为 HTML 字符串

```react
const html = renderToStaticMarkup(reactNode, options?)
```

### 



## wasm-react

在react项目中使用wasm

编写rust函数

```rust
#[wasm_bindgen]
pub fn generate_configuration(node_data: String, edge_data: String) -> String {
    let nodes: Vec<Node> = parse_nodes(&node_data);
    let edges: Vec<Edge> = parse_edges(&edge_data);
    let docker_compose_obj = generate_dockercompose_yml(nodes, edges);
    let output_string = serde_yaml::to_string(&docker_compose_obj).unwrap();
    return output_string;
}
```

添加编译wasm命令

```json
"scripts": {
    ...    
    "build:wasm": "cd wasm-parser && ~/.cargo/bin/wasm-pack build --target web --out-dir ./wasm-build"
}
```

添加在js中调用wasm的包

```json
"dependencies": {
    ...
    "wasm-parser": "file:./wasm-parser/wasm-build"
}
```

在react中调用wasm

```javascript
// WASM modules
import init, { generate_configuration } from "wasm-parser";
// Calling the function 
init().then(() => {
    const rusty_str = generate_configuration(stringifiedNodes, stringifiedEdges);
});
```

https://betterprogramming.pub/deploying-a-wasm-powered-react-app-on-vercel-cf3cae2a75d6

## resume

https://github.com/tbakerx/react-resume-template
