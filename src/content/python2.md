---
title: Python
date: 2020-03-011 21:40:33
categories: IT
tags:
    - IT，Web,数据库
toc: true
thumbnail: 
---

　　python语法简单，应用广泛，好好学习。

<!--more-->

https://github.com/jackfrued/Python-100-Days

## Python库

### paramiko

https://github.com/paramiko/paramiko



### gevent

python 程序实现的一种单线程下的多任务执行调度器，简单来说在一个线程里，先后执行 AB 两个任务，但是当 A 遇到耗时操作（网络等待、文件读写等），这个时候 gevent 会让 A 继续执行，但是同时也会开始执行 B 任务，如果 B 在遇到耗时操作同时 A 又执行完了耗时操作，gevent 又继续执行 A



### asyncio

**event_loop** 事件循环：理解为一个循环的池，里面存放一些 async 关键词定义的协程函数，只有放到循环池里才能执行

**coroutine** 协程：协程对象，指一个使用 async 关键字定义的函数，它的调用不会立即执行函数，而是会返回一个协程对象。协程对象需要注册到事件循环，由事件循环调用。

**task** 任务：一个协程对象就是一个原生可以挂起的函数，任务则是对协程进一步封装，其中包含任务的各种状态。

**future**：代表将来执行或没有执行的任务的结果。它和 task 上没有本质的区别

**async/await** 关键字：python3.5 用于定义协程的关键字，async 定义一个协程，await 用于挂起阻塞的异步调用接口。



### hiredis



### grequests



### celery

python分布式队列库

https://docs.celeryq.dev/en/stable/index.html



### uv

python包管理工具

https://github.com/astral-sh/uv



### ruff

python linter

https://astral.sh/ruff

### psutil

获取系统内存、cpu、磁盘等信息

https://github.com/giampaolo/psutil



## Django入门

### Django版本与python版本对应关系

| Django版本 | Python版本                                |
| ---------- | ----------------------------------------- |
| 1.8        | 2.7、3.2、3.3、3.4、3.5                   |
| 1.9、1.10  | 2.7、3.4、3.5                             |
| 1.11       | 2.7、3.4、3.5、3.6、3.7（Django 1.11.17） |
| 2.0        | 3.4、3.5、3.6、3.7                        |
| 2.1        | 3.5、3.6、3.7                             |
| 2.2        | 3.5、3.6、3.7、3.8（Django 2.2.8）        |
| 3.0        | 3.6、3.7、3.8                             |

### 创建应用

执行命令

```shell
python manage.py startapp first
```



### DRF

在Django项目中，如果要实现REST架构，即将网站的资源发布成REST风格的API接口，可以使用著名的三方库`djangorestframework` ，我们通常将其简称为DRF。

安装DRF

```shell
pip install djangorestframework
```

配置DRF

```python
INSTALLED_APPS = [
    'rest_framework',
]

# 下面的配置根据项目需要进行设置
REST_FRAMEWORK = {
    # 配置默认页面大小
    # 'PAGE_SIZE': 10,
    # 配置默认的分页类
    # 'DEFAULT_PAGINATION_CLASS': '...',
    # 配置异常处理器
    # 'EXCEPTION_HANDLER': '...',
    # 配置默认解析器
    # 'DEFAULT_PARSER_CLASSES': (
    #     'rest_framework.parsers.JSONParser',
    #     'rest_framework.parsers.FormParser',
    #     'rest_framework.parsers.MultiPartParser',
    # ),
    # 配置默认限流类
    # 'DEFAULT_THROTTLE_CLASSES': (
    #     '...'
    # ),
    # 配置默认授权类
    # 'DEFAULT_PERMISSION_CLASSES': (
    #     '...',
    # ),
    # 配置默认认证类
    # 'DEFAULT_AUTHENTICATION_CLASSES': (
    #     '...',
    # ),
}
```



### JWT

用三方库`PyJWT`生成和验证JWT，下面是安装`PyJWT`的命令。

```shell
pip install pyjwt
```

生成令牌

```shell
payload = {
    'exp': datetime.datetime.utcnow() + datetime.timedelta(days=1),
    'userid': 10001
}
token = jwt.encode(payload, settings.SECRET_KEY).decode()
```

验证令牌

