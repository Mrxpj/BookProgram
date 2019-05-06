<template>
  <div class="address_container">
    <div class="order_box" v-if="orderList">
      <p class="order_title">订单信息</p>
      <div class="order_book" v-for="(item,index) in orderList" :key="index">
        <div class="order_bookleft">
          <img :src="item.img" alt="">
        </div>
        <div class="order_bookright">
          <p class="book_title">{{item.book_name}}</p>
          <p class="book_money">
            <span class="money_num">￥{{item.price}}</span>
            <!-- <span class="shopcar_icon iconfont icongouwuche" @click='setVisible(false)'></span> -->
          </p>
          <p class="total"><sapn class="order_time">{{item.time}}</sapn><sapn>合计：￥{{item.num*item.price}}</sapn></p>
        </div>
        <span class="book_num">x{{item.num}}</span>
      </div>
    </div>
    <div v-else>
      <img class="address_img" src="/static/images/order.png" alt="">
      <div class="address_message">
        <p class="address_title">还没有订单呢~</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      orderList: []
    };
  },
  onShow() {
    let _that = this;
    const db = wx.cloud.database({ env: "xcxu-b70d87" });
    wx.cloud.callFunction({
      name: "user",
      complete: res => {
        _that.openid = res.result.openid;
        // console.log(_that.openid);
        // console.log(res);
      }
    });
    //获取订单列表
    db
      .collection("order")
      .where({
        _openid: _that.openid,
      })
      .get({
        success(res) {
          _that.orderList = res.data;
          // console.log(_that.addressList);
        }
      });
  }
};
</script>

<style scoped>
.address_container {
  text-align: center;
}
.address_img {
  width: 150rpx;
  height: 150rpx;
  margin-top: 350rpx;
}
.address_message {
  margin-top: 50rpx;
}
.address_title {
  text-align: center;
  font-size: 28rpx;
}
.remind {
  text-align: center;
  font-size: 24rpx;
  padding-top: 20rpx;
  color: #999;
}
.order_book {
  position: relative;
  width: 100%;
  height: 200rpx;
  margin-top: 30rpx;
  clear: both;
}
.order_title {
  height: 60rpx;
  line-height: 60rpx;
  padding-left: 40rpx;
  border-bottom: 1rpx solid #eee;
  font-size: 28rpx;
}
.order_bookleft {
  float: left;
}
.order_bookleft img {
  height: 200rpx;
  width: 220rpx;
}
.book_title {
  font-size: 32rpx;
  padding-top: 20rpx;
  margin-bottom: 20rpx;
  overflow: hidden;
  -webkit-line-clamp: 1;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  text-align: left;
}

.book_money {
  padding-top: 30rpx;
  text-align: left;
  padding-bottom: 20rpx;
}
.money_num {
  color: red;
}
.book_num {
  position: absolute;
  font-size: 28rpx;
  right: 40rpx;
  top: 50%;
}
.total {
  font-size: 28rpx;
  /* text-align: right; */
  /* padding-left: 500rpx; */
}
.order_time {
  padding-right: 100rpx;
}
</style>