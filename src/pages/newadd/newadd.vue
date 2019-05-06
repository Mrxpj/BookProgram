<template>
  <div class="newadd_container">
    <div class="newadd_message">
      <ul class="newadd_message_ul">
        <li class="newadd_message_li">
          <span class="newadd_span">收货人</span>
          <input class="newadd_input" type="text" placeholder="请输入姓名" v-model="username">
        </li>
        <li class="newadd_message_li">
          <span class="newadd_span">联系方式</span>
          <input class="newadd_input" type="text" placeholder="请输入手机号" v-model="phone">
        </li>
        <li class="newadd_message_li">
          <div class="section">
            <div class="section__title"></div>
            <picker mode="region" @change="bindRegionChange($event)">
              <span class="newadd_span">省、市、区/县</span>
              <input class="picker newadd_input" placeholder="请选择地区" :value="region">
            </picker>
          </div>
        </li>
        <li class="newadd_message_li">
          <span class="newadd_span">详细地址</span>
          <input class="newadd_input" type="text" placeholder="街号楼号或者房间号信息" v-model="detail_address">
        </li>
        <li class="newadd_message_li">
          <span class="newadd_span">邮箱</span>
          <input class="newadd_input" type="text" placeholder="购买电子书产品必填" v-model="email">
        </li>
      </ul>
      <div class="save">
        <button class="save_btn" @click="setSave">保存</button>
      </div>
    </div>
    <toast :message="toastmessage" :visible.sync="visible"></toast>
  </div>
</template>

<script>
import toast from "mpvue-toast";
export default {
  name: "Newadd",
  components: {
    toast
  },
  data() {
    return {
      username: "",
      phone: "",
      region: "",
      detail_address: "",
      email: "",
      visible: false, //弹框控制,
      toastmessage: ""
    };
  },
  onload: function() {
    let _that = this;
    _that.username = "";
    _that.region = "";
    _that.phone = "";
    _that.email = "";
    _that.detail_address = "";
  },
  onShow: function() {
    let _that = this;
    _that.username = "";
    _that.region = "";
    _that.phone = "";
    _that.email = "";
    _that.detail_address = "";
  },
  methods: {
    bindRegionChange(e) {
      this.region = e.mp.detail.value;
    },
    setSave() {
      let _that = this;

      if(!(/^[\u4E00-\u9FA5A-Za-z]+$/.test(_that.username))){
        _that.visible = !_that.visible;
        _that.toastmessage = "请输入正确的姓名";
      }else if (!/^1[34578]\d{9}$/.test(_that.phone)) {
        _that.visible = !_that.visible;
        _that.toastmessage = "请输入正确的手机号码";
      }else if(!(/^[a-zA-Z0-9_.-]+@[a-zA-Z0-9-]+(\.[a-zA-Z0-9-]+)*\.[a-zA-Z0-9]{2,6}$/.test(_that.email))){
        _that.visible = !_that.visible;
        _that.toastmessage = "请输入正确的邮箱";
      }else {
        const url = "../address/main";
        const db = wx.cloud.database({ env: "xcxu-b70d87" });
        const _ = db.command;
        //先判断集合是否有这个地址和收货人和电话
        db
          .collection("useraddress")
          .where({
            username: _that.username,
            phone: _that.phone,
            region: _that.region
          })
          .get({
            success(res) {
              console.log(res.data);
              if (res.data.length === 0) {
                //如果没有就新增
                // console.log("----")
                db.collection("useraddress").add({
                  data: {
                    username: _that.username,
                    region: _that.region,
                    phone: _that.phone,
                    email: _that.email,
                    book_name: _that.book_name,
                    ischeck: false,
                    detail_address: _that.detail_address
                  }
                });
                wx.navigateTo({ url });
              } else {
                //有就不保存，弹出提示框
                _that.visible = !_that.visible;
                _that.toastmessage = "已有该地址";
              }
            }
          });
      }

      // this.visible = !this.visible;
    }
  }
};
</script>

<style scoped>
ul {
  list-style: none;
}
.newadd_message_li {
  height: 90rpx;
  border-bottom: 1rpx solid #ccc;
  line-height: 90rpx;
  width: 700rpx;
}
.newadd_message_ul {
  margin-left: 25rpx;
}
.newadd_span {
  float: left;
  font-size: 28rpx;
  margin-right: 50rpx;
  width: 180rpx;
  text-align: left;
}
.newadd_input {
  height: 90rpx;
  line-height: 90rpx;
  font-size: 28rpx;
}
.city {
  background: lightcoral;
  font-size: 28rpx;
}
.save {
  margin-top: 50rpx;
  width: 700rpx;
  margin-left: 25rpx;
}
.save_btn {
  display: block;
  width: 100%;
  height: 80rpx;
  font-size: 30rpx;
  background: red;
  color: #fff;
}
</style>