<template>
  <div class="address_container">
    <div v-if="addressList.length">
      <div class="my_messagebox" v-for="(item,index) in addressList" :key="index">
        <div class="choicebox" @click="changeColor(item._id,index)">
          <span class="choice" :class="{active:item.ischeck}"></span>
        </div>
        <div class="my_message">
          <p class="my_message_p1">
            <span class="span_title">{{item.username}}</span>
            <span class="span_title">{{item.phone}}</span>
          </p>
          <p class="my_message_p2">{{item.region + item.detail_address}}</p>
          <p class="my_message_p2">{{item.email}}</p>
        </div>
        <span class="modify iconfont icondel" @click="remove(item._id,index)"></span>
      </div>
    </div>
    <div v-else>
      <div>
        <img class="address_img" src="/static/images/address.png" alt="">
        <div class="address_message">
          <p class="address_title">当前暂无详细收货地址</p>
          <p class="remind">请点击下方按钮新增</p>
        </div>
      </div>
    </div>
    <div class="add_address">
      <button class="add_btn" @click="newAdd">新增收货地址</button>
    </div>
  </div>
</template>

<script>
// import Newadd from "./newadd/newadd";

export default {
  data() {
    return {
      username: "",
      phone: "",
      region: "",
      address: "",
      email: "",
      isShow: false,
      isActive: false,
      addressList: [],
      arrcheck: []
    };
  },
  onLoad() {
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
        _openid: _that.openid
      })
      .get({
        success(res) {
          _that.addressList = res.data;
        }
      });
  },
  onShow() {
    //获取用户id
    let _that = this;
    const db = wx.cloud.database({ env: "xcxu-b70d87" });
    db
      .collection("useraddress")
      .where({
        _openid: _that.openid
      })
      .get({
        success(res) {
          _that.addressList = res.data;
        }
      });
  },
  methods: {
    newAdd() {
      wx.navigateTo({ url: "../newadd/main" });
    },
    changeColor(_id, index) {
      let _that = this;
      const db = wx.cloud.database({ env: "xcxu-b70d87" });
      let ischeckList = _that.addressList;
      for (let i in ischeckList) {
        if (ischeckList[i]._id === _id) {
          db
            .collection("useraddress")
            .doc(_id)
            .update({
              data: {
                ischeck: true
              }
            });
        } else {
          // console.log( ischeckList[i]._id);
          db
            .collection("useraddress")
            .doc(ischeckList[i]._id)
            .update({
              data: {
                ischeck: false
              }
            });

          db
            .collection("useraddress")
            .where({
              _openid: _that.openid
            })
            .get({
              success(res) {
                _that.addressList = res.data;
              }
            });
        }
      }

      db
        .collection("useraddress")
        .where({
          _openid: _that.openid
        })
        .get({
          success(res) {
            _that.addressList = res.data;
          }
        });
    },
    remove(_id, index) {
      let _that = this;
      const db = wx.cloud.database({ env: "xcxu-b70d87" });
      const _ = db.command;
      db
        .collection("useraddress")
        .doc(_id)
        .remove();
      _that.addressList.splice(index, 1);
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
.add_address {
  position: fixed;
  bottom: 0;
  left: 0;
  height: 80rpx;
  width: 100%;
  line-height: 80rpx;
  text-align: center;
}
.add_btn {
  display: block;
  width: 100%;
  height: 80rpx;
  font-size: 30rpx;
  background: red;
  color: #fff;
}
.my_messagebox {
  position: relative;
  width: 100%;
  border-bottom: solid 1rpx #eee;
  float: left;
}
.my_message {
  margin-bottom: 40rpx;
}
.my_message p {
  text-align: left;
}
.my_message_p1 {
  padding-top: 30rpx;
  padding-bottom: 5rpx;
}
.my_message_p2 {
  width: 480rpx;
  padding-bottom: 5rpx;
  overflow: hidden;
  -webkit-line-clamp: 1;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-box-orient: vertical;
}
.choicebox {
  height: 200rpx;
  width: 150rpx;
  float: left;
}
.choice {
  display: inline-block;
  width: 50rpx;
  height: 50rpx;
  border-radius: 50%;
  border: solid 1rpx #ccc;
  margin-left: 10rpx;
  margin-top: 50rpx;
}
.active {
  background: red;
}
.span_title {
  font-size: 28rpx;
  margin-right: 40rpx;
}
.my_message_p2 {
  font-size: 26rpx;
  color: #999;
}
.modify {
  position: absolute;
  right: 40rpx;
  top: 50%;
  color: red;
  font-size: 44rpx;
}
</style>