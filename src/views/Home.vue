<template>
  <div class="home">
    <h1>{{ currentTime }}</h1>
    <Qrcode :request="getCode" :timeout="3000">
      <template v-slot:failed>
        <div>自定义失败文案</div>
      </template>
      <template v-slot:refresh>
        <div>自定义刷新文案</div>
      </template>
    </Qrcode>
  </div>
</template>

<script>
import Qrcode from '@/components/qrcode.vue'
import dayjs from 'dayjs'

export default {
  name: 'QrcodePage',
  components: {
    Qrcode
  },
  data() {
    return {
      timer: '', // 定义一个定时器的变量
      currentTime: dayjs().format('MM月DD日 HH:MM:ss')
    }
  },
  created() {
    // 创建定时器，每一秒都更新时间
    this.timer = setInterval(() => {
      this.currentTime = dayjs().format('MM月DD日 HH:MM:ss')
    }, 1000)
  },
  beforeDestroy() {
    // 清空展示的定时器
    this.timer && clearInterval(this.timer)
  },
  methods: {
    // 获取二维码的方法
    async getCode() {
      try {
        const res = await this.getRequestFromMock()
        return res
      } catch (e) {
        console.log('当前二维码获取失败', e)
      }
    },
    // 模拟，生成二维码的异步请求
    getRequestFromMock() {
      return new Promise((resolve, reject) => {
        if (Math.random() > 0.5) {
          resolve('success')
        } else {
          reject(new Error('failed'))
        }
      })
    }
  }
}
</script>
