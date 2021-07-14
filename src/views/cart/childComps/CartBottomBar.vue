<template>
  <div class="bottom-bar">
    <div class="check-content">
      <check-button
        :isChecked="isSelectAll"
        class="check-button"
        @click.native="checkClick"
      ></check-button>
      <span>全选</span>
    </div>

    <div class="price">合计:{{ totalPrice }}</div>

    <div class="calculate">去计算({{ checkLength }})</div>
  </div>
</template>

<script>
import CheckButton from "components/content/checkButton/CheckButton";
import { mapGetters } from "vuex";
export default {
  components: {
    CheckButton,
  },
  computed: {
    ...mapGetters(["cartList"]),
    totalPrice() {
      return (
        "￥" +
        this.cartList
          .filter((item) => {
            return item.checked;
          })
          .reduce((preValue, item) => {
            return preValue + item.price * item.count;
          }, 0)
          .toFixed(2)
      );
    },
    checkLength() {
      // return this.cartList.filter((item) => {
      //   return item.checked
      // }).reduce((preValue,item) => {
      //   return preValue + item.count
      // },0)
      return this.cartList.filter(item => item.checked).length
    },
    isSelectAll() {
      if (this.cartList.length === 0) return false;
      // return ! (this.cartList.filter(item => !item.checked).length)

      return !this.cartList.find((item) => !item.checked);

      // for(let item of this.cartList){
      //   if(!item.checked){
      //     return false
      //   }
      // }
      // return true
    },
  },
  methods: {
    checkClick() {
      if (this.isSelectAll){
        this.cartList.forEach(item => item.checked = false)
      }else {
        this.cartList.forEach(item => item.checked = true)
      }
    }
  },
};
</script>

<style>
.bottom-bar {
  position: relative;
  display: flex;

  height: 40px;
  line-height: 40px;

  background-color: #eee;

  font-size: 14px;
}
.check-content {
  display: flex;
  align-items: center;
  margin-left: 10px;
  margin-right: 20px;
  width: 60px;
}
.check-button {
  height: 20px;
  width: 20px;
  line-height: 20px;
  margin-right: 5px;
}
.price {
  flex: 1;
}
.calculate {
  width: 90px;
  background-color: red;
  color: #fff;
  text-align: center;
}
</style>