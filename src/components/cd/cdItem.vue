<template>
  <div class="cd-sec" :class="{'cd-sec-sm': !flag}">
    <router-link class="a-box" :to="`/cdPage?id=${id}`">
      <div @click="showCdPage" class="img-box">
        <img v-lazy="img" alt="">
      </div>
    </router-link>

    <h3 class="cd-title">{{title}}</h3>
  </div>
</template>

<script>
  export default {
    // 封面，title，id
    props: ['img', 'title', 'id', 'flag'],
    methods: {
      showCdPage () {
        if (!this.$store.state.cdPageId) {
          this.$store.commit('changeCdPageId', this.id)
        }
      }
    }
  }
</script>

<style lang="scss" scoped>
.cd-sec-sm {
  width: calc(50% - 10px);
}

.cd-sec {
  display: flex;
  align-items: center;
  margin-right: 10px;
  margin-bottom: 20px;
  user-select: none;

  .a-box {
    width: 30%;
    margin-right: 10px;

    .img-box {
      overflow: hidden;
      display: flex;
      justify-content: center;
      align-items: center;
      border-radius: 15px;
      width: 100%;
      margin-bottom: 10px;
      background-color: #FFF;
      cursor: pointer;

      & img {
        width: 100%;
      }
    }
  }

  .cd-title {
    color: #b0b0b0;
    font-size: 14px;
  }
}
</style>