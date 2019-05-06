<template>
  <div class="address_container">
    <div v-if="!cartList.length">
      <img class="address_img" src="/static/images/shopcar.png" alt="">
      <div class="address_message">
        <p class="address_title">购物车空空的，快去选书吧~</p>
        <!-- <p v-else class="address_title">你还未登录，赶快去登录吧~</p> -->
      </div>
    </div>
    <div v-else class="cart_message">
      <div class="cart_box" v-for="(item,index) in cartList" :key="index">
        <div class="choicebox">
          <span class="choice" :class="{active:item.ischeck}" @click="choiceColor(item._id,item.ischeck)"></span>
        </div>
        <div class="cart_img_div">
          <img :src="item.img" alt="" class="cart_img">
        </div>
        <div class="cart_book_title">
          <p class="book_title_p">
            <span class="book_title_span">{{item.book_name}}</span>
          </p>
          <p class="book_sum">
            <span class="cart_jian" @click="discreaseNum(item._id)">-</span>
            <span class="cart_num">{{item.num}}</span>
            <span class="cart_jia" @click="increaseNum(item._id)">+</span>
          </p>
          <span class="remove iconfont icondel" @click="remove(item._id,index)"></span>
          <span class="cart_money">￥{{item.price}}</span>
        </div>
      </div>
      <div class="cart_footer">
        <div class="footer_left">
          <span class="allchoice" :class="{active:isAllActive}" @click="changeAllColor"></span>
          <span class="all_font">全选</span>
          <span class="sum_font">总计：</span>
          <span class="all_money">{{allprice}}</span>
        </div>
        <div class="footer_right" @click="pageSell">
          结算
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isActive: false,
      isAllActive: false,
      _id: "",
      openid: "",
      cartList: [], //获取购物车列表,
      allprice: 0, //购物车总价
      arrId: [],
    };
  },
  onload() {
    let _that = this;
    //获取用户ID
    wx.cloud.callFunction({
      name: "user",
      complete: res => {
        _that.openid = res.result.openid;
        // console.log(_that.openid);
        // console.log(res);
      }
    });
    //获取购物车列表
    db
      .collection("usercart")
      .where({
        _openid: _that.openid
      })
      .get({
        success(res) {
          _that.cartList = res.data;
          _that.allprice = 0; //每进来一次清空一次
          for (let i in res.data) {
            _that.arrId[i] = res.data[i]._id;
            if (res.data[i].ischeck) {
              _that.allprice += res.data[i].price * res.data[i].num; //计算购物车总价格
            }
          }
          // console.log(_that.arrId)
        }
      });
    //获取用户id
    // wx.cloud.callFunction({
    //   name: "user",
    //   complete: res => {
    //     _that.openid = res.result.openid;
    //     // console.log(_that.openid);
    //     // console.log(res);
    //   }
    // });
    // console.log(_that.openid)
  },
  onShow() {
    let _that = this;
    const db = wx.cloud.database({ env: "xcxu-b70d87" });
    //获取用户id
    wx.cloud.callFunction({
      name: "user",
      complete: res => {
        _that.openid = res.result.openid;
        // console.log(_that.openid);
        // console.log(res);
      }
    });
    //获取购物车列表
    db
      .collection("usercart")
      .where({
        _openid: _that.openid
      })
      .get({
        success(res) {
          _that.cartList = res.data;
          _that.allprice = 0; //每进来一次清空一次
          for (let i in res.data) {
            _that.arrId[i] = res.data[i]._id;
            if (res.data[i].ischeck) {
              _that.allprice += res.data[i].price * res.data[i].num; //计算购物车总价格
            }
          }
          // console.log(_that.arrId)
        }
      });
  },
  methods: {
    choiceColor(_id, ischeck) {
      // console.log("----");
      let _that = this;
      const db = wx.cloud.database({ env: "xcxu-b70d87" });
      db
        .collection("usercart")
        .doc(_id)
        .update({
          data: {
            ischeck: ischeck ? false : true
          }
        });

      //获取用户id
      // wx.cloud.callFunction({
      //   name: "user",
      //   complete: res => {
      //     _that.openid = res.result.openid;
      //     // console.log(_that.openid);
      //     // console.log(res);
      //   }
      // });
      //获取购物车列表
      db
        .collection("usercart")
        .where({
          _openid: _that.openid
        })
        .get({
          success(res) {
            _that.cartList = res.data;
            // console.log(res);
          }
        });
    },
    changeAllColor() {
      let _that = this;
      const db = wx.cloud.database({ env: "xcxu-b70d87" });
      this.isAllActive = this.isAllActive ? false : true;
      //获取用户id
      // wx.cloud.callFunction({
      //   name: "user",
      //   complete: res => {
      //     _that.openid = res.result.openid;
      //     // console.log(_that.openid);
      //     // console.log(res);
      //   }
      // });
      if (_that.isAllActive === true) {
        db
          .collection("usercart")
          .where({
            _openid: _that.openid
          })
          .get({
            success(res) {
              _that.cartList = res.data;
              // console.log(res);
              for (let i in res.data) {
                db
                  .collection("usercart")
                  .doc(res.data[i]._id)
                  .update({
                    data: {
                      ischeck: true
                    }
                  });
              }
              // console.log(_that.allprice);
            }
          });
        //获取购物车列表
        db
          .collection("usercart")
          .where({
            _openid: _that.openid
          })
          .get({
            success(res) {
              _that.cartList = res.data;
              // console.log(res);
            }
          });
      } else {
        db
          .collection("usercart")
          .where({
            _openid: _that.openid
          })
          .get({
            success(res) {
              _that.cartList = res.data;
              // console.log(res);
              for (let i in res.data) {
                db
                  .collection("usercart")
                  .doc(res.data[i]._id)
                  .update({
                    data: {
                      ischeck: false
                    }
                  });
              }
              // console.log(_that.allprice);
            }
          });
        //获取购物车列表
        db
          .collection("usercart")
          .where({
            _openid: _that.openid
          })
          .get({
            success(res) {
              _that.cartList = res.data;
              // console.log(res);
            }
          });
      }
    },
    discreaseNum(_id) {
      let _that = this;
      const db = wx.cloud.database({ env: "xcxu-b70d87" });
      const _ = db.command;
      db
        .collection("usercart")
        .doc(_id)
        .update({
          data: {
            num: _.inc(-1)
          }
        });

      db
        .collection("usercart")
        .where({
          _openid: _that.openid
        })
        .get({
          success(res) {
            _that.cartList = res.data;
            // console.log(res);
          }
        });
    },
    increaseNum(_id) {
      let _that = this;
      const db = wx.cloud.database({ env: "xcxu-b70d87" });
      const _ = db.command;
      db
        .collection("usercart")
        .doc(_id)
        .update({
          data: {
            num: _.inc(1)
          }
        });

      db
        .collection("usercart")
        .where({
          _openid: _that.openid
        })
        .get({
          success(res) {
            _that.cartList = res.data;
            // console.log(res);
          }
        });
    },
    remove(_id, index) {
      let _that = this;
      const db = wx.cloud.database({ env: "xcxu-b70d87" });
      const _ = db.command;
      db
        .collection("usercart")
        .doc(_id)
        .remove();
      _that.cartList.splice(index, 1);
    },
    pageSell() {
      const settlement_url = "../settlement/main";
      wx.navigateTo({ url: settlement_url });
    }
  }
};
</script>

