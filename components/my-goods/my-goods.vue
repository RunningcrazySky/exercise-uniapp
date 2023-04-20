<template>
  <view>
    <view class="goods-item">
      <!-- 左边图片 -->
      <view class="goods-img">
        <image :src="goods.goods-small-logo || defaultPic" class="goods-pic"></image>
      </view>
      <!-- 右边文字 -->
      <view class="goods-info">
        <view class="goods-name">{{goods.goods_name}}</view>
        <view class="goods-price">￥{{goods.goods_price | tofixed}}</view>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    name:"my-goods",
    // 定义 props 属性，用来接收外界传递到当前组件的数据
    props: {
      goods: {
        type: Object,
        default: {}
      }
    },
    data() {
      return {
        defaultPic: 'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png'
      };
    },
    filters: {
      tofixed(num){
        return Number(num).toFixed(2)
      }
    }
  }
</script>

<style lang="scss">
.goods-item{
    display: flex;
    padding: 10px 5px;
    border-bottom: 1px solid #f0f0f0;
    // 左侧图片
    .goods-img{
      margin-right: 5px;
      .goods-pic{
        width: 100px;
        height: 100px;
        display: block;
      }
    }
    // 右侧文字
    .goods-info{
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      .goods-name {
        font-size: 13px;
      }
      .goods-price {
        font-size: 16px;
        color: #c00000;
      }
    }
  }
</style>