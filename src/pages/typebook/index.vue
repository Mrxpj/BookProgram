<template>
  <div class="type_container">
    <div v-if="typeList.length">
      <div class="book_rank" v-for="(item, index) in typeList" :key="index">
        <div class="bookrank_left">
          <img :src="item.img" alt="">
        </div>
        <div class="bookrank_right">
          <div @click="detailPage(item.img,item.book_name,item.message,item.price,item._id)">
            <p class="book_title">{{item.book_name}}</p>
            <p class="book_description">{{item.message}}</p>
          </div>
          <p class="book_money">
            <span class="money_num">{{item.price}}</span>
            <!-- <span class="shopcar_icon iconfont icongouwuche" @click='setVisible(false)'></span> -->
          </p>
        </div>
      </div>
    </div>

    <div v-else>
      <img class="address_img" src="/static/images/typebook.png" alt="">
      <div class="address_message">
        <p class="address_title">该类型的书已经没有啦~去看看其他书吧</p>
        <button class="type_btn" @click="backIndex">回到首页</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      type:"",
      typeList:[]
    }
  },
  onLoad(options){
    let _that = this;
    _that.type = options.type;
    console.log(_that.type);
    const db = wx.cloud.database({ env: "xcxu-b70d87" });
     db
      .collection("typebook")
      .where({
        type: _that.type
      })
      .get({
        success(res) {
          _that.typeList = res.data;
          // console.log(_that.typeList)
          console.log(res);
        }
      });
  },
  methods: {
    backIndex(){
      wx.switchTab({
        url: "../index/main"
      });
    },
    detailPage(img,book_name,message,price,_id) {
      const detail_url = "../detail/main?img=" + img + "&&book_name=" + book_name + "&&message=" + message + "&&price=" + price + "&&_id=" + _id;
      wx.navigateTo({ url: detail_url });
    },
  }
}
</script>

<style scoped>
.type_container {
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
.type_btn {
  display: block;
  width: 200rpx;
  height: 80rpx;
  line-height: 80rpx;
  text-align: center;
  background: red;
  color: #fff;
  font-size: 32rpx;
  margin-top: 30rpx;
}
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
  margin-bottom: 20rpx;
  overflow: hidden;
  -webkit-line-clamp: 1;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  text-align: left;
}
.book_description {
  font-size: 30rpx;
  overflow: hidden;
  -webkit-line-clamp: 2;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  color: #828282;
}
.book_money {
  margin-top: 20rpx;
  text-align: left;
}
.money_num {
  color: red;
}

</style>
