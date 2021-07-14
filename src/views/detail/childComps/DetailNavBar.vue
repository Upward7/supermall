<template>
  <div>
    <nav-bar>
      <div slot="left" class="back" @click="backClick">
        <img src="~assets/img/common/back.svg" alt="">
      </div>
      <div slot="center" class="title">
        <div class="title-item" 
        v-for="(item, index) in titles" :key="index"
        :class="{active: index == currentIndex}"
        @click="itemClick(index)">
          {{ item }}
        </div>
      </div>
    </nav-bar>
  </div>
</template>

<script>
import NavBar from "components/common/navbar/NavBar";
export default {
  components: {
    NavBar,
  },
  data() {
    return {
      titles: ["商品", "参数", "评论", "推荐"],
      currentIndex: 0,
    };
  },
  methods: {
    backClick() {
      this.$router.back()
    },
    itemClick(index) {
      this.currentIndex = index
      this.$emit('titleClick', index)
    },
  },
};
</script>

<style>
.title {
  display: flex;
  font-size: 14px;
}
.title-item {
  flex: 1;
}
.active {
  color: var(--color-high-text)
}

.back img{
  vertical-align: middle;
}
</style>