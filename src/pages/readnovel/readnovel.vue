<template>
    <div>
        <web-view :src="book_url"></web-view>
    </div>
</template>

<script
  type="text/javascript"
  src="https://res.wx.qq.com/open/js/jweixin-1.3.2.js"
></script>
<script>
export default {
  data() {
    return {
      book_url: ""
    };
  },
  onLoad: function(options) {
    this.book_url =
      "http://t.shuqi.com/route.php?pagename=#!/bid/" +
      options.book_id +
      "/ct/read";
    //  this.book_url = ""
      console.log(this.book_url)
  }
};
</script>

