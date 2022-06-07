#### case1
font-family "Microsoft YaHei"
font-size 16px
line-height 23px
font-height 20.89px
ex 8.639px
实际容器height: 24.958px

基线和中线间的偏移量为ex的22.6647%，计算：(24.958 - 23)/8.639

#### case2
font-family "Microsoft YaHei"
font-size 24px
line-height 30px
ex: 12.958px
实际容器height: 32.965px

基线和中线间的偏移量为ex的22.8816%，计算：(32.965 - 30)/12.958


inline行内元素的字体在检查时显示的高度由font-size和line-height:normal决定
```html
<div style="border: 1px solid"><span style="line-height: normal;font-size: 20px;">fxgEnglish</span></div>
```
