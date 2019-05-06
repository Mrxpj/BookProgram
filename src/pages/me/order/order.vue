<template>
    <div class="address_container">
        <div>
            <img class="address_img" src="/static/images/order.png" alt="">
            <div class="address_message">
                <p class="address_title">还没有订单呢~</p>
            </div>
        </div>
    </div>
</template>

<script>
export default {};
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
</style>