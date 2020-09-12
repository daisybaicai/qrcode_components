<template>
  <div id="qrcode">
    <div class="refresh" @click="getCode" v-if="refresh">
      <slot name="failed" v-if="failed">
        <div>
          <p>
            二维码获取失效
            <br />请点击刷新
          </p>
        </div>
      </slot>
      <slot name="refresh" v-else>
        <div>
          <p>
            二维码过期
            <br />请点击刷新
          </p>
        </div>
      </slot>
    </div>
  </div>
</template>

<script>
export default {
  name: 'QrCode',
  props: {
    // 超时刷新时间
    timeout: {
      type: Number,
      default: 2000
    },
    // code 请求, 返回值为渲染的二维码的内容
    request: {
      type: Function,
      default: () => {}
    }
  },
  data() {
    return {
      refresh: false,
      freshCodeTimer: null,
      failed: false
    }
  },
  created() {
    this.downCount()
    this.getCode()
  },
  mounted() {
    this.code = new QRCode(document.getElementById('qrcode'), {
      text: '',
      width: 200,
      height: 200,
      colorDark: 'rgba(69, 78, 106, 1)',
      colorLight: '#ffffff',
      correctLevel: QRCode.CorrectLevel.H
    })
  },
  beforeDestroy() {
    this.freshCodeTimer && clearInterval(this.freshCodeTimer)
  },
  methods: {
    downCount() {
      this.freshCodeTimer = setTimeout(() => {
        this.refresh = true
      }, this.timeout)
    },
    async getCode() {
      const qrCodeText = await this.request()
      if (qrCodeText) {
        this.failed = false
        this.refresh = false
        this.downCount()
        this.code.clear()
        this.code.makeCode(qrCodeText)
      } else {
        this.refresh = true
        this.failed = true
      }
    }
  }
}
</script>

<style lang="less" scoped>
#qrcode {
  position: relative;
  width: 200px;
  height: 200px;
  border: 2px solid whitesmoke;
  .refresh {
    position: absolute;
    display: flex;
    justify-content: center;
    align-items: center;
    top: 0;
    left: 0;
    width: 201px;
    height: 200px;
    background-color: rgba(126, 126, 126, 0.8);
    font-size: 16px;
    font-family: PingFangSC-Medium, PingFang SC;
    font-weight: 500;
    color: #ffffff;
  }
}
</style>
