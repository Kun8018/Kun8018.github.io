---
title: React Native入门
date: 2020-04-02 21:40:33
categories: IT
tags:
    - IT，RN，
toc: true
thumbnail: https://s1.ax1x.com/2020/03/27/G9OXNR.png
---

　　React Native组件

<!--more-->

## vaul

适用于平板和手机的抽屉

安装

```shell
npm install vaul
```

使用

```react
import { Drawer } from 'vaul';

function MyComponent() {
  return (
    <Drawer.Root>
      <Drawer.Trigger>Open</Drawer.Trigger>
      <Drawer.Portal>
        <Drawer.Content>
          <Drawer.Title>Title</Drawer.Title>
        </Drawer.Content>
        <Drawer.Overlay />
      </Drawer.Portal>
    </Drawer.Root>
  );
}
```

