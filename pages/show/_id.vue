<template>
  <main hidden>
    <style>
    body {
      font-size: 300%;
      text-align: center;
      word-break: break-word;
    }
    /* 如果支持flex布局 */
    /* 更优雅的水平/垂直居中方式 */
    html {
      min-height: 100%;
      display: flex;
      flex-direction: column;
    }
    body {
      flex: 1;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    </style>

    <img v-if="showImage" :src="showImage">
    <h1 v-else v-text="showText" :style="textStyle"></h1>
  </main>
</template>

<script>
import loadStats from '~assets/stats'
import wxfn from '~/assets/wxfn'
import config from '~/config'
import axios from 'axios'
import _ from 'lodash'

export default {
  head () {
    return {
      title: this.shareTitle
    }
  },

  // asycnData for context params
  // https://github.com/nuxt/nuxt.js/issues/489
  async asyncData ({ params }) {
    let { data } = await axios
      .get(`${config.apiUrl}/get/${params.id}`)
    data = _.defaults(data, {
      showImage: '',
      showText: '',
      showTextSize: 1
    })
    return data
  },

  async mounted () {
    // hack: 解决nuxt 内容比style先加载的bug
    // https://github.com/nuxt/nuxt.js/issues/22
    this.$el.removeAttribute('hidden')

    loadStats()

    const base = location.protocol + '//' + location.host
    await wxfn.config()
    wxfn.share({
      title: this.shareTitle,
      desc: this.shareDesc,
      link: `${base}/show/${this.id}`,
      imgUrl: this.shareImage
        || `${base}/laoge.jpg`
    })
  },

  computed: {
    textStyle () {
      return {
        'font-size': this.showTextSize + 'em'
      }
    }
  }
}
</script>

<style scoped>
h1 {
  margin: 40px;
}
img {
  display: block;
  max-width: 100%;
}
</style>
