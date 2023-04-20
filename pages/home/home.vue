<template>
  <view>
    <!-- 自定义搜索框组件 -->
    <view class="search-box">
      <my-search @click='gotoSearch'></my-search>
    </view>
    <!-- 轮播图 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="(item, i) in swiperList" :key="i">
        <navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
          <image :src="item.image_src"></image>
        </navigator>
      </swiper-item>
    </swiper>
    <!-- 导航菜单 -->
    <view class="nav-list">
      <view class="nav-item" v-for="(item, i) in navList" :key="i" @click="navClickHandler(item)">
          <image :src="item.image_src" class="nav-img"></image>
      </view>
    </view>
    <!-- 楼层 -->
    <view class="floor-list">
      <view class="floor-item" v-for="(item,i) in floorList" :key="i">
          <!-- 楼层标题 -->
          <image class="floor-title" :src='item.floor_title.image_src'></image>
          <!-- 楼层图片 -->
          <view class="floor-img-box">
            <!-- 左侧大图片盒子 -->
            <navigator class="left-img-box" :url="item.product_list[0].url">
              <image :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
            </navigator>
            <!-- 右侧 4个图片盒子 -->
           <view class="right-img-box">
              <navigator v-for="(item2,i2) in item.product_list" :key="i2" v-if="i2 !== 0" :url="item2.url">
                <image :src="item2.image_src" :style="{width: item2.image_width + 'rpx'}" mode="widthFix"></image>
              </navigator>
            </view>
          </view>
      </view>     
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 轮播图数据
        swiperList: [],
        // 分类导航数据
        navList: [],
        // 楼层数据
        floorList: []
      };
    },
    onLoad(){
      this.getSwiperList(),
      this.getNavList(),
      this.getFloorList()
    },
    methods: {
      gotoSearch(){
        uni.navigateTo({
          url:'/subpkg/search/search'
        })
      },
      // 获取轮播图数据的方法
      async getSwiperList(){
        // 发起请求
        const {data: res} = await uni.$http.get('/api/public/v1/home/swiperdata')
        // 请求失败
        if(res.meta.status !== 200) return uni.$showMsg
        // 请求成功,为 data 中数据赋值
        this.swiperList = res.message
      },
      // 获取分类导航数据的方法
      async getNavList(){
        const { data: res } = await uni.$http.get('/api/public/v1/home/catitems')
        if(res.meta.status !== 200) return uni.$showMsg
        this.navList = res.message
      },
      // 分类导航点击事件
      navClickHandler(item){
        if(item.name === '分类'){
          uni.switchTab({
            url: '/pages/cate/cate'
          })
        }
      },
      // 获取楼层数据
      async getFloorList(){
        const { data: res } = await uni.$http.get('/api/public/v1/home/floordata')
        if(res.meta.status !== 200) return uni.$showMsg
        // 通过双层 forEach 循环，处理 URL 地址
        res.message.forEach(floor => {
          floor.product_list.forEach( prod => {
            prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
          })
        })
        this.floorList = res.message
        console.log(res)
      }
    }
  }
</script>

<style lang="scss">
  // 搜索框
  .search-box{
    position: sticky;
    top: 0%;
    z-index: 999;
  }
  // 轮播图
  swiper{
    height: 330rpx;
    .swiper-item, image{
      height: 100%;
      width: 100%;
    }
  }
  // 导航分类
  .nav-list{
    display: flex;
    justify-content: space-between;
    padding: 30rpx;
    .nav-img{
      width: 120rpx;
      height: 120rpx;
    }
  }
  // 楼层
  .floor-title{
    height: 60rpx;
    width: 100%;
  }
  .floor-img-box{
    display: flex;
    padding: 15rpx;
    image{
      margin: 5rpx;
    }
    .right-img-box{
      display: flex;
      flex-wrap: wrap;
    }
  }
</style>
