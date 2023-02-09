<template>
  <div class="wrapper">
    <h2>Send proxies</h2>

    <div class="proxies-in">
      <textarea v-model="proxiesIn" required
                placeholder="socks5://login:pass@1.1.1.1:99&#10;socks4://login:pass@2.2.2.2:8000&#10;http://3.3.3.3:90"
      ></textarea>
    </div>

    <div>
      <button class="btn"
              :disabled="buttonSendProxiesIsDisabled || proxiesIn === ''"
              @click="checkProxies"
      >
        {{ buttonSendProxiesText }}
        <span v-if="buttonSecondCounter > 0">
          {{ ((buttonSecondCounter % 1000) / 10).toFixed(1) }}
        </span>
      </button>
    </div>
  </div>

  <div class="wrapper">
    <h2>Checked proxies</h2>

    <div class="table-wrap">
      <table>
        <thead>
        <tr>
          <th id="th-n">#</th>
          <th id="th-status" @click="sortByStatus">Status</th>
          <th id="th-address" @click="sortByAddress">Proxy</th>
          <th id="th-real-ip" @click="sortByRealIP">IP</th>
          <th id="th-country" @click="sortByCountry">Country</th>
          <th id="th-region" @click="sortByRegion">Region</th>
          <th id="th-city" @click="sortByCity">City</th>
          <th id="th-comment" @click="sortByComment">Comment</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="(p, idx) in proxiesOut.proxies" :key="idx">
          <th scope="row">{{ idx + 1 }}</th>
          <td>{{ p.status ? p.status : '-' }}</td>
          <td>{{ p.address ? p.address : '-' }}</td>
          <td>{{ p.realIP ? p.realIP : '-' }}</td>
          <td>{{ p.country ? p.country : '-' }}</td>
          <td>{{ p.region ? p.region : '-' }}</td>
          <td>{{ p.city ? p.city : '-' }}</td>
          <td>{{ p.comment ? p.comment : '-' }}</td>
        </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Proxies",

  data() {
    return {
      proxiesIn: "",
      proxiesOut: {},
      requestID: "",

      buttonSendProxiesText: "Check",
      buttonSendProxiesCheckText: "Check",
      buttonSendProxiesCheckingText: "Checking ...",
      buttonSendProxiesIsDisabled: false,
      buttonSecondCounter: 0,
      buttonSecondCounterStart: false,

      sortingFlag: true
    }
  },

  methods: {
    async checkProxies() {
      this.proxiesOut = {}

      this.buttonSecondCounterStart = true
      this.secondCounter()

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
            console.log("check proxies", err)
          })

      this.buttonSecondCounterStart = false

      this.buttonSendProxiesText = this.buttonSendProxiesCheckText
      this.buttonSendProxiesIsDisabled = false
    },

    async getProxiesByRequestID(requestID) {
      for (let i = 0; i < 5; i++) {
        await axios(
            {
              method: 'get',
              baseURL: import.meta.env.VITE_API_V1,
              url: '/proxies/' + requestID,
            }
        )
            .then(resp => (
                    this.proxiesOut = resp.data
                ),
            )
            .catch(err => {
              console.log("get prxies by request id", err)
            })

        if (this.proxiesOut.status === 'OK') {
          break
        }

        await this.timeout(2500)
      }
    },

    timeout(ms) {
      return new Promise(resolve => setTimeout(resolve, ms))
    },

    secondCounter() {
      const interval = setInterval(() => {
        this.buttonSecondCounter = this.buttonSecondCounter + 1

        if (this.buttonSecondCounterStart === false) {
          this.buttonSecondCounter = 0

          clearInterval(interval)
        }
      }, 100)
    },

    sortByStatus() {
      if (this.sortingFlag) {
        this.proxiesOut.proxies = [...this.proxiesOut.proxies].sort(function (a, b) {
          return a.status - b.status
        })
      } else {
        this.proxiesOut.proxies = [...this.proxiesOut.proxies].sort(function (a, b) {
          return b.status - a.status
        })
      }

      this.sortingFlag = !this.sortingFlag;
    },
    sortByAddress() {
      if (this.sortingFlag) {
        this.proxiesOut.proxies = [...this.proxiesOut.proxies].sort(function (a, b) {
          return a.address - b.address
        })
      } else {
        this.proxiesOut.proxies = [...this.proxiesOut.proxies].sort(function (a, b) {
          return b.address - a.address
        })
      }

      this.sortingFlag = !this.sortingFlag;
    },
    sortByRealIP() {
      if (this.sortingFlag) {
        this.proxiesOut.proxies = [...this.proxiesOut.proxies].sort(function (a, b) {
          return a.realIP - b.realIP
        })
      } else {
        this.proxiesOut.proxies = [...this.proxiesOut.proxies].sort(function (a, b) {
          return b.realIP - a.realIP
        })
      }

      this.sortingFlag = !this.sortingFlag;
    },
    sortByCountry() {
      if (this.sortingFlag) {
        this.proxiesOut.proxies = [...this.proxiesOut.proxies].sort(function (a, b) {
          return a.country - b.country
        })
      } else {
        this.proxiesOut.proxies = [...this.proxiesOut.proxies].sort(function (a, b) {
          return b.country - a.country
        })
      }

      this.sortingFlag = !this.sortingFlag;
    },
    sortByRegion() {
      if (this.sortingFlag) {
        this.proxiesOut.proxies = [...this.proxiesOut.proxies].sort(function (a, b) {
          return a.region - b.region
        })
      } else {
        this.proxiesOut.proxies = [...this.proxiesOut.proxies].sort(function (a, b) {
          return b.region - a.region
        })
      }

      this.sortingFlag = !this.sortingFlag;
    },
    sortByCity() {
      if (this.sortingFlag) {
        this.proxiesOut.proxies = [...this.proxiesOut.proxies].sort(function (a, b) {
          return a.city - b.city
        })
      } else {
        this.proxiesOut.proxies = [...this.proxiesOut.proxies].sort(function (a, b) {
          return b.city - a.city
        })
      }

      this.sortingFlag = !this.sortingFlag;
    },
    sortByComment() {
      if (this.sortingFlag) {
        this.proxiesOut.proxies = [...this.proxiesOut.proxies].sort(function (a, b) {
          return a.comment - b.comment
        })
      } else {
        this.proxiesOut.proxies = [...this.proxiesOut.proxies].sort(function (a, b) {
          return b.comment - a.comment
        })
      }

      this.sortingFlag = !this.sortingFlag;
    },
  },
}
</script>

<style scoped>

</style>