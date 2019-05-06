<template>
  <div class="settlement_container">
    <div v-if="addressList.length">
      <div class="settlement_address" v-for="(item,index) in addressList" :key="index" @click="addressPage">
        <p class="person">
          <span class="person_name">{{item.username}}</span>
          <span class="person_phone">{{item.phone}}</span>
          <span class="person_email">{{item.email}}</span>
        </p>
        <p class="detail_address">{{item.region + item.detail_address}}</p>
      </div>
    </div>
    <div v-else @click="addressPage">
      <p class="noaddress">
        <span>还未选择地址，去新增吧~</span>
        <span class="jiantou">></span>
      </p>
    </div>
    <div class="order_box">
      <p class="order_title">订单信息</p>
      <div class="order_book" v-for="(item,index) in sellList" :key="index">
        <div class="order_bookleft">
          <img :src="item.img" alt="">
        </div>
        <div class="order_bookright">
          <p class="book_title">{{item.book_name}}</p>
          <p class="book_money">
            <span class="money_num">￥{{item.price}}</span>
            <!-- <span class="shopcar_icon iconfont icongouwuche" @click='setVisible(false)'></span> -->
          </p>
        </div>
        <span class="book_num">x{{item.num ? item.num : 1}}</span>
      </div>
    </div>
    <div class="order_footer">
      <div class="footer_left">
        <span class="footer_font">付款：</span>
        <span class="footer_money">￥{{sellMoney}}</span>
      </div>
      <div class="footer_right" @click="pay">
        微信支付
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      sellList: [],
      addressList: [],
      sellMoney: 0,
      _id: "",
      book_name: "",
      message: "",
      price: "",
      img: "",
      num:"",
      openid:""
    };
  },
  onLoad: function(options) {
    //获取用户id
    let _that = this;
    const db = wx.cloud.database({ env: "xcxu-b70d87" });
    // wx.cloud.callFunction({
    //   name: "user",
    //   complete: res => {
    //     _that.openid = res.result.openid;
    //     // console.log(_that.openid);
    //     // console.log(res);
    //   }
    // });
    // //获取地址列表
    // db
    //   .collection("useraddress")
    //   .where({
    //     _openid: _that.openid,
    //     ischeck: true
    //   })
    //   .get({
    //     success(res) {
    //       _that.addressList = res.data;
    //       console.log(_that.addressList);
    //     }
    //   });
    //获取购物车列表
    if (options.id) {
      _that._id = options.id;
      db
        .collection("typebook")
        .where({
          _id: options.id
        })
        .get({
          success(res) {
            _that.sellMoney = 0; //每进来一次清空一次
            _that.sellList = res.data;
            console.log(res);
            // for (let i in res.data) {
            //   _that.sellMoney += res.data[i].price * res.data[i].num; //计算购物车总价格
            // }
            // console.log(res);
            _that.sellMoney = res.data[0].price;
          }
        });
    } else {
      db
        .collection("usercart")
        .where({
          _openid: _that.openid,
          ischeck: true
        })
        .get({
          success(res) {
            _that.sellMoney = 0; //每进来一次清空一次
            _that.sellList = res.data;
            for (let i in res.data) {
              _that.sellMoney += res.data[i].price * res.data[i].num; //计算购物车总价格
            }
            // console.log(res);
          }
        });
    }
  },
  onShow: function() {
    //获取用户id
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
    //获取地址列表
    db
      .collection("useraddress")
      .where({
        _openid: _that.openid,
        ischeck: true
      })
      .get({
        success(res) {
          _that.addressList = res.data;
          // console.log(_that.addressList);
        }
      });
  },
  methods: {
    addressPage() {
      const address_url = "../address/main";
      wx.navigateTo({ url: address_url });
    },
    pay() {
      var time = new Date();
      var year = time.getFullYear();
      var month = time.getMonth();
      month = month > 10 ? month: "0" + month
      var day = time.getDay();
      day = day > 10 ? day: "0" + day
      var hours = time.getHours();
      var minutes = time.getMinutes();
      var timeStr = "" + year + "-" + month + "-" + day + " " + hours + ":" + minutes;
      wx.showToast({
        title: "加载中...",
        mask: true,
        icon: "loading"
      });
      let _that = this;
      const db = wx.cloud.database({ env: "xcxu-b70d87" });
      if (_that._id) {
        //立即购买过来的加入order数据集合
        db
          .collection("typebook")
          .where({
            _id: _that._id
          })
          .get({
            success(res) {
              console.log(res);
              _that.book_name = res.data[0].book_name;
              _that.message = res.data[0].message;
              _that.img = res.data[0].img;
              _that.price = res.data[0].price;
              db.collection("order").add({
                data: {
                  book_name: _that.book_name,
                  message: _that.message,
                  img: _that.img,
                  price: _that.price,
                  num: 1,
                  time: timeStr
                }
              });
            }
          });
      } else {
        console.log("----");
        //购物车过来的
        db
          .collection("usercart")
          .where({
            _openid: _that.openid,
            ischeck: true
          })
          .get({
            success(res) {
              console.log(res);
              for (var i in res.data) {
                db.collection("order").add({
                  data: {
                    book_name: res.data[i].book_name,
                    message: res.data[i].message,
                    img: res.data[i].img,
                    price: res.data[i].price,
                    num: res.data[i].num,
                    time: timeStr
                  }
                });
                db.collection('usercart').doc(res.data[i]._id).remove({
                  
                })
              }
            }
          });
      }

      setTimeout(function() {
        wx.showToast({
          title: "支付成功",
          mask: true,
          icon: "success"
        });
      }, 1000);
      setTimeout(function() {
        const order_url = "../order/main";
        wx.navigateTo({ url: order_url });
      }, 2000);
    }
  }
};
</script>

