# GPS-invalid-point-processing

iOS提供了获取位置信息的类，`CLLocationManager`。
通过它，我们可以轻松得到包含位置信息的对象`CLLocation`。

但正常情况下在运动中，会不断得到`CLLocation`，二期中很多点都是无效的。
如果不去掉，将导致里程，速度，配速等一系列数据误差非常大。

这里我针对常见的误差情况，用最简单明了的方法，教你判断无效的GPS点。

一、静止不动
上一个GPS点的瞬时速度为零，当前点的瞬时速度也为0,那么当前点无效.
瞬时速度就是`CLLocation`对象的属性`speed`。

二、GPS信号太差
信号是`CLLocation`的属性`horizontalAccuracy`，表示水平方向的误差值。
当这个值大于70，我们认为信号太差。

三、无效移动
前后两个GPS点的距离，小于`horizontalAccuracy` * `(0.5~0.66)`，我们认为这个点是无效移动。

四、平均速度过大
如果当前点到上一个点的距离 / 当前点到上一个点的时间
速度大于某一个值，则认为当前点无效
比如跑步，博尔特最快是`10.44m/s`

五、采样频率
如果不想GPS点过于密集，可以用时间限制。
如果当前点的时间到上一个点事件小于2s，则认为点无效。
另外也可以用位置管理器的`distanceFilter`属性来设置。
当`distanceFilter = 30`的时候，只有超过30m的移动，才会进入到位置更新的回调方法。
这样做的好处是设备可以更加省电，CPU的占用更低，根据实际情况而定。
