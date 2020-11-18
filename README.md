## 前言
夹娃娃营销活动大家应该都不陌生那如何用小程序实现呢？

今天就带大家用canvas撸了一个全手工绘制的夹娃娃分享给大家。此处应有掌声 : )

### 一、效果图

![](https://blog.100boot.cn/storage/thumbnails/_signature/2MN5CRHT0NNDA9QRAFIBS6G7HS.jpg)

### 二、项目结构
![](https://blog.100boot.cn/storage/thumbnails/_signature/H0CDF7BJVBPIJH7GUJDC34EQF.jpg)

### 三、运行流程
* 获取所有奖品列表
* 依次绘制背景、爪子、展台、顶部海报
* 点击任意区域出发开始夹娃娃
* 请求后端获取得到的奖品，也可以为空（没有夹到）
* 展示夹到的奖品

### 四、关键代码

点击屏幕开始
模拟后台返回数据
```javascript
// 点击屏幕开始
if (!isStart && clip) {
    isStart = true;
    var prizes = this.data.prizes;
    // 模拟后台返回数据
    wx.showLoading({
        title: '请求中',
    });
    setTimeout(function () {
        wx.hideLoading();
        // 随机一个奖品
        var rIndex = utils.getRandom(prizes.length * 2);
        var prize = rIndex > prizes.length - 1 ? null : prizes[rIndex];

        // 启动夹子
        clip.open(prize);
    }, 1000);
}
```

当爪子动画完成回到顶部后回调函数
```javascript
// 当爪子动画完成回到顶部后回调函数
clip.onMoveUp = function () {
    isStart = false;
    // 获取夹到的物品, 可能为空
    var prize = clip.getPrize();
    // 显示夹到的物品, 可能为空
    if (prize) {
        wx.showToast({
            image: prize.img,
            title: "获得'" + prize.text + "'",
            duration: 3000,
            mask: true,
        })
    }else{
        wx.showToast({
            icon: "none",
            title: "哎呀! 什么也没有抓到。",
            duration: 3000,
            mask: true,
        })
    }
};
```

计算流逝的时间和绘制每个模块
```javascript
// 更新动画
var currentTime = new Date().getTime();
// 上一次的时间
var lastTime = currentTime;
var update = function () {
    // 当前时间
    currentTime = new Date().getTime();

    // 计算流逝的时间
    var time = currentTime - lastTime;

    // 绘制背景
    context.drawImage(bgImg, 0, 0, w, h);

    // 绘制爪子
    clip.draw(context);
    clip.update(time);

    // 绘制展台
    booth.draw(context);
    booth.update(time);

    // 绘制顶部海报
    context.drawImage(headerImg, 0, 0, w, headerH);

    // 绘图画布
    context.draw();

    // 记录上次的时间
    lastTime = currentTime;
};
```

爪子的绘制
```javascript
Clip.prototype.draw = function (context) {

    // 线条
    context.save();
    context.drawImage(lineImg, this.line.left, 0, this.line.w, this.line.h);
    context.restore();

    var theta = Math.PI * this.angle / 180;
    // left clip
    context.save();
    context.translate(this.lb.right, this.lb.top); // 以左边爪子的右上为圆心旋转
    context.rotate(theta);
    context.drawImage(clipLImg, -this.lb.w, 0);
    context.restore();

    // right clip
    context.save();
    context.translate(this.rb.left, this.rb.top); // 以右边爪子的左上为圆心旋转
    context.rotate(-theta);
    context.drawImage(clipRImg, 0, 0);
    context.restore();

    context.save();
    context.drawImage(screwImg, this.screw.left, this.screw.top);
    context.restore();

    // 显示被抓的娃娃
    if (this.showPrize && this.prize && this.prize.img) {
        context.save();
        context.drawImage(this.prize.img, this.lb.left, this.lb.y, this.prize.w, this.prize.h);
        context.restore();
    }
};
```

### 五、完整代码 「听说，打赏我的人最后都找到了真爱。」
![](https://blog.100boot.cn/storage/thumbnails/_signature/1PCPKDVVBV6VS4F00P6AGJN4N9.jpg)
![](https://blog.100boot.cn/storage/thumbnails/_signature/3QIRHL9BHG6MTTMFK614DEMN7V.jpg)

### 六、关注我们

![](https://blog.100boot.cn/storage/thumbnails/_signature/FG39BR6DEBTHUA1SK6PI85EE1.jpg)

> IT实战联盟是集产品、UI设计、前后端、架构、大数据和AI人工智能等为一体的实战交流服务平台！联盟嘉宾都为各互联网公司项目的核心成员，联盟主旨是“让实战更简单”，欢迎来撩~~~

> 我们的网站: [http://blog.100boot.cn](http://blog.100boot.cn)
