<template>
  <view class="cart-container" v-if="cart.length !== 0">
    <!-- 购物地址 -->
    <my-address></my-address>
    <!-- 购物商品 -->
    <uni-swipe-action>
      <!-- 购物车商品列表的标题区域 -->
      <view class="cart-title">
        <!-- 左侧的图标 -->
        <uni-icons type="shop" size="18"></uni-icons>
        <!-- 描述文本 -->
        <text class="cart-title-text">购物车</text>
      </view>
      <!-- 购物车物品 -->
      <view v-for="(goods, i) in cart" :key="i">
        <uni-swipe-action-item :right-options='options' @click="swipeActionClickHandler(goods)">
          <my-goods :goods="goods" :show-radio="true" :show-num="true" @radio-change='radioChangeHandler' @num-change='numberChangeHandler'></my-goods>
        </uni-swipe-action-item>
      </view>
    </uni-swipe-action>
    <!-- 结算区 -->
    <my-settle></my-settle>
  </view>
  <!-- 空白购物车区域 -->
  <view class="empty-cart" v-else>
    <image src="/static/cart/emptycart.gif" class="empty-img" mode="widthFix"></image>
    <text class="tip-text">空空如也~</text>
  </view>
</template>

<script>
import badgeMix from '@/mixins/tabbar-badge.js'
import { mapMutations } from 'vuex';
import { mapState } from 'vuex';
  export default {
    mixins: [badgeMix],
    data() {
      return {
        options: [{
          text: '删除',
          style: {
            backgroundColor: '#d4237a'
          }
        }]
      };
    },
    computed: {
      ...mapState('m_cart', ['cart'])
    },
    methods: {
      ...mapMutations('m_cart', ['updateGoodsState', 'updateGoodsCount', 'removeGoodsById']),
      // 商品勾选状态改变
      radioChangeHandler(e){
        this.updateGoodsState(e)
      },
      // 商品数量改变
      numberChangeHandler(e){
        this.updateGoodsCount(e)
      },
      // 点击滑动操作按钮
      swipeActionClickHandler(goods){
        this.removeGoodsById(goods.goods_id)
      }
    }
  }
</script>

<style lang="scss">
  .cart-container{
    padding-bottom: 50px;
    .cart-title {
      height: 40px;
      display: flex;
      align-items: center;
      font-size: 14px;
      padding-left: 5px;
      border-bottom: 1px solid #efefef;
      .cart-title-text {
        margin-left: 10px;
      }
    }
  }
  .empty-cart{
    display: flex;
    flex-direction: column;
    padding-top: 150px;
    align-items: center;
    font-size: 12px;
    color: gray;
    .empty-img{
      width: 50%;
    }
  }
</style>