```python
try:
    token = 'eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE1OTQ4NzIzOTEsInVzZXJpZCI6MTAwMDF9.FM-bNxemWLqQQBIsRVvc4gq71y42I9m2zt5nlFxNHUo'
    payload = jwt.decode(token, settings.SECRET_KEY)
except InvalidTokenError:
    raise AuthenticationFailed('无效的令牌或令牌已经过期')
```



### 中间件



### session与cookie

在创建Django项目时，默认的配置文件`settings.py`文件中已经激活了一个名为`SessionMiddleware`的中间件（关于中间件的知识我们在后面的章节做详细讲解，这里只需要知道它的存在即可），因为这个中间件的存在，我们可以直接通过请求对象的`session`属性来操作会话对象。前面我们说过，`session`属性是一个像字典一样可以读写数据的容器对象，因此我们可以使用“键值对”的方式来保留用户数据。与此同时，`SessionMiddleware`中间件还封装了对cookie的操作，在cookie中保存了sessionid，这一点我们在上面已经提到过了。

在默认情况下，Django将session的数据序列化后保存在关系型数据库中，在Django 1.6以后的版本中，默认的序列化数据的方式是JSON序列化，而在此之前一直使用Pickle序列化。JSON序列化和Pickle序列化的差别在于前者将对象序列化为字符串（字符形式），而后者将对象序列化为字节串（二进制形式），因为安全方面的原因，JSON序列化成为了目前Django框架默认序列化数据的方式，这就要求在我们保存在session中的数据必须是能够JSON序列化的，否则就会引发异常。还有一点需要说明的是，使用关系型数据库保存session中的数据在大多数时候并不是最好的选择，因为数据库可能会承受巨大的压力而成为系统性能的瓶颈，在后面的章节中我们会告诉大家如何将session保存到缓存服务中以提升系统的性能。



## Redis

通常情况下，Web应用的性能瓶颈都会出现在关系型数据库上，当并发访问量较大时，如果所有的请求都需要通过关系型数据库完成数据持久化操作，那么数据库一定会不堪重负。优化Web应用性能最为重要的一点就是使用缓存，就是把哪些数据体量不大但访问频率非常高的数据提前加载到缓存服务器中，这又是典型的空间换时间的方法。通常缓存服务器都是直接将数据置于内存中而且使用了非常高效的数据存取策略（例如：键值对方式），在读写性能上是远远优于传统的关系型数据库的，因此我们可以让Web应用接入缓存服务器来优化其性能，一个非常好的选择就是使用Redis。



## 数据库



### sqlalchemy

https://github.com/sqlalchemy/sqlalchemy



### sqlmodel

https://github.com/tiangolo/sqlmodel

### asyncpg

https://github.com/MagicStack/asyncpg



### psycopg2

https://github.com/psycopg/psycopg2



### psycopg

https://github.com/psycopg/psycopg

## 大数据

https://blog.csdn.net/wally21st/article/details/77688755?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param



## 新语言Mojo

https://github.com/modularml/mojo



## 新语言Jax

https://github.com/google/jax



## 新性能python编译器codon

https://github.com/exaloop/codon



## 瘦脸算法

自动瘦脸与眼睛放大可以算作图像局部扭曲算法的一个应用，

大概流程如下：

1.使用dlib检测出人脸关键点

2.使用Interactive Image Warping 局部平移算法实现瘦脸