<style scoped>
@import "../../../static/iconfont/iconfont.css";

.book_rank {
  height: 250rpx;
  margin-top: 20rpx;
}
.bookrank_left {
  float: left;
}
.bookrank_left img {
  height: 200rpx;
  width: 220rpx;
}
.bookrank_right {
  margin-left: 20rpx;
}
.book_title {
  font-size: 32rpx;
  padding-top: 40rpx;
  margin-bottom: 20rpx;
  overflow: hidden;
  -webkit-line-clamp: 1;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  text-align: left;
}

.book_money {
  padding-top: 40rpx;
  text-align: left;
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
.settlement_address {
  background: rgb(245, 243, 243);
  height: 140rpx;
}
.person {
  padding-top: 20rpx;
  padding-bottom: 15rpx;
  padding-left: 40rpx;
}
.person_name,
.person_phone,
.person_email {
  font-size: 28rpx;
  margin-right: 40rpx;
}
.noaddress {
  width: 100%;
  height: 100rpx;
  font-size: 32rpx;
  background: rgb(248, 248, 248);
  text-align: center;
  line-height: 100rpx;
}
.jiantou {
  display: inline-block;
  margin-left: 250rpx;
  font-size: 42rpx;
  color: #999;
}
.detail_address {
  padding-left: 40rpx;
  font-size: 26rpx;
  color: #999;
}
.order_title {
  height: 60rpx;
  line-height: 60rpx;
  padding-left: 40rpx;
  border-bottom: 1rpx solid #eee;
  font-size: 28rpx;
}
.order_book {
  position: relative;
  width: 100%;
  height: 200rpx;
  clear: both;
}
.order_bookleft {
  float: left;
}
.order_bookleft img {
  height: 200rpx;
  width: 220rpx;
}

.order_footer {
  position: fixed;
  height: 80rpx;
  line-height: 80rpx;
  width: 100%;
  bottom: 0;
  right: 0;
  border-top: 1rpx solid #eee;
}
.footer_left {
  float: left;
  background: #fff;
  width: 500rpx;
  height: 80rpx;
  padding-left: 40rpx;
}
.footer_right {
  height: 80rpx;
  text-align: center;
  color: #fff;
  font-size: 28rpx;
  background: rgb(5, 212, 5);
}
.footer_font,
.footer_money {
  font-size: 28rpx;
}
.footer_money {
  color: red;
}
</style>
