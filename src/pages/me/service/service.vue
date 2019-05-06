<template>
  <div class="service_container">
    <div class="service_message" v-for="(item,index) in serviceList" :key="index">
      <div class="service_left">
        <img class="service_img" :src="item.imgurl" alt="">
      </div>
      <div class="service_right">
        <p class="title">{{item.title}}</p>
        <p class="number">{{item.photo}}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      serviceList: [
        {
          imgurl: "/static/images/qq.png",
          title: "客服QQ",
          photo: '请添加"1057961401"'
        },
        {
          imgurl: "/static/images/photo.png",
          title: "客服电话",
          photo: "13660094354"
        }
      ]
    };
  }
};
</script>

<style scoped>
.service_img {
  display: block;
  width: 100rpx;
  height: 100rpx;
  margin-top: 15rpx;
  margin-left: 20rpx;
}
.service_left {
  float: left;
  width: 140rpx;
  height: 140rpx;
  text-align: center;
  line-height: 140rpx;
}
.service_right {
  height: 140rpx;
}
.title {
  font-size: 30rpx;
  color: #6d6d6d;
  padding-left: 15rpx;
  padding-top: 20rpx;
}
.number {
  font-size: 30rpx;
  color: black;
  padding-left: 15rpx;
  padding-top: 10rpx;
}
</style>