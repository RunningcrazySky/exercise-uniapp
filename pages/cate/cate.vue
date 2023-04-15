<template>
  <view>
    <!-- 搜索框 -->
    <view class="search-box">
      <input type="text" placeholder="搜索" class="search">
    </view>
    <!-- 滚动区域 -->
    <view class="scroll-view-container">
      <!-- 左侧滚动视图区域 -->
      <scroll-view class="left-scroll-view" scroll-y :style="{height: wh + 'px'}">
        <block v-for="(item, i) in cateList" :key="i">
          <view :class="['left-scroll-view-item', item.i === active ? 'active' : '']" @click="activeChanged(i)">{{item.cat_name}}</view>
        </block>
      </scroll-view>
      <!-- 右侧滚动视图区域 -->
      <scroll-view class="right-scroll-view" scroll-y :style="{height: wh + 'px'}" :scroll-top="scrollTop">
        <blockquote class="cate-lv2" v-for="(item2, i2) in cateLevel2" :key="i2">
          <view class="cate-lv2-title" @click="activeChangedlv2(i2)">{{item2.cat_name}}</view>
          <!-- 动态渲染三级分类列表 -->
          <view class="cate-lv3-list">
                <!-- 三级分类 Item 项 -->
            <view class="cate-lv3-item" v-for="(item3, i3) in item2.children" :key="i3" @click="goToGoodsList(item3)">
              <!-- 图片 -->
              <image :src="item3.cat_icon.replace('dev', 'web')"></image>
              <!-- 文本 -->
              <text>{{item3.cat_name}}</text>
            </view>
          </view>
        </blockquote>
      </scroll-view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 窗口可用高度=窗口高度-tabbar高度-navigationbar高度
        wh: 0,
        cateList: [],
        active: 0,
        cateLevel2: [],
        scrollTop: 0
      };
    },
    onLoad(){
      // 当前系统信息
      const sysInfo = uni.getSystemInfoSync()
      // 窗口可用高度
      this.wh = sysInfo.windowHeight     
      this.getCateList()
    },
    methods: {
      async getCateList(){
        const {data: res} = await uni.$http.get('/api/public/v1/categories')
        if(res.meta.status !== 200) return uni.$show
        this.cateList = res.message
        this.cateLevel2 = res.message[0].children
      },
      activeChanged(i){
        this.active = i
        this.cateLevel2 = this.cateList[i].children
        this.scrollTop = this.scrollTop ? 0 : 1
      },
      goToGoodsList(item3){
        uni.navigateTo({
          url:'/subpkg/goods_list/goods_list?cid=' + item3.cat_id
        })
      }
    }
  }
</script>

<style lang="scss">
  $pink: #F4C8D8;
  $white: #fff;
  // 搜索框
  .search-box{
    padding: 10rpx 20rpx;
    background: $pink;
    .search{
      padding: 10rpx;
      background-color: $white;
      border-radius: 30rpx;
      text-align: center;
      font-size: 28rpx;
      &::placeholder{
        // padding: 20rpx;
        font-size: 32rpx;
      }
    }
  }
  // 滚动框
  .scroll-view-container{
    display: flex;
    text-align: center;
    .left-scroll-view{
      width: 120px;
      .left-scroll-view-item{
        line-height: 60px;
        background-color: #f7f7f7;
        font-size: 12px;
        &.active{
          background-color: #fff;
          position: relative;
          // 渲染激活项左侧的红色指示边线
          &::before {
            content: ' ';
            display: block;
            width: 3px;
            height: 30px;
            background-color: #c00000;
            position: absolute;
            left: 0;
            top: 50%;
            transform: translateY(-50%);
          }
        }
      }
    }
    .cate-lv2-title{
      font-size: 12px;
      font-weight: bold;
      text-align: center;
      padding: 15px 0;
    }
    .cate-lv3-list{
      display: flex;
      flex-wrap: wrap;
      .cate-lv3-item{
        width: 33.33%;
        margin-bottom: 10px;
        display: flex;
        flex-direction: column;
        align-items: center;
        image{
          width: 60px;
          height: 60px;
        }
        text{
          font-size: 12px
        }
      }
    }
  }
</style>
