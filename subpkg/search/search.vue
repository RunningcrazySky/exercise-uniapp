<template>
  <view>
    <!-- 搜索框 -->
    <view class="search-box">
      <uni-search-bar focus="true" @input="input" :radius="100" cancelButton="none"></uni-search-bar>
    </view>
    <!-- 搜索建议 -->
    <view class="sugg-list" v-if="searchResults.length !== 0">
      <view class="sugg_item" v-for="(item,i) in searchResults" :key="i" @click="gotoDetail(item.goods_id)">
        <view class="goods-name">{{item.goods_name}}</view>
        <uni-icons type="arrowright" size="16"></uni-icons>
      </view>
    </view>
    <!-- 搜索历史 -->
    <view class="history_box" v-else>
      <view class="history_title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="17"  @click="clearHistory"></uni-icons>
      </view>
      <view class="history_list">
        <uni-tag :text="item" v-for="(item, i) in historyList" :key="i" @click="gotoGoodsList(item)"></uni-tag>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        timer: null,
        kw: '',
        searchResults: [],
        historyList: ['a', 'abb', 'abcd']
      };
    },
    onLoad(){
      this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
    },
    // 搜索关键词显示商品建议列表
    methods: {
      // 获取搜索列表
      async getSearchList() {
        // 判断关键词是否为空
        if (this.kw === '') {
          this.searchResults = []
          return
        }
        // 发起请求，获取搜索建议列表
        const { data: res } = await uni.$http.get('/api/public/v1/goods/qsearch', { query: this.kw })
        if (res.meta.status !== 200) return uni.$showMsg()
        this.searchResults = res.message
        this.saveSearchHistory()
      },
      // 输入框防抖处理
      input(e){
        clearTimeout(this.timer)
        this.timer = setTimeout(() => {
          this.kw = e
           // 搜索关键词显示商品建议列表
          this.getSearchList()
        }, 500)
      },
      // 搜索列表编程式导航
      gotoDetail(goods_id){
        uni.navigateTo({
          url:'/subpkg/goods_detail/goods_detail?goods_id=' + goods_id
        })
      },
      // 搜索历史去重/本地存储
      saveSearchHistory(){
        this.historyList.unshift(this.kw)
        this.historyList = [...new Set(this.historyList)]
        // 搜索关键词本地存储
        uni.setStorageSync('kw', JSON.stringify(this.historyList))
      },
      // 清除搜索历史
      clearHistory(){
        this.historyList = []
        uni.setStorageSync('kw', '[]')
      },
      // 点击搜索历史跳转商品列表
      gotoGoodsList(kw){
        uni.navigateTo({
          url:'/subpkg/goods_list/goods_list?query=' + kw
        })
      }
    }
  }
</script>

<style lang="scss">
  .search-box{
    position: sticky; 
    top: 0;
    z-index: 999;
  }
  uni-search-bar{
    text-align: left;
  }
  // 建议列表
  .sugg-list{
    padding: 0 5px;
    .sugg_item{
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 13px 0;
      border-bottom: 1px solid #efefef;
      .goods-name{
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        font-size: 12px;
        margin-right: 5px;
      }
    }
  }
  // 搜索历史
  .history_title{
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px 5px;
    border-bottom: 1px solid #efefef;
    text{
      font-size: 14px;
    }
  }
  .history_list{
    display: flex;
    flex-wrap: wrap;
    padding: 10px;
    uni-tag{
      margin: 0 5px;
      margin-bottom: 10px;
    }
  }
</style>
