<template>
  <div class="detail_container">
    <div class="img_box">
      <img :src="img" alt="" class="big_img">
    </div>
    <div class="book_message">
      <div class="book_message_left">
        <p class="book_title">{{book_name}}</p>
        <p class="book_money">￥{{price}}</p>
      </div>
      <div class="book_message_right">
        <p class="book_description">{{message}}</p>
      </div>
    </div>
    <div class="bank"></div>
    <div class="contain">
      <scroll- scroll-x class="top">
        <div class="tabbar" :class="{'tabbar-bottom':currentTab==index}"
        v-for="(item,index) in tabBar" :key="index" @click="clickTab(index)"
        >{{item.title}}</div>
      </scroll->
      <swiper :current="currentTab" @change="changeTab">
        <swiper-item>
          <div></div>
        </swiper-item>
        <swiper-item>
          <div>
            <p class="book_title" style="text-align:left;">价格：￥{{price}}</p>
            <p class="book_title" style="text-align:left;">开本：16</p>
          </div>
        </swiper-item>
        <swiper-item>
          <div></div>
        </swiper-item>
      </swiper>
    </div>
    <div class="cart_footer">
      <div class="min_box" @click="changeIndex">
        <span class="footer_icon iconfont iconshouye"></span>
        <span class="footer_title">首页</span>
      </div>
      <div class="min_box" @click="cartPage">
        <span class="footer_icon iconfont icongouwuche"></span>
        <span class="footer_title">购物车</span>
        <span v-if="showCartNum" class="cart_number">{{cart_num}}</span>
      </div>
      <div class="add_cart" @click="addCart(false)">
        加入购物车
      </div>
      <div class="buy" @click="pageSell">
        立即购买
      </div>
    </div>
    <toast :message="cartmessage" :visible.sync="visible"></toast>
  </div>
</template>
<script>
import toast from "mpvue-toast";

export default {
  components: {
    toast
  },
  data() {
    return {
      tabBar: [
        { title: "图文详情" },
        { title: "商品参数" },
        { title: "商品评论" }
      ],
      currentTab: 0,
      img: "",
      book_name: "",
      message: "", //物品简介
      cartmessage: "", //购物车弹窗信息
      price: "",
      cart_num: 0,
      showCartNum: false,
      _id: "", //books集合商品唯一id
      openid: "", //用户唯一id
      datas: "",
      visible: false, //弹框控制
      isLogin: false ,//判断用户是否已经登录
      timer: null //定时器
    };
  },
  onLoad: function(options) {
    let _that = this;
    //获取用户openid
    wx.cloud.callFunction({
      name: "user",complete: res => { _that.openid = res.result.openid;}
    });
    if (_that.openid) {_that.isLogin = true;} else {_that.isLogin = false;}
    const db = wx.cloud.database({ env: "xcxu-b70d87" });
    db.collection("books").get({success(res) { _that.datas = res.data;}});
    if (options.img) {
      _that.img = options.img;
      _that.book_name = options.book_name;
      _that.message = options.message;
      _that.price = options.price;
      _that._id = options._id;
    }
  },
  methods: {
    clickTab(e) {
      this.currentTab = e;
    },
    changeTab(e) {
      this.currentTab = e.mp.detail.current;
    },
    changeIndex() {
      wx.switchTab({
        url: "../index/main"
      });
    },
    cartPage() {
      wx.switchTab({
        url: "../shopcar/main"
      });
    },
    addCart() {
      let _that = this;
      _that.visible = !_that.visible;
      //判断用户是否登录，是否可以接着操作
        _that.cartmessage = "加入购物车成功";
        const db = wx.cloud.database({ env: "xcxu-b70d87" });
        const _ = db.command;
        //先判断集合是否有这个商品
        db
          .collection("usercart")
          .where({
            _openid: _that.openid,
            cartid: _that._id
          })
          .get({
            success(res) {
              console.log(res.data);
              if (res.data.length === 0) {
                //如果没有就新增
                // console.log("----")
                db.collection("usercart").add({
                  data: {
                    num: 1,
                    cartid: _that._id,
                    img: _that.img,
                    price: _that.price,
                    book_name: _that.book_name,
                    ischeck: true
                  }
                });
              } else {
                //有就直接更新数据
                let _id = res.data[0]._id;
                db
                  .collection("usercart")
                  .doc(_id)
                  .update({
                    data: {
                      num: _.inc(1)
                    }
                  });
              }
            }
          });
    },
    pageSell() {
      // console.log(this.openid);
      let _that = this;
      // console.log(_that._id);
        const settlement_url = "../settlement/main?id=" + _that._id;
        wx.navigateTo({ url: settlement_url });
    }
  }
};
</script>

<style scoped>
@import "../../../static/iconfont/iconfont.css";

.img_box {
  width: 100%;
  height: 480rpx;
  text-align: center;
  border-bottom: 1rpx solid rgb(207, 207, 207);
}
.big_img {
  width:380rpx;
  height: 360rpx;
  margin-top: 50rpx;
}
.book_message {
  height: 200rpx;
}
.book_message_right {
  height: 100%;
}
.book_title {
  font-size: 30rpx;
  padding-top: 50rpx;
  padding-left: 40rpx;
  overflow: hidden;
  -webkit-line-clamp: 1;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-box-orient: vertical;
}
.book_money {
  font-size: 32rpx;
  color: orange;
  padding-top: 30rpx;
  padding-left: 40rpx;
}
.bank {
  width: 100%;
  height: 20rpx;
  background: rgb(250, 249, 249);
}
.contain {
  text-align: center;
}
.top {
  width: 100%;
  text-align: center;
  line-height: 42px;
  white-space: nowrap;
  position: relative;
  background: #fff;
  /* border-bottom: 1rpx solid rgb(207, 207, 207); */
}
.tabbar {
  width: 25%;
  font-size: 28rpx;
  height: 42px;
  display: inline-block;
  margin-right: 25rpx;
  color: rgb(0, 0, 0);
}

.tabbar-bottom {
  color: orange;
  border-bottom: 2px solid orange;
}
.cart_footer {
  width: 100%;
  height: 100rpx;

  position: fixed;
  bottom: 0;
  left: 0;
}
.min_box {
  position: relative;
  float: left;
  width: 100rpx;
  height: 100%;
  text-align: center;
}
.footer_title {
  font-size: 24rpx;
  display: block;
}
.footer_icon {
  font-size: 42rpx;
}
.add_cart,
.buy {
  float: left;
  text-align: center;
  line-height: 100rpx;
  font-size: 32rpx;
  width: 275rpx;
  height: 100rpx;
  color: #fff;
}
.add_cart {
  background: orange;
}
.buy {
  background: red;
}
.cart_number {
  position: absolute;
  top: 0;
  right: 0;
  display: inline-block;
  width: 30rpx;
  height: 30rpx;
  text-align: center;
  line-height: 20rpx;
  border-radius: 50%;
  background: red;
  color: #fff;
  font-size: 28rpx;
}
.book_description {
  font-size: 30rpx;
  overflow: hidden;
  -webkit-line-clamp: 3;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  color: #575656;
  padding-left: 40rpx;
  padding-top: 50rpx;
}
.book_message_left {
  width: 200rpx;
  float: left;
}
</style>
