## 问题表格
| 问题                                           | 测试结果 |
| ---------------------------------------------- | -------- |
| 什么时候全屏变换结束（包括进入全屏和退出全屏）                           |   window上的resize监听触发时       |
| 是否会触发window的resize事件                   | 触发     |
| 是否会触发这个将要全屏的元素上的resizeObserver | 触发     |
| 执行requestFullscreen时，是否同步                                               |    异步，执行requestFullscreen后添加的fullscreenchange监听事件也会触发      |

## 全屏流程图
元素requestFullscreen后，可能发生的情况：

```mermaid
flowchart TB
Start --> A(requestFullsreen返回的promise resolve后)
A --情况1--> B1(fullscreenchange触发)
B1 --> C("resizeObserver触发(元素大小变化到视口的大小，视口大小包括视口的滚动条)")
C --> D1(触发window上的resize监听)
D1 --> E("resizeObserver触发(元素大小变化到屏幕的大小)")

A --情况2--> D2(触发window上的resize监听)
D2 --> B2(fullscreenchange触发)
B2 --> E

E --> 全屏
```

## 测试代码
```html
<!DOCTYPE html>

<html lang="en">

<head>

    <meta charset="UTF-8">

    <meta http-equiv="X-UA-Compatible" content="IE=edge">

    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>Document</title>

    <style>

        html,body{margin:0;}

        .container{

            width: 80%;

            /* width: 800px; */

            height: 600px;

            background: red;

        }

    </style>

</head>

<body>

    <div class="container">

        <button class="toggle">click</button>

    </div>

    <script>

        const container=document.querySelector('.container')

        window.addEventListener('resize',()=>console.log('window.resize, size:',container.clientWidth,'*',container.clientHeight))

  

        const ob=new ResizeObserver((entries)=>{

            console.log('resize observer')

            entries.forEach(entry=>{

                console.log('contentBoxSize, size',entry.contentBoxSize[0].inlineSize,'*',entry.contentBoxSize[0].blockSize)

            })

        })

        ob.observe(container)

  
  

        let isFullscreen=false

        const button=document.querySelector('button')

  

        window.addEventListener('fullscreenchange',()=>{

            console.log('fullscreenchange, size:',container.clientWidth,'*',container.clientHeight)

        })

        button.addEventListener('click',()=>{

            isFullscreen=!isFullscreen

            if(isFullscreen){

                container.requestFullscreen().then(()=>{

                    console.log('requestFullscreen resolve, size:',container.clientWidth,'*',container.clientHeight)

                })

                console.log('after click button, size:',container.clientWidth,'*',container.clientHeight)

                // window.addEventListener('fullscreenchange',()=>{

                //     console.log('new fullscreenchange')

                // })

            }else{

                document.exitFullscreen()

            }

        })

    </script>

</body>

</html>
```