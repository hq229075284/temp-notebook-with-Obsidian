# chrome中的测试结果
## case1
```javascript
const audio=document.querySelector('audio')
audio.play()
```

描述：无交互，直接代码调用audio.play()

结果：
```ad-bug
Uncaught (in promise) DOMException: play() failed because the user didn't interact with the document first. [https://goo.gl/xX8pDD](https://goo.gl/xX8pDD)
```  

## case2
```javascript
const audio=document.querySelector('audio')
setTimeout(()=>{
  audio.play()
},2000)
```

描述：无交互，在异步任务中调用audio.play()

结果：
```ad-bug
Uncaught (in promise) DOMException: play() failed because the user didn't interact with the document first.  [https://goo.gl/xX8pDD](https://goo.gl/xX8pDD)
```  

## case 3
```javascript
const audio=document.querySelector('audio')
setTimeout(()=>{
  audio.play()
},2000)
```

描述：页面初始化后，在2秒内有交互，在2秒后调用audio.play()

结果：
```ad-success
能播放音频
```

## 结论
JavaScript调用音频接口前，用户需至少与网页交互一次，否则浏览器会报异常

# 附录

### 不同浏览器对该行为的限制也不相同，以下是几种浏览器的测试结果


#### 可自动发出声音的
+ 火狐浏览器（国产系统）
+ 奇安信可信浏览器（国产系统）
+ 统信浏览器（国产系统）
+ 360安全浏览器

#### 需要交互后，能自动发出声音的
+ 微软edge浏览器
+ 联想浏览器
+ 谷歌浏览器
+ safari浏览器

#### 不能自动发出声音的
+ IE浏览器