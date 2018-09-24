# 快速入门Web阅读器开发

> 作者：sam

## 课程地址

本课程为[慕课网](https://www.imooc.com)的免费课，可以点击[这里](https://www.imooc.com/learn/1038)进行学习

## 课程介绍

通过本课程，大家可以了解电子书阅读器的基本工作原理及ePub格式电子书的解析原理，我会手把手带大家用Vue.js来实现一个手机阅读器，实现电子书的阅读功能，以及一些辅助功能，如翻页、字号设置、主题设置、阅读进度调整和电子书目录。本课程浅显易懂，配合实际应用案例，适合初学者和有一定前端知识的同学进行学习。

## 课程主要知识点

- ePub电子书解析和渲染
```javascript
// 生成Book对象
this.book = new Epub(DOWNLOAD_URL)
// 通过Book.renderTo生成Rendition对象
this.rendition = this.book.renderTo('read', {
  width: window.innerWidth,
  height: window.innerHeight,
  method: 'default'
})
// 通过Rendtion.display渲染电子书
this.rendition.display()
```

- ePub电子书翻页
```javascript
// 上一页
function prevPage() {
  if (this.rendition) {
    this.rendition.prev()
  }
}
// 下一页
function nextPage() {
  if (this.rendition) {
    this.rendition.next()
  }
}
```

- ePub电子书的字号设置和场景切换
```javascript
// 设置主题
function setTheme(index) {
  this.themes.select(this.themeList[index].name)
  this.defaultTheme = index
}
// 注册主题
function registerTheme() {
  this.themeList.forEach(theme => {
    this.themes.register(theme.name, theme.style)
  })
}
// 设置字号大小
function setFontSize(fontSize) {
  this.defaultFontSize = fontSize
  if (this.themes) {
    this.themes.fontSize(fontSize + 'px')
  }
}
```

- ePub电子书生成目录和定位信息
```javascript
// Book对象的钩子函数ready
this.book.ready.then(() => {
  // 生成目录
  this.navigation = this.book.navigation
  // 生成Locations对象
  return this.book.locations.generate()
}).then(result => {
  // 保存locations对象
  this.locations = this.book.locations
  // 标记电子书为解析完毕状态
  this.bookAvailable = true
})
```

- ePub电子书通过百分比进行定位
```javascript
function onProgressChange(progress) {
  const percentage = progress / 100
  const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
  this.rendition.display(location)
}
```

- HTML5 range控件
```html
<input class="progress" 
       type="range"
       max="100"
       min="0"
       step="1"
       @change="onProgressChange($event.target.value)" 
       @input="onProgressInput($event.target.value)"
       :value="progress"
       :disabled="!bookAvailable"
       ref="progress">
```
