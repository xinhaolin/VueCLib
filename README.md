# Vue组件仓库

## 评论组件
### 传递参数：
 1. submitCmtLoading：Boolean 是否正在提交
 2. limtText：Number 限制字符长度  0为不限制
 3. iconWidth：Number 表情包宽度
 4. iconHeight：Number 表情包高度
 5. cmt_show： Boolean 是否显示
 6. iconList： Array 表情包列表【必传】
 7. placeholder： String 提示语
 8. info: Object 信息 方便数据传递
### 方法：
 1. changeText：文本变化时触发 参数：text 文本内容
 2. submitError: 提交验证不通过时触发  参数：error 错误信息 （只验证评论为空与评论超出字数） 一般不会触发，因为已经做了文本非空提交与超出切割
 3. submitSuccess： 提交验证通过时触发 参数： text 文本 info 信息
