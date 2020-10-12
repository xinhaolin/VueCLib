<template>
  <div v-if="cmt_show" class="cmt_box">
    <div
      ref="cmt_input"
      class="content_edit"
      contenteditable="true"
      :placeholder="placeholder"
      @focus="changeHandle"
      @input="changeText"
      @blur="blurInput"
      v-html="content"
    ></div>
    <div class="cmt_handle">
      <img
        id="emoticon_icon"
        v-if="iconList.length"
        src="//www1.pconline.com.cn/20200929/pgc/cmt/icon.png"
        @click.stop="showIconListPlane"
      />
      <div
        @click="submitCmt()"
        :class="['btn_submit', isSubmit ? 'btn_submit_y' : '']"
      >
        发布
      </div>
      <div v-show="iconShow" class="icon_list">
        <ul class="icon_box">
          <li
            class="icon_item"
            v-for="(item, index) in iconList"
            :key="`icon${index}`"
            @click="insertIcon(item)"
          >
            <img :src="item" />
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Comment",
  props: {
    submitCmtLoading: {
      type: Boolean,
      default: false
    },
    limtText: {
      type: Number,
      default: 0
    },
    iconWidth: {
      type: Number,
      dafault: 24
    },
    iconHeight: {
      type: Number,
      dafault: 24
    },
    cmt_show: {
      type: Boolean,
      default: true
    },
    iconList: {
      type: Array,
      required: true
    },
    placeholder: {
      type: String,
      default: "积极回复可吸引更多人评论"
    },
    info: {
      type: Object
    }
  },
  data: function() {
    return {
      content: "",
      widFouce: "",
      rangeFouce: "",
      iconShow: false,
      isSubmit: false
    };
  },
  watch: {
    cmt_show: {
      handler(value) {
        if (value) {
          this.$nextTick(() => {
            console.log("this.$refs.cmt_input", this.$refs.cmt_input);
            this.toLast(this.$refs.cmt_input);
          });
        }
      },
      immediate: true
    }
  },
  mounted() {
    const self = this;
    document.getElementById("app").addEventListener("click", self.setHideClick);
    self.$once("hook:beforeDestroy", () => {
      document.getElementById("app") &&
        document
          .getElementById("app")
          .removeEventListener("click", self.setHideClick);
    });
  },
  methods: {
    setHideClick(event) {
      let target = event.srcElement;
      let nameList = ["icon_item", "icon_list", "icon_box"];
      if (
        !nameList.includes(target.className) &&
        !nameList.includes(target.parentElement.className)
      ) {
        this.iconShow = false;
      }
    },
    changeHandle() {
      console.log("blur");
    },

    changeText(e) {
      //  表情包插入时不触发该方法，只有输入时会触发
      // 判断字数，要先把自定义表情改成占位符，一个自定义表情按俩2个字符算
      let text = e.srcElement.innerHTML;
      let emitText = text;
      if (this.limtText) {
        let textObj = this.paseText(text);
        let len = this.getCharLen(textObj.text);
        if (len > this.limtText) {
          let str = this.setCmtTextByLimit(textObj.text, textObj.key);
          emitText = textObj.isReplace
            ? this.reductionStr(text, str, textObj.key)
            : str;
          e.srcElement.innerHTML = emitText;
          this.toLast(e.srcElement);
        }
      }

      this.isSubmit = emitText.length ? true : false;
      this.$emit("changeText", emitText);
    },
    paseText(str) {
      // 解析内容，把图片全部用占位替换掉  跟换行相关（div，br）全部改成两个空格
      let str1 = str.replace(/<div>|<\/div>|<br>/gi, "  ");
      // eslint-disable-next-line no-useless-escape
      let imgReg = /<img[^>]*src[=\'\"\s]+([^\"\']*)[\"\']?[^>]*>/gi;
      let imgMatch = str1.match(imgReg);
      let key = this.getRandomFour(str1);
      let isReplace = false;

      if (imgMatch) {
        isReplace = true;
        for (let i = 0; i < imgMatch.length; i++) {
          str1 = str1.replace(imgMatch[i], key);
        }
      }
      return {
        isReplace,
        key,
        text: str1
      };
    },
    /**
     * @description: 复原评论内容
     */
    reductionStr(sourceText, text, key) {
      // 解析内容，把图片全部用占位替换掉
      // eslint-disable-next-line no-useless-escape
      let imgReg = /<img[^>]*src[=\'\"\s]+([^\"\']*)[\"\']?[^>]*>/gi;
      let imgMatch = sourceText.match(imgReg);
      let result = text;
      if (imgMatch) {
        for (let i = 0; i < imgMatch.length; i++) {
          result = result.replace(key, imgMatch[i]);
        }
      }
      return result;
    },
    getRandomFour(str) {
      // 在时间戳里取随机4位数作为key，且如果文本中包含key则递归
      let timeStr = new Date().getTime() + "";
      let result = "";
      for (let i = 0; i < 4; i++) {
        let nums = Math.floor(Math.random() * 13);
        result = result + timeStr[nums];
      }
      return str.indexOf(result) == -1 ? result : this.getRandomFour(str);
    },
    getCharLen(sSource) {
      // 空格&nbsp; 要当1个字符算，所以最后要给每个空格减去5
      // 获取字符长度
      var l = 0;
      var schar;
      for (var i = 0; (schar = sSource.charAt(i)); i++) {
        // eslint-disable-next-line no-control-regex
        l += schar.match(/[^\x00-\xff]/) != null ? 2 : 1;
      }
      let nbsp = sSource.match(/&nbsp;/gi);
      if (nbsp) {
        let len = nbsp.length;
        l = l - len * 5;
      }

      return l;
    },
    setCmtTextByLimit(sSource, key) {
      let self = this;
      //  文字切割 如果超出文字限制需要切割
      if (typeof sSource !== "string") return;
      var str = changeLast(sSource, key);
      if (this.getCharLen(str) <= this.limtText) return str;
      while (this.getCharLen(str) > this.limtText) {
        str =
          4 + str.lastIndexOf(key) == str.length
            ? str.substring(0, str.length - 4)
            : str.substring(0, str.length - 1);
      }
      function changeLast(strl, key) {
        // 一直切割到最后一个不为表情包或不超出限制为止
        while (
          4 + strl.lastIndexOf(key) == strl.length &&
          self.getCharLen(strl) > self.limtText
        ) {
          strl = sSource.substring(0, sSource.length - 4);
        }
        return strl;
      }
      return str;
    },
    showIconListPlane() {
      // 显示表情包列表
      if (!this.iconShow) {
        this.getFouceInput();
      }
      this.iconShow = !this.iconShow;
      //  iconShow
    },
    getFouceInput() {
      this.widFouce = window.getSelection();
      this.rangeFouce = this.widFouce.getRangeAt(0);
    },
    toLast(obj) {
      // 将光标移到最后
      if (window.getSelection) {
        let range = window.getSelection();
        range.selectAllChildren(obj);
        range.collapseToEnd();
      }
    },
    insertIcon(url) {
      // 判断是否超出字数，如果超出不给插入
      const self = this;
      this.isSubmit || (this.isSubmit = true);
      let length = this.getCharLen(
        this.paseText(this.$refs.cmt_input.innerHTML).text
      );
      if (this.limtText && length + 4 > this.limtText) return;

      const img = `<img src='${url}' width=${this.iconWidth} height=${this.iconHeight} />`;
      //  兼容性判断 如果不兼容不往下执行，虽然说不兼容IE9以下，但是还是做一下判断 方便后面灵活控制
      if (window.getSelection && window.getSelection().getRangeAt) {
        let winSn = this.widFouce,
          range = this.rangeFouce;
        //  要判断的光标状态
        if (
          winSn.focusNode.className !== "content_edit" &&
          winSn.focusNode.parentElement.className !== "content_edit"
        ) {
          winSn.selectAllChildren(self.$refs.cmt_input);
          winSn.collapseToEnd();
          range = winSn.getRangeAt(0);
        }
        range.collapse(false);
        let node;
        if (range.createContextualFragment) {
          // 兼容IE9跟safari
          node = range.createContextualFragment(img);
        } else {
          let tempDom = document.createElement("div");
          tempDom.innerHTML = img;
          node = tempDom;
        }
        let dom = node.firstChild;
        range.insertNode(dom);
        let clRang = range.cloneRange();
        clRang.setStartAfter(dom);
        winSn.removeAllRanges();
        winSn.addRange(clRang);

        self.$emit("changeText", self.$refs.cmt_input.innerHTML);
      } else {
        console.log("不兼容~");
      }
    },
    blurInput() {
      this.getFouceInput();
    },
    submitCmt() {
      // 提交评论
      const self = this;
      let text = this.$refs.cmt_input.innerHTML;
      let length = this.getCharLen(this.paseText(text).text);
      if (self.submitCmtLoading || !self.isSubmit) return;
      if (self.cmt_text == "") {
        self.$emit("submitError", "评论为空");
        return;
      } else if (this.limtText && length > this.limtText) {
        self.$emit("submitError", "评论超出字数");
        return;
      }
      self.$emit("submitSuccess", text, self.info);
    }
  }
};
</script>
<style lang="scss" scoped>
.cmt_box {
  width: 510px;
  height: 180px;
  background-color: #ffffff;
  border-radius: 2px;
  border: solid 1px #ececec;
  padding: 14px;
  box-sizing: border-box;
  margin: auto;
  .content_edit {
    width: 100%;
    height: 120px;
    outline: none;
    border: none;
    text-align: left;
    font-size: 14px;

    &:empty::before {
      color: #cccccc;
      content: attr(placeholder);
    }
  }
  .cmt_handle {
    display: flex;
    justify-content: space-between;
    align-items: center;
    position: relative;
    #emoticon_icon {
      width: 21px;
      height: 21px;
      display: block;
      flex-shrink: 0;
      cursor: pointer;
    }
    .btn_submit {
      width: 68px;
      height: 32px;
      background-color: #cccccc;
      border-radius: 16px;
      text-align: center;
      line-height: 32px;
      color: #ffffff;
      font-size: 14px;
      cursor: pointer;

      &.btn_submit_y {
        background-color: #f95354;
      }
    }
    .icon_list {
      position: absolute;
      top: 40px;
      left: -10px;
      width: 280px;
      border-radius: 2px;
      background: #fff;
      box-shadow: 0 5px 18px 0 rgba(0, 0, 0, 0.16);
      padding: 15px;
      &::before {
        content: "";
        width: 0;
        height: 0;
        display: block;
        padding: 0;
        position: absolute;
        top: -10px;
        left: 10px;
        border-left: 10px solid transparent;
        border-right: 10px solid transparent;
        border-bottom: 10px solid #fff;
      }
      .icon_box {
        list-style: none;
        display: flex;
        justify-content: start;
        align-items: center;
        flex-wrap: wrap;
        padding: 0;
        margin: 0;

        .icon_item {
          padding: 5px;
          text-align: center;
          cursor: pointer;
          > img {
            width: 30px;
            height: 30px;
          }
        }
      }
    }
  }
}
</style>
