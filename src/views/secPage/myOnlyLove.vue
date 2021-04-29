<template>
<keep-alive>
  <div class="fix-top">
    <swiper :imgList="imgList"></swiper>

    <!-- 登录 -->
    <Modal
      v-model="show"
      footer-hide
      :closable="false"
      width="300" class="modle-box">
      <div class="title-info-box">
        <input class="cd-name-input" @focus="stopPlayBar(true)" @blur="stopPlayBar(false)" 
        v-model="postUsername" type="text" placeholder="请输入用户名" name="">

        <input class="cd-name-input" @focus="stopPlayBar(true)" @blur="stopPlayBar(false)" 
        v-model="postPassword" type="password" placeholder="请输入密码" name="">

        <div class="button-box">
          <button @click="logOff" class="save-button">注销</button>
          <button @click="login" class="save-button">登录</button>
        </div>
      </div>
    </Modal>

    <div class="user-sec">
      <!-- 头像区域 -->
      <div @click="showLogin" class="icon-sec">
        <div class="icon-img">
          <img :src="icon" alt="">
        </div>
        <span class="icon-text">{{username}}</span>
      </div>
      <!-- 头像区域end -->

      <router-link to="/ddPlayPage">
        <button class="save-button">我的歌单</button>
      </router-link>
    </div>

    <div class="bm-love-sec">
      <h2 class="bm-love-title">单推专辑</h2>

      <div class="cd-item-sec">
        <cd-item :img="allSong.img" :title="allSong.title"
        :id="allSong.id"></cd-item>

        <cd-item v-for="(item, index) in cdList" :key="index"
        :img="item.imgUrl" :title="item.title" :id="item._id"></cd-item>
      </div>
    </div>
  </div>
</keep-alive>
</template>

<script>
  import swiper from '../../components/swiper/swiper'
  import cdItem from '../../components/cd/cdItem'
  import { getAPI, getUserAPI } from '../../assets/js/common'

  export default {
    data () {
      return {
        // 轮播图数组，最少存在3个数据
        imgList: [
          {
            imgUrl: require('../../assets/img/swiper/73713002_p0.jpg')
          },
          {
            imgUrl: require('../../assets/img/swiper/77635312_p0.jpg')
          },
          {
            imgUrl: require('../../assets/img/swiper/78468133_p0.jpg')
          }
        ],
        // cd数组
        cdList: [],
        // 总歌单
        allSong: {
          img: require('../../assets/img/allSong.jpg'),
          title: '歌单汇总',
          id: '001'
        },
        // 用户登陆
        show: false,
        // 用户名
        postUsername: '',
        // 密码
        postPassword: '',
        // 用户名
        username: this.$store.state.username,
        // 头像
        icon: this.$store.state.icon
      }
    },
    created () {
      this.getCd()

      let username = this.$cookies.get('username')
      let icon = this.$cookies.get('icon')

      username ? this.$store.commit('changeUsername', username) : this.username = this.$store.state.username
      icon ? this.$store.commit('changeIcon', icon) : this.icon = this.$store.state.icon
    },
    methods: {
      // 获得cdlist
      getCd () {
        this.$http.get(`${getAPI()}/getCd`)
          .then(res => {
            // 替换http为https
            let data = res.data

            data.list.forEach(ele => {
              ele.imgUrl = ele.imgUrl.replace(/http/g, 'https')
            })

            this.cdList = res.data.list
          })
      },
      // 检测是否开始输入
      stopPlayBar (flag) {
        this.$store.commit('changeTextInFlag', flag)
      },
      /**
       * 显示登录界面
       */
      showLogin () {
        this.show = !this.show
      },
      /**
       * 登录
       */
      login () {
        if (!this.postUsername || !this.postPassword) {
          this.$Message.warning('没输完草peko');
        } else {
          let password = this.$md5(this.postPassword)

          this.$http.post(`${getUserAPI()}/login`, {
            username: this.postUsername,
            password: password
          }).then(res => {
            if (res.data.status === 2000) {
              let data = res.data.data

              data.icon = data.icon.replace(/http/g, 'https')

              this.$store.commit('changeUsername', data.username)
              data.icon ? this.$store.commit('changeIcon', data.icon) : 0

              this.$cookies.set('username', data.username, '30d')
              this.$cookies.set('icon', data.icon, '30d')
              this.$cookies.set('userId', data.userId, '30d')

              this.postUsername = ''
              this.postPassword = ''

              this.showLogin()
            } else if (res.data.status === 2) {
              this.$Message.error('账号或密码错误');
            } else {
              this.$Message.error('服务器错误');
            }
            
          })
        }
      },
      // 注销
      logOff () {
        this.$cookies.remove('username')
        this.$cookies.remove('icon')
        this.$cookies.remove('userId')

        this.$store.commit('changeUsername', 'いらっしゃい〜')
        this.$store.commit('changeIcon', require('../../assets/img/icon.jpg'))

        this.showLogin()
      }
    },
    computed: {
      vuexUsername () {
        return this.$store.state.username
      },
      vuexIcon () {
        return this.$store.state.icon
      }
    },
    watch: {
      vuexUsername (newVal) {
        this.username = newVal
      },
      vuexIcon (newVal) {
        this.icon = newVal
      }
    },
    components: {
      swiper,
      cdItem
    }
  }
</script>

<style lang="scss" scoped>
::v-deep .ivu-modal-body {
  background-color: #212121;
  border-radius: 5px;
}

// 登录
.title-info-box {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  height: 110px;
  width: 100%;
  background-color: #212121;

  .cd-name-input {
    padding: 5px;
    width: 100%;
    border-radius: 5px;
    background-color: #303030;
    border: none;
    color: #FFF;
    outline: none;
  }

  .button-box {
    display: flex;
    justify-content: space-around;
    margin-top: 10px;
    width: 100%;

    .save-button {
      user-select: none;
      width: 100px;
      background-color: #82342c;
      color: #FFF;
      border: none;
      border-radius: 10px;
      padding: 5px 20px;
      outline: none;
      cursor: pointer;
    }
  }
}

.user-sec {
  padding: 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;

  // 头像区域
  .icon-sec {
    display: flex;
    align-items: center;
    cursor: pointer;

    .icon-img {
      overflow: hidden;
      width: 40px;
      height: 40px;
      border-radius: 50%;
      margin-right: 10px;

      & img {
        width: 100%;
      }
    }

    .icon-text {
      color: #b0b0b0;
      font-size: 14px;
      font-weight: 600;
    }
  }
  // 头像区域end

  .save-button {
    user-select: none;
    width: 100px;
    background-color: #82342c;
    color: #FFF;
    border: none;
    border-radius: 10px;
    padding: 5px 20px;
    outline: none;
    cursor: pointer;
  }
}

.bm-love-sec {
  min-height: 100vh;
  padding: 0 20px 40px;

  .bm-love-title {
    margin-bottom: 20px;
    font-size: 16px;
    color: #b0b0b0;
    font-weight: normal;
  }

  .cd-item-sec {
    display: flex;
    flex: 1;
    flex-wrap: wrap;
    width: 94vw;
  }
}
</style>