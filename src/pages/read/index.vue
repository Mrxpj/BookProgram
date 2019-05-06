<template>
  <div class="read_container">
    <div class="search_box">
      <span class="search_icon iconfont iconicon_search"
       @click="searchBook"></span>
      <input class="bookkey" type="text" placeholder="请输入小说名称" 
      v-model="bookkey" confirm-type="search" @change="changeBook">
    </div>
    <div v-if="isShow" class="search_book__message" @click="novelPage(index)"
     v-for="(item,index) in book_message" :key="index">
      <div class="search_book__img">
        <img :src="item.cover" alt="" class="book_img">
      </div>
      <div class="search_book__container">
        <p class="search_book__title">{{item.title}}</p>
        <p class="search_book__description">{{item.desc}}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      bookkey: "", //搜索框关键字
      book_id: "", //小说id
      isShow: false,
      book_message: [] //小说简介
    };
  },
  onLoad: function() {},
  methods: {
    searchBook() {
      if (this.bookkey) {
        var _that = this;
        wx.request({
          url: "https://www.apiopen.top/novelInfoApi?name=" + this.bookkey,
          method: "GET",
          data: {},
          header: {
            header: {
              "content-type": "application/json"
            }
          },
          success: function(res) {
            _that.book_message = res.data.data.data;
          }
        });
        _that.isShow = true;
      }
    },
    novelPage(index) {
      this.book_id = this.book_message[index].bid;
      console.log(this.book_id);
      wx.navigateTo({
        url: "../readnovel/main?book_id=" + this.book_id
      });
    }
  }
};
</script>

<style scoped>
@import "../../../static/iconfont/iconfont.css";

.search_box {
  width: 700rpx;
  height: 70rpx;
  line-height: 70rpx;
  border-radius: 20rpx;
  background: rgb(235, 232, 232);
  margin-left: 25rpx;
  margin-top: 20rpx;
}
.search_icon {
  float: left;
  padding-left: 20rpx;
  padding-right: 20rpx;
  padding-top: 5rpx;
  font-size: 42rpx;
}
.bookkey {
  font-size: 28rpx;
  height: 70rpx;
  line-height: 70rpx;
}
.search_book__message {
  width: 100%;
  height: 240rpx;
  margin-top: 20rpx;
}
.search_book__img {
  float: left;
  width: 250rpx;
  height: 200rpx;
  text-align: center;
  line-height: 200rpx;
  font-size: 0; /* //解决图片与div顶部不对齐 */
}
.book_img {
  height: 200rpx;
  width: 140rpx;
  vertical-align: middle;
}
.search_book__container {
  height: 100%;
}
.search_book__title {
  font-size: 32rpx;
}
.search_book__description {
  font-size: 28rpx;
  padding-top: 20rpx;
  overflow: hidden;
  -webkit-line-clamp: 3;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  color: #828282;
}
</style>
