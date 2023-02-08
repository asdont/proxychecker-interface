<template>
  <div class="wrapper">
    <h2>Send proxies</h2>

    <div class="proxies-in">
      <textarea v-model="proxiesIn" required
                placeholder="socks5://login:pass@1.1.1.1:99&#10;socks4://login:pass@2.2.2.2:8000&#10;http://3.3.3.3:90"
      ></textarea>
    </div>
    <div>
      <button class="load-button"
              :disabled="buttonSendProxiesIsDisabled || proxiesIn === ''"
              @click="checkProxies"
      >
        {{ buttonSendProxiesText }}
      </button>
    </div>
  </div>

  <div class="wrapper">
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Proxies",

  data() {
    return {
      proxiesIn: "http://127.0.0.1:80",
      proxiesOut: {},
      requestID: "",
      buttonSendProxiesText: "Check",
      buttonSendProxiesCheckText: "Check",
      buttonSendProxiesCheckingText: "Checking ...",
      buttonSendProxiesIsDisabled: false
    }
  },

  methods: {
    async checkProxies() {
      this.buttonSendProxiesText = this.buttonSendProxiesCheckingText
      this.buttonSendProxiesIsDisabled = true


      await axios(
          {
            method: 'post',
            baseURL: import.meta.env.VITE_API_V1,
            url: '/proxies',
            data: this.proxiesIn
          }
      )
          .then(resp => (
                  this.getProxiesByRequestID(resp.data.requestID)
              ),
          )
          .catch(err => {
            console.log(err)
            // if (err) {
            //   this.$emit('setAppError', "get redirect link: " + err)
            //   console.log(err)
            // }
          })

      this.buttonSendProxiesText = this.buttonSendProxiesCheckText
      this.buttonSendProxiesIsDisabled = false
    },

    async getProxiesByRequestID(requestID) {
      let statusRequest = ''

      await axios(
          {
            method: 'get',
            baseURL: import.meta.env.VITE_API_V1,
            url: '/proxies/' + requestID,
          }
      )
          .then(resp => (
                  statusRequest = resp.data.status
              ),
          )
          .catch(err => {
            console.log(err)
            // if (err) {
            //   this.$emit('setAppError', "get redirect link: " + err)
            //   console.log(err)
            // }
          })
    },

    checkStatusRequest(statusRequest) {
      console.log(statusRequest)

      if (statusRequest === 'NOT_READY') {
        setTimeout(function() {

          console.log(requestID)

          getProxiesByRequestID(requestID)
        }, 2000, this);
      }
    }
  },
}
</script>

<style scoped>

</style>