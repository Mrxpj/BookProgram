<template>
  <div class="me_container">
    <div class="login_box">
      <div>
        <img class="login_img" 
        src="cloud://xcxu-b70d87.7863-xcxu-b70d87/images/images/background.jpg"
         alt="">
      </div>
      <img class="login_photo" v-if="isShow" :src="avatarUrl" alt="">
      <span v-else class="login_photo"></span>
      <p v-if="isShow" class="username">{{userInfo.nickName}}</p>
      <button v-else class="login_font" open-type="getUserInfo" 
      @getuserinfo="getUserInfo" @click="handleGetUserInfo">登 录</button>
    </div>
    <div class="bank">
    </div>
    <div class="me_list">
      <ul class="me_list__ul" v-for="(item,index) in ullist" :key="index">
        <li class="me_list__li" @click="myClick(item.clickname)">
          <span class="my_order">{{item.name}}</span>
          <span class="jiantou">></span>
        </li>
      </ul>
    </div>
    <div class="bank" v-if="isShow">
    </div>
    <div class="exit" v-if="isShow">
      <button class="exit_login" @click="outUserInfo">退出登录</button>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      ullist: [
        {
          name: "我的订单",
          clickname: "../order/main"
        },
        {
          name: "我的收货地址",
          clickname: "../address/main"
        },
        {
          name: "我的优惠卷",
          clickname: "../coupon/main"
        },
        {
          name: "联系客服",
          clickname: "../service/main"
        }
      ],
      userInfo: {},
      avatarUrl: "",
      //用户未授权
      isShow: false
    };
  },
  beforeMount() {
    this.handleGetUserInfo();
  },
  // onload() {
  //     this.handleGetUserInfo();
  // },
  methods: {
    //获取用户登录信息
    handleGetUserInfo() {
      wx.getUserInfo({success: data => {
          this.userInfo = data.userInfo;this.isShow = true;
          this.avatarUrl = data.userInfo.avatarUrl;},
        fail: () => { }});//   console.log("获取失败");
    },
    getUserInfo(data) {//判断用户是否授权
      if (data.mp.detail.rawData) {this.handleGetUserInfo();}}, //用户授权
    outUserInfo() { this.isShow = false;this.userInfo = {};},
    myClick(str) {wx.navigateTo({ url: str });}
  }
};
</script>

<style scoped>
ul {
  list-style: none;
}
.login_img {
  display: block;
  height: 400rpx;
  width: 100%;
}
.login_box {
  position: relative;
}
.login_font {
  position: absolute;
  display: inline-block;
  height: 70rpx;
  width: 180rpx;
  text-align: center;
  line-height: 70rpx;
  border-radius: 30rpx;
  background: crimson;
  color: #fff;
  font-size: 32rpx;
  bottom: 40rpx;
  left: 50%;
  margin-left: -90rpx;
}
.username {
  position: absolute;
  width: 300rpx;
  text-align: center;
  font-size: 32rpx;
  bottom: 40rpx;
  left: 50%;
  margin-left: -150rpx;
}
.login_photo {
  position: absolute;
  width: 150rpx;
  height: 150rpx;
  background: dimgrey;
  top: 110rpx;
  left: 50%;
  margin-left: -75rpx;
  border-radius: 50%;
  /* background-image: url(/static/images/bag.png) */
}
.me_list__ul {
  margin-left: 25rpx;
}
.me_list__li {
  height: 90rpx;
  border-bottom: 1rpx solid #ccc;
  line-height: 90rpx;
  width: 700rpx;
}
.my_order {
  font-size: 28rpx;
}
.jiantou {
  float: right;
  color: rgb(168, 167, 167);
  font-size: 40rpx;
}
.bank {
  width: 100%;
  height: 20rpx;
  background: rgb(250, 249, 249);
}
.exit {
  height: 90rpx;
  width: 100%;
  text-align: center;
  line-height: 90rpx;
}
.exit_login {
  font-size: 28rpx;
  color: rgb(248, 26, 26);
}
</style>