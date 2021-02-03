<!--
 * @Author: your name
 * @Date: 2020-10-12 11:34:04
 * @LastEditTime: 2020-10-12 11:45:06
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \VueCLib\src\views\Cmt.vue
-->
<template>
  <div class="cmt">
    <Comment
      :info="info"
      :submitCmtLoading="submitCmtLoading"
      :limtText="limtText"
      :iconWidth="iconWidth"
      :iconHeight="iconHeight"
      :cmt_show="cmt_show"
      :iconList="imgList"
      @changeText="changeText"
      @submitError="submitError"
      @submitSuccess="submitSuccess"
    />
  </div>
</template>

<script>
import Comment from "../components/Comment.vue";
export default {
  name: "Cmt",
  components: { Comment },
  data: function() {
    return {
      info: {
        id: 1
      },
      submitCmtLoading: false,
      limtText: 400,
      iconWidth: 24,
      iconHeight: 24,
      cmt_show: true,
      iconList: ["https://gold-cdn.xitu.io/asset/twemoji/2.6.0/svg/1f603.svg"],
      imgList: [
        {
          icon: "//www1.pconline.com.cn/20200929/pgc/cmt/icon.png", // tab的icon
          width: 30, // tab icon的宽度
          height: 30, // tab icon的高度
          list: ["//gold-cdn.xitu.io/asset/twemoji/2.6.0/svg/1f603.svg"]
        },
        {
          icon: "//www1.pconline.com.cn/20200929/pgc/cmt/icon.png", // tab的icon
          width: 30, // tab icon的宽度
          height: 30, // tab icon的高度
          list: ["//gold-cdn.xitu.io/asset/twemoji/2.6.0/svg/1f603.svg"]
        }
      ]
    };
  },
  methods: {
    changeText(text) {
      console.log("改变", text);
    },
    submitError(error) {
      console.log("error", error);
    },
    submitSuccess(val, info) {
      console.log("val", val, info);
    }
  }
};
</script>
