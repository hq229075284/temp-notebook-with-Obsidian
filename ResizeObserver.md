# ResizeObserver

元素被resizeObserver监听后，
+ 当该元素display显隐时，会执行监听回调
+ 当该元素从DOM树中移除时，会执行监听回调
+ 当该元素重新添加到DOM树中时，会执行监听回调

> 测试代码

```html
<!DOCTYPE html>

<html lang="en">

  <head>

    <meta charset="UTF-8" />

    <meta http-equiv="X-UA-Compatible" content="IE=edge" />

    <meta name="viewport" content="width=device-width, initial-scale=1.0" />

    <title>Document</title>

  </head>

  

  <body>

    <button class="remove">remove</button>

    <button class="reset">reset</button>

    <button class="toggle">toggle</button>

    <div class="target" style="height: 600px; border: 1px solid"></div>

    <script>

      const target = document.querySelector(".target");

  

      const ob = new ResizeObserver(function (entries) {

        entries.forEach((entry) => {

          const { blockSize: height, inlineSize: width } =

            entry.borderBoxSize[0];

          console.log(`height:${height}, width:${width}`);

        });

      });

      ob.observe(target);

  

      const removeBtn = document.querySelector("button.remove");

      const resetBtn = document.querySelector("button.reset");

      removeBtn.addEventListener("click", () => {

        target.remove();

      });

      resetBtn.addEventListener("click", () => {

        document.body.appendChild(target);

      });

  

      const toggleBtn = document.querySelector("button.toggle");

      toggleBtn.addEventListener("click", () => {

        const { display } = target.style;

        if (display) {

          target.style.setProperty("display", "");

        } else {

          target.style.setProperty("display", "none");

        }

      });

    </script>

  </body>

</html>
```