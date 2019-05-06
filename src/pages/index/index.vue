<template>
  <div class="index_container">
    <div class="search_bigbox">
      <div class="search_box">
        <span class="search_icon iconfont iconicon_search"
         @click="searchBook"></span>
        <input class="bookkey" type="text" placeholder="请输入图书关键词" 
        v-model="bookkey">
      </div>
    </div>
    <div class="swiper_container"><Swiper :images="images" /></div>
    <div class="book_type">
      <span class="type_icon iconfont icontubiao-"></span>
      <span class="type_title">图书分类</span>
    </div>
    <div class="type_box">
      <div class="type_container" v-for="(item, index) in iconlist" 
      :key="index" @click="changeType(item.type)">
        <p class="type_img iconfont" :class="item.iconname"></p>
        <p class="type_name">{{item.typename}}</p>
      </div>
    </div>
    <div class="rankbox">
      <span class="rank_icon iconfont iconranking"></span>
      <span class="rank_list">图书排行榜</span>
    </div>
    <div>
      <div class="book_rank" v-for="(item, index) in rankdata" :key="index">
        <div class="bookrank_left"><img :src="item.img" alt=""></div>
        <div class="bookrank_right">
          <div @click="detailPage(item.img,item.book_name,item.message,item.price,item._id)">
            <p class="book_title">{{item.book_name}}</p>
            <p class="book_description">{{item.message}}</p>
          </div><p class="book_money"><span class="money_num">￥{{item.price}}.00</span></p>
        </div>
      </div>
    </div><toast :message="message" :visible.sync="visible"></toast>
  </div>
</template>
<script>
// import card from '@/components/card'
import Swiper from "@/components/swiper";
import toast from "mpvue-toast";

export default {
  components: {
    Swiper,
    toast
  },
  data() {
    return {
      bookkey: "",
      IdArray: [],
      images: [
        {
          url: "/static/images/0.jpg"
        },
        {
          url: "/static/images/1.jpg"
        },
        {
          url: "/static/images/2.jpg"
        }
      ],
      iconlist: [
        {
          iconname: "iconjiaocaijiaofu blue",
          typename: "教辅",
          type: "teach"
        },
        {
          iconname: "iconwenxue-copy orange",
          typename: "文学",
          type: "literature"
        },
        {
          iconname: "icontongshu red",
          typename: "童书",
          type: "child"
        },
        {
          iconname: "iconxiaoshuo green",
          typename: "小说",
          type: "novel"
        },
        {
          iconname: "iconshangchuanjilu pink",
          typename: "传记",
          type: "biography"
        },
        {
          iconname: "icondianzishu cadetblue",
          typename: "电子书",
          type: "e-books"
        }
      ],
      rankdata: [], //排行榜数据
      visible: false,
      message: ""
    };
  },
  methods: {
    detailPage(img, book_name, message, price, _id) {
      const detail_url =
        "../detail/main?img=" +
        img +
        "&&book_name=" +
        book_name +
        "&&message=" +
        message +
        "&&price=" +
        price +
        "&&_id=" +
        _id;
      wx.navigateTo({ url: detail_url });
    },
    changeType(type) {  //点击分类跳转
      const type_url = "../typebook/main?type=" + type;
      wx.navigateTo({ url: type_url });
    },
    searchBook() {
      var _that = this;
      const db = wx.cloud.database({ env: "xcxu-b70d87" });
      if (_that.bookkey) {//传入关键字，在search页做查询
        const search_url = "../search/main?bookkey=" + _that.bookkey;
        // console.log(search_url)
        wx.navigateTo({ url: search_url });
      } else {
        _that.visible = !_that.visible;
        _that.message = "输入关键字才可以搜索喔~";
      }_that.bookkey = "";
    }
  },
   mounted() {
    // env是你云开发的环境id。
    let _that = this;
    const db = wx.cloud.database({ env: "xcxu-b70d87" });
    //查询排行榜的图书信息
    db //这里不能使用this.db，不然会报错
      .collection("books")
      .get({
        success(res) {
          _that.rankdata = res.data;
        }
      });
  },
  computed: {}
};
</script>

<style scoped>
@import "../../../static/iconfont/iconfont.css";

.search_bigbox {
  position: fixed;
  top: 0;
  left: 0;
  z-index: 99;
  width: 100%;
  height: 100rpx;
  background: #fff;
}
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
.swiper_container {
  margin-top: 100rpx;
}
.rankbox {
  clear: both;
  height: 80rpx;
  background: #eee;
  text-align: center;
  line-height: 80rpx;
  font-size: 34rpx;
  color: black;
}
.book_type {
  height: 80rpx;
  background: #eee;
  line-height: 80rpx;
  text-align: center;
  font-size: 34rpx;
  color: black;
}

.rank_icon,
.type_icon {
  color: black;
  margin-right: 10rpx;
}
.type_icon {
  font-size: 40rpx;
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
.book_title {
  font-size: 32rpx;
  margin-bottom: 20rpx;
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
}
.money_num {
  color: red;
}
.shopcar_icon {
  color: red;
  margin-left: 250rpx;
  font-size: 40rpx;
}
.type_container {
  float: left;
  margin-top: 40rpx;
  width: 25%;
  height: 150rpx;
  text-align: center;
}
.type_img {
  display: inline-block;
  height: 100rpx;
  width: 100rpx;
  line-height: 100rpx;
  text-align: center;
  border-radius: 50%;
  font-size: 40rpx;
  color: #fff;
}
.type_name {
  margin-top: 10rpx;
  font-size: 28rpx;
  width: 180rpx;
  text-align: center;
}
.red {
  background: red;
}
.orange {
  background: orange;
}
.blue {
  background: rgb(41, 41, 247);
}
.green {
  background: rgb(4, 248, 4);
}
.cadetblue {
  background: cadetblue;
}
.pink {
  background: rgb(250, 167, 181);
}
/* .toast_class {
  position: fixed;
  display: block;
  top: 50%;
  left: 50%;
  margin-left: -85rpx;
  margin-top: -40rpx;
  width: 170rpx;
  height: 80rpx;
  background: rgba(255, 255, 255, 0.3);
} */
</style>
