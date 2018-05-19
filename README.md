# css_performance_demo
##下面介绍重排重绘造成的性能开销和如何进行优化
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>css3-transform转换成maxtrix</title>
    <style>
        .container{
            position: relative;
            min-height: 400px;
        }
        .box{
            position: absolute;
            top: 0;
            left: 0;
            width: 200px;
            height: 200px;
            border-radius: 50%;
            background-color: orange;
        }

        .animation-run{
            animation: move 4s infinite;
        }

        @keyframes move {
            0%{
                top: 0;
                left: 0;
            }
            25%{
                top: 0;
                left: 200px;
            }
            50%{
                top: 200px;
                left: 200px;
            }
            75%{
                top: 200px;
                left: 0;
            }
        }     
    </style>
</head>
<body>
    <div class="container">
        <div class="box animation-run"></div>
    </div>
</body>
</html>
这个是没有开启3d的动画，下面进行录制20s看看主要消耗的时间是哪一部分，主要开销的时间是刚刚开始rendering的时间和造成重排的时间
```
![image.png](https://upload-images.jianshu.io/upload_images/6264932-a9d5a2dd69d6984f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>css3-transform转换成maxtrix</title>
    <style>
        .container{
            position: relative;
            min-height: 400px;
        }
        .box{
            position: absolute;
            top: 0;
            left: 0;
            width: 200px;
            height: 200px;
            border-radius: 50%;
            background-color: orange;
        }

        .animation-run{
            animation: move 4s infinite;
        }

        /* transform不会发生重排重绘 */
        @keyframes move {
            0%{
                transform: translate(0,0);
            }
            25%{
                transform: translate(200px,0);
            }
            50%{
                transform: translate(200px,200px);
            }
            75%{
                transform: translate(0,200px);
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="box animation-run"></div>
    </div>
</body>
</html>
这个例子是使用transform来做动画，开启了硬件加速，不会造成重排重绘，很明显下图没有了paint消耗的时间
```
![image.png](https://upload-images.jianshu.io/upload_images/6264932-711f4b564e8e431b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>css3-transform转换成maxtrix</title>
    <style>
        .container{
            position: relative;
            min-height: 400px;
        }
        .box{
            position: absolute;
            top: 0;
            left: 0;
            width: 200px;
            height: 200px;
            border-radius: 50%;
            background-color: orange;
        }

        .animation-run{
            animation: move 4s infinite;
        }

        /* 用矩阵加快性能速度 */
        @keyframes move {
            0%{
                transform: matrix(0)
            }
            25%{
                transform: matrix(1, 0, 0, 1, 200, 0)
            }
            50%{
                transform: matrix(1, 0, 0, 1, 200, 200);
            }
            75%{
                transform: matrix(1, 0, 0, 1, 0, 200);
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="box animation-run"></div>
    </div>
</body>
</html>
最终优化版本，里面采用了transform做动画，开启了硬件加速，如果把translate转换成矩阵，进行了深度的优化
```
![image.png](https://upload-images.jianshu.io/upload_images/6264932-5407388b883a5b01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
