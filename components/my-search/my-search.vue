<template>
  <view class="my-search-container" :style="{'background-color': bgcolor}">
    <view class="my-search-box" :style="{'border-radius': radius + 'px'}" @click="searchBoxHandler">
      <uni-icons type="search" size="17"></uni-icons>
      <text class="placeholder">搜索</text>
    </view>
  </view>
</template>

<script>
  export default {
    name:"my-search",
    props: {
      // 背景颜色
      bgcolor: {
        type: String,
        default: '#f4c8d8'
      },
      // 圆角尺寸
      radius: {
        type: Number,
        // 单位是 px
        default: 18
      }
    },
    data() {
      return {
      };
    },
    methods: {
      searchBoxHandler(){
        this.$emit('click')
      }
    }
  }
</script>

<style lang="scss">
  .my-search-container{
    height: 50px;
    padding: 0 10px;
    display: flex;
    align-items: center;
    .my-search-box{
      background-color: #fff;
      width:100%;
      height: 36px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 15px;
      uni-icons{
        margin-right: 5px;
      }
    }
  }
</style>