<style scoped>
@import "../../../static/iconfont/iconfont.css";

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
.cart_box {
  width: 100%;
  height: 200rpx;
  border-bottom: 1rpx solid #eee;
}
.choicebox {
  float: left;
  height: 200rpx;
  width: 100rpx;
}
.choice {
  display: block;
  width: 40rpx;
  height: 40rpx;
  border-radius: 50%;
  border: solid 1rpx #ccc;
  /* margin-left: 10rpx; */
  margin-top: 80rpx;
  margin-left: 30rpx;
}
.active,
.activeAll {
  background: orange;
}
.cart_book_title {
  width: 500rpx;
  position: relative;
  float: left;
}
.cart_img_div {
  float: left;
  text-align: center;
}
.cart_img {
  height: 140rpx;
  width: 140rpx;
  margin-top: 30rpx;
}
.book_title_p {
  width: 420rpx;
  font-size: 28rpx;
  padding-top: 30rpx;
  text-align: left;
  overflow: hidden;
  -webkit-line-clamp: 1;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-box-orient: vertical;
}
.book_title_span {
  padding-left: 50rpx;
}
.book_sum {
  padding-top: 50rpx;
  padding-right: 240rpx;
}
.cart_jian,
.cart_num,
.cart_jia {
  display: inline-block;
  height: 50rpx;
  width: 50rpx;
  text-align: center;
  line-height: 50rpx;
  border: 1rpx solid #ccc;
  font-size: 28rpx;
}
.remove {
  position: absolute;
  top: 35rpx;
  right: 20rpx;
}
.cart_money {
  display: block;
  position: absolute;
  top: 130rpx;
  right: 30rpx;
  color: orange;
  font-size: 28rpx;
}
.cart_footer {
  position: fixed;
  height: 100rpx;
  width: 100%;
  bottom: 0;
  left: 0;
}
.all_font {
  display: block;
  font-size: 28rpx;
  color: rgb(167, 166, 166);
  float: left;
}
.allchoice {
  display: block;
  width: 40rpx;
  height: 40rpx;
  border-radius: 50%;
  border: solid 1rpx #ccc;
  float: left;
  margin: 25rpx 20rpx 0 20rpx;
  /* margin-left: 10rpx; */
}
.sum_font {
  display: block;
  float: left;
  font-size: 28rpx;
  padding-left: 160rpx;
  color: rgb(167, 166, 166);
}
.all_money {
  display: block;
  float: left;
  color: orange;
  font-size: 28rpx;
}
.footer_left {
  float: left;
  width: 500rpx;
  height: 100rpx;
  line-height: 100rpx;
}
.footer_right {
  float: right;
  width: 240rpx;
  height: 100rpx;
  background: orange;
  text-align: center;
  line-height: 100rpx;
  color: #fff;
}
</style>
