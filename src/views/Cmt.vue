<template>
  <div class="cmt">
    <Comment
      :info="info"
      :submitCmtLoading="submitCmtLoading"
      :limtText="limtText"
      :iconWidth="iconWidth"
      :iconHeight="iconHeight"
      :cmt_show="cmt_show"
      :iconList="iconList"
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
      iconList: [
        "https://gold-cdn.xitu.io/asset/twemoji/2.6.0/svg/1f603.svg",
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
