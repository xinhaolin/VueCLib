# Vue组件仓库(新)
### 以前写的组件代码仓库不见，重新建一个吧，希望能坚持下去。

## 评论组件
<img src="https://img-blog.csdnimg.cn/20201010095608925.jpg" width = "300" height = "200" alt="图片名称" align=center />

### 传递参数：
 1. submitCmtLoading：Boolean 是否正在提交
 2. limtText：Number 限制字符长度  0为不限制
 3. iconWidth：Number 表情包宽度
 4. iconHeight：Number 表情包高度
 5. cmt_show： Boolean 是否显示
 6. iconList： Array 表情包列表【必传】，格式看下方
 7. placeholder： String 提示语
 8. info: Object 信息 方便数据传递
 9. filterPaste： Boolean 是否开启对复制内容进行过滤（仅保留文本）
### 方法：
 1. changeText：文本变化时触发 参数：text 文本内容
 2. submitError: 提交验证不通过时触发  参数：error 错误信息 （只验证评论为空与评论超出字数） 一般不会触发，因为已经做了文本非空提交与超出切割
 3. submitSuccess： 提交验证通过时触发 参数： text 文本 info 信息
### 表情列表格式：
  ```javascript
   const iconList = [
     {
     icon: '//www1.pconline.com.cn/20200929/pgc/cmt/icon.png', // tab的icon
      width: 30, // tab icon的宽度
      height: 30, // tab icon的高度
      list: [
        "//gold-cdn.xitu.io/asset/twemoji/2.6.0/svg/1f603.svg"
      ]
      }
   ]
  ```
### 组件版本：
 v1.0: 只支持单套表情包，且暂无对复制进文本框的内容进行过滤操作，需要在changText回调里进行自主过滤<br/>
 v1.1：支持多套表情,且开启文本过滤操作，会对复制进去的文本进行标签过滤
