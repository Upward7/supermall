<template>
  <div class="cart">

    <nav-bar class="nav-bar">
      <div slot="center">购物车({{cartLength}})</div>
      <!-- <div slot="center">购物车({{length}})</div> -->
    </nav-bar>

    <cart-list></cart-list>

    <cart-bottom-bar></cart-bottom-bar>
  </div>
</template>

<script>
import NavBar from "components/common/navbar/NavBar";

import {mapGetters} from "vuex"

import CartList from './childComps/CartList';
import CartBottomBar from './childComps/CartBottomBar';

export default {
  components: {
    NavBar,
    CartList,
    CartBottomBar,
  },
  computed: {
    // mapGetters 辅助函数 将store中的getter映射到局部计算属性
    // 两种语法
    ...mapGetters([
      'cartLength'
    ]),
    // ...mapGetters({
    //   length: 'cartLength'
    // })
  }
};
</script>

<style>
.cart {
  height: 100vh;
}
.nav-bar {
  background-color: var(--color-tint);
  color: #fff;
}
</style>
