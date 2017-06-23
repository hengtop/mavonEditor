# mavonEditor

![npm](https://nodei.co/npm/mavon-editor.png?downloads=true&downloadRank=true&stars=true)

> 基于Vue的markdown编辑器

### [English Documents](./README-EN.md)

## example (图片展示)

### PC

![PC](./img/example1.png)

### 图片上传&预览

![PC](./img/images_preview_0.gif)

![PC](./img/images_preview_1.gif)

![PC](./img/images_preview_2.gif)

![PC](./img/images_preview_3.gif)

### 移动

![移动](./img/example-phone.png)
&nbsp;&nbsp;&nbsp;
![移动](./img/example-phone2.png)
## Use Setup (开始)

### Install mavon-editor (安装)

```
$ npm install mavon-editor --save
```

#### Webpack

为了优化打包引入体积，从**2.0.0**起`hightlight.js`各代码高亮文件将在使用时按需异步加载，必须要将`mavon-editor/dist/js/*.js`都引入
如果你使用`webpack`打包，你需要配置webpack如下所示
`webpack.conf.js`:
```javascript
//...
var lang = require('highlight.js-async-webpack/src/file.lang.hljs.js');
var _entry= {
    back_end: './src/back-end/index.js',
    vue: ['vue']
};
for (var i = 0; i < lang.length; i++) {
    _entry[lang[i]] = ['mavon-editor/dist/js/' + lang[i] + '.js']
}

//...
module.exports = {
    entry: _entry,
    output: {
        path: path.resolve(__dirname, './dist'),
        filename: 'js/[name].js',
        //...
    },
    //...
}
```

### Use (如何引入)

#### 方法1
`index.js`:
```javascript
    // 全局注册
    // import with ES6
    import Vue from 'vue'
    import mavonEditor from 'mavon-editor'
    import 'mavon-editor/dist/css/index.css'

    // use
    Vue.use(mavonEditor)
    new Vue({
        'el': '#main',
        data() {
            return { value: '' }
        }
    })
```
`index.html`
```html
<div id="main">
    <mavon-editor v-model="value"/>
</div>
```

#### 方法2
`index.js`:
```javascript
    // 全局注册
    // require with Webpack/Node.js
    var Vue = require('vue')
    var mavonEditor = require('mavon-editor')
    import 'mavon-editor/dist/css/index.css'

    // use
    Vue.use(mavonEditor)
    new Vue({
        'el': '#main',
        data() {
            return { value: '' }
        }
    })
```
`index.html`
```html
<div id="main">
    <mavon-editor v-model="value"/>
</div>
```

#### 方法3
`editor.vue`:
```javascript
    <template>
        <div id="editor">
            <mavon-editor style="height: 100%"></mavon-editor>
        </div>
    </template>
    <script>
    // Local Registration
    import { mavonEditor } from 'mavon-editor'
    import 'mavon-editor/dist/css/index.css'
    export default {
        name: 'editor',
        components: {
            mavonEditor
            // or 'mavon-editor': mavonEditor
        }
    }
    </script>
    <style>
    #editor {
        margin: auto;
        width: 80%;
        height: 580px;
    }
    </style>
```
`index.js`:
```javascript
    import Vue from 'vue';
    var editor = require('./editor.vue');
    new Vue({
        el: '#main',
        render: h => h(editor)
    });
```
`index.html`:
```html
<div id="main">
</div>
```

#### 方法4
`editor.vue`:
```javascript
    <template>
        <div id="editor">
            <mavon-editor style="height: 100%"></mavon-editor>
        </div>
    </template>
    <script>
    // Local Registration
    // import mavonEditor from 'mavon-editor'
    var mavonEditor = require('mavon-editor')
    import 'mavon-editor/dist/css/index.css'
    export default {
        name: 'editor',
        components: {
            'mavon-editor': mavonEditor.mavonEditor
        }
    }
    </script>
    <style>
    #editor {
        margin: auto;
        width: 80%;
        height: 580px;
    }
    </style>
```
`index.js`:
```javascript
    import Vue from 'vue';
    var editor = require('./editor.vue');
    new Vue({
        el: '#main',
        render: h => h(editor)
    });
```
`index.html`:
```html
<div id="main">
</div>
```

#### Images Upload & Preview

```javascript
<template>
    <button @click="uploadimg">upload</button>
    <mavon-editor @imgAdd="$imgAdd" @imgDel="$imgDel"></mavon-editor>
</template>
exports default {
    data(){
        return {
            img_file: {}
        }
    },
    methods: {
        $imgAdd(pos, $file){
            this.img_file[pos] = $file;
        },
        $imgDel(pos){
            delete this.img_file[pos];
        },
        uploadimg($e){
            // upload files in one request.
            console.log(this.img_file);
            var formdata = new FormData();
            for(var _img in this.img_file){
                formdata.append(_img, this.img_file[_img]);
            }
            axios({
                url: 'http://127.0.0.1/index.php',
                method: 'post',
                data: formdata,
                headers: { 'Content-Type': 'multipart/form-data' },
            }).then((res) => {
                console.log(res);
            })
        },
    }
}
```
### 注

- **默认大小样式为 min-height: 300px , ming-width: 300px 可自行覆盖**
- **基础z-index: 1500**
- **屏幕分辨率低于768px ，自动取消【单栏 | 双栏】编辑模式 ，更改为【编辑 | 预览】切换 ， 并且取消【沉浸式阅读】模式( > 768px 的眼睛图标为【阅读模式】 , 反之为【编辑 |预览】)**
- **快捷键的监听对象更改为编辑器自身，所以当编辑器失去焦点后，快捷键失效**

## API 文档

### props

| name 名称      | type 类型 | default 默认值 | describe 描述                          |
| ------------ | :-----: | :---------: | ---------------------------------------- |
| value        | String  |             | 初始值                                     |
| language     | String  |     cn      | 语言选择，暂支持 cn: 中文简体 ， en: 英文      |
| scrollStyle  | Boolean |    true     | 开启滚动条样式(暂时仅支持chrome)              |
| subfield     | Boolean |    true     | true： 单双栏编辑模式 ， false： 编辑预览模式    |
| default_open | String |    edit     | edit： 初始展示编辑区域 ， preview： 初始展示预览区域  , 其他 = edit |
| placeholder | String |    开始编辑...     |  输入框为空时默认提示文本  |
| editable     | Boolean |    true     | 是否允许编辑     |
| code_style | String |    code-github     | markdown样式： code-github , code-hybrid , code-xcode   |
| toolbarsFlag | Boolean |    true     | 工具栏是否显示                |
| toolbars     | Object  |     如下例     | 工具栏                      |

```javascript
 /*
    默认工具栏按钮全部开启, 传入自定义对象
    例如: {
         bold: true, // 粗体
         italic: true,// 斜体
         header: true,// 标题
    }
    此时, 仅仅显示此三个功能键
 */
toolbars: {
      bold: true, // 粗体
      italic: true, // 斜体
      header: true, // 标题
      underline: true, // 下划线
      strikethrough: true, // 中划线
      mark: true, // 标记
      superscript: true, // 上角标
      subscript: true, // 下角标
      quote: true, // 引用
      ol: true, // 有序列表
      ul: true, // 无序列表
      link: true, // 链接
      imagelink: true, // 图片链接
      code: true, // code
      table: true, // 表格
      subfield: true, // 是否需要分栏
      fullscreen: true, // 全屏编辑
      readmodel: true, // 沉浸式阅读
      htmlcode: true, // 展示html源码
      help: true, // 帮助
      /* 1.3.5 */
      undo: true, // 上一步
      redo: true, // 下一步
      trash: true, // 清空
      save: true, // 保存（触发events中的save事件）
      /* 1.4.2 */
      navigation: true // 导航目录
  }
```


### events

| name 方法名         |            params 参数            | describe 描述                              |
| ---------------- | :-----------------------------: | ---------------------------------------- |
| change           |  String: value , String: reder  | 编辑区发生变化的回调事件(render: value 经过markdown解析后的结果) |
| save             |  String: value , String: reder  | ctrl + s 的回调事件(保存按键,同样触发该回调)             |
| fullscreen       | Boolean: status , String: value | 切换全屏编辑的回调事件(boolean: 全屏开启状态)             |
| readmodel        | Boolean: status , String: value | 切换沉浸式阅读的回调事件(boolean: 阅读开启状态)            |
| htmlcode         | Boolean: status , String: value | 查看html源码的回调事件(boolean: 源码开启状态)           |
| subfieldtoggle   | Boolean: status , String: value | 切换单双栏编辑的回调事件(boolean: 双栏开启状态)            |
| helptoggle       | Boolean: status , String: value | 查看帮助的回调事件(boolean: 帮助开启状态)               |
| navigationtoggle | Boolean: status , String: value | 切换导航目录的回调事件(boolean: 导航开启状态)             |
| imgAdd           | String: filename, File: imgfile | 图片文件添加回调事件(filename: 写在md中的文件名, File: File Object) |
| imgDel           |        String: filename         | 图片文件删除回调事件(filename: 写在md中的文件名)          |

## Dependencies (依赖)

- [markdown-it](https://github.com/markdown-it/markdown-it)

- [auto-textarea](https://github.com/hinesboy/auto-textarea)

- [stylus](https://github.com/stylus/stylus)

## Follow-up (后续)

- ~~撤销键、清空键、保存按钮~~（1.3.5）
- ~~重构~~（1.4.0）
- ~~支持开启标题导航~~(1.4.2)
- ~~支持图片上传~~(1.7.0)
- 滚动条样式的浏览器兼容性


## update(更新内容)
- **2.0.0** highlight.js语言高亮文件异步调用渲染(17.6.20 / [CHENXCHEN](https://github.com/CHENXCHEN))
- **1.7.3** 更改事件监听方式，修正粗体样式(17.6.15 / [CHENXCHEN](https://github.com/CHENXCHEN))
- **1.7.0** 取消单栏编辑模式实时渲染(17.6.14 / [CHENXCHEN](https://github.com/CHENXCHEN))
    - 添加图片预览(粘贴板图片复制粘贴本地预览、图片拖拽本地预览、手动选择图片本地预览)
    - 添加图片文件添加删除事件
    - 删除to-markdown部分
- **1.6.3** 新增Markdown样式选择 props：code_style(17.6.9 / [yyyybzzzz PR](https://github.com/yyyybzzzz))
- **1.6.1** 新增props：enabled编辑开关、toolbarsFlag工具栏是否显示(17.5.26)
    - 修复subfield = true初始化时候不显示内容
- **1.5.6** 支持语言切换 ， 新增英文文档(17.5.11)
- **1.5.3** 拓展markdown渲染规则——KaTeX$公式 ， 修改help文档(17.5.6 / [CHENXCHEN](https://github.com/CHENXCHEN))
- **1.5.2** 优化项目结构(17.5.6 / [CHENXCHEN](https://github.com/CHENXCHEN))
    - 将toolbar抽离为两个单独vue文件，事件提取为toolbar_left.js和toolbar_right.js
    - 抽离stylus样式为mavon-editor.styl
    - 调整md.css位置至lib/css
- **1.5.1** 添加postcss插件，压缩插件体积，分离markdown样式(样式需单独引入，参考上述用法)(17.5.6 / [CHENXCHEN](https://github.com/CHENXCHEN))
- **1.4.8** 优化项目结构,添加webpack-dev-server的开发测试(17.5.4 / [CHENXCHEN](https://github.com/CHENXCHEN)）
- **1.4.7** 图标局部引入,减少文件体积(17.4.26）
- **1.4.2** 支持开启标题导航 ,快捷键监听对象更改为document（17.4.25）
- **1.4.0** 重构代码（17.4.24）
- **1.3.5** 增加撤销键、清空键、保存按钮 , 修复底部展示不完整BUG
- **1.3.4** 多个编辑器快捷键/组合键监听覆盖 , props.toolbars 传递规则纠正(传入值整体覆盖默认值)
- **1.3.3** 多个编辑器z-index冲突
- **1.3.2** props 传递方法 更改为 v-on 绑定方法

## Collaborators(合作者)

- [CHENXCHEN](https://github.com/CHENXCHEN)


## Licence (证书)

mavonEditor is open source and released under the MIT Licence.

Copyright (c) 2017 hinesboy