```python
import dlib
import cv2
import numpy as np
import math
predictor_path='shape_predictor_68_face_landmarks.dat'
 
#使用dlib自带的frontal_face_detector作为我们的特征提取器
detector = dlib.get_frontal_face_detector()
predictor = dlib.shape_predictor(predictor_path)

def landmark_dec_dlib_fun(img_src):
    img_gray = cv2.cvtColor(img_src,cv2.COLOR_BGR2GRAY)
 
    land_marks = []
 
    rects = detector(img_gray,0)
 
    for i in range(len(rects)):
        land_marks_node = np.matrix([[p.x,p.y] for p in predictor(img_gray,rects[i]).parts()])
        # for idx,point in enumerate(land_marks_node):
        #     # 68点坐标
        #     pos = (point[0,0],point[0,1])
        #     print(idx,pos)
        #     # 利用cv2.circle给每个特征点画一个圈，共68个
        #     cv2.circle(img_src, pos, 5, color=(0, 255, 0))
        #     # 利用cv2.putText输出1-68
        #     font = cv2.FONT_HERSHEY_SIMPLEX
        #     cv2.putText(img_src, str(idx + 1), pos, font, 0.8, (0, 0, 255), 1, cv2.LINE_AA)
        land_marks.append(land_marks_node)
 
    return land_marks
'''
方法： Interactive Image Warping 局部平移算法
'''
 
 
def localTranslationWarp(srcImg,startX,startY,endX,endY,radius):
 
    ddradius = float(radius * radius)
    copyImg = np.zeros(srcImg.shape, np.uint8)
    copyImg = srcImg.copy()
 
    # 计算公式中的|m-c|^2
    ddmc = (endX - startX) * (endX - startX) + (endY - startY) * (endY - startY)
    H, W, C = srcImg.shape
    for i in range(W):
        for j in range(H):
            #计算该点是否在形变圆的范围之内
            #优化，第一步，直接判断是会在（startX,startY)的矩阵框中
            if math.fabs(i-startX)>radius and math.fabs(j-startY)>radius:
                continue
 
            distance = ( i - startX ) * ( i - startX) + ( j - startY ) * ( j - startY )
 
            if(distance < ddradius):
                #计算出（i,j）坐标的原坐标
                #计算公式中右边平方号里的部分
                ratio=(  ddradius-distance ) / ( ddradius - distance + ddmc)
                ratio = ratio * ratio
 
                #映射原位置
                UX = i - ratio  * ( endX - startX )
                UY = j - ratio  * ( endY - startY )
 
                #根据双线性插值法得到UX，UY的值
                value = BilinearInsert(srcImg,UX,UY)
                #改变当前 i ，j的值
                copyImg[j,i] =value
 
    return copyImg

#双线性插值法
def BilinearInsert(src,ux,uy):
    w,h,c = src.shape
    if c == 3:
        x1=int(ux)
        x2=x1+1
        y1=int(uy)
        y2=y1+1
 
        part1=src[y1,x1].astype(np.float)*(float(x2)-ux)*(float(y2)-uy)
        part2=src[y1,x2].astype(np.float)*(ux-float(x1))*(float(y2)-uy)
        part3=src[y2,x1].astype(np.float) * (float(x2) - ux)*(uy-float(y1))
        part4 = src[y2,x2].astype(np.float) * (ux-float(x1)) * (uy - float(y1))
 
        insertValue=part1+part2+part3+part4
        return insertValue.astype(np.int8)

def face_thin_auto(src):
 
    landmarks = landmark_dec_dlib_fun(src)
 
    #如果未检测到人脸关键点，就不进行瘦脸
    if len(landmarks) == 0:
        return
 
    for landmarks_node in landmarks:
        left_landmark= landmarks_node[3]
        left_landmark_down=landmarks_node[5]
 
        right_landmark = landmarks_node[13]
        right_landmark_down = landmarks_node[15]
 
        endPt = landmarks_node[30]
 
 
        #计算第4个点到第6个点的距离作为瘦脸距离
        r_left=math.sqrt((left_landmark[0,0]-left_landmark_down[0,0])*(left_landmark[0,0]-left_landmark_down[0,0])+
                         (left_landmark[0,1] - left_landmark_down[0,1]) * (left_landmark[0,1] - left_landmark_down[0, 1]))
 
        # 计算第14个点到第16个点的距离作为瘦脸距离
        r_right=math.sqrt((right_landmark[0,0]-right_landmark_down[0,0])*(right_landmark[0,0]-right_landmark_down[0,0])+
                         (right_landmark[0,1] -right_landmark_down[0,1]) * (right_landmark[0,1] -right_landmark_down[0, 1]))
 
        #瘦左边脸
        thin_image = localTranslationWarp(src,left_landmark[0,0],left_landmark[0,1],endPt[0,0],endPt[0,1],r_left)
        #瘦右边脸
        thin_image = localTranslationWarp(thin_image, right_landmark[0,0], right_landmark[0,1], endPt[0,0],endPt[0,1], r_right)
 
    #显示
    cv2.imshow('thin',thin_image)
    cv2.imwrite('thin.jpg',thin_image)

def main():
    src = cv2.imread('timg4.jpg')
    cv2.imshow('src', src)
    face_thin_auto(src)
    cv2.waitKey(0)
 
if __name__ == '__main__':
    main()
```



人脸老化

- 制作皱纹、斑点纹理图,并且手动标定纹理图的关键点
- 头发分割，根据年纪改变发色
- 检测人脸关键点，通过关键点对纹理图进行形变
- 纹理图与原图进行叠加、融合

https://blog.csdn.net/danffer1985/article/details/54602585?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param

