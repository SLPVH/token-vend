<template>
  <div class="invoice">
    <h1>Scan To Pay</h1>
    <div class="qr" v-if="invoice">
      <qrcode-vue :value="invoice" size="350" level="H"></qrcode-vue>
    </div>
  </div>
</template>

<script>
import QrcodeVue from 'qrcode.vue'
import axios from "axios"

export default {
  name: 'invoice',
  components: {
    QrcodeVue
  },  
  methods: {
    vend() {
      axios.post(`http://${process.env.VUE_APP_IOZETA_VEND_IP}/add/pay`, 'amt=1.00', {
        auth: {
          username: process.env.VUE_APP_IOZETA_VEND_USER,
          password: process.env.VUE_APP_IOZETA_VEND_PASS
        }
      }).then(res => {
        console.log(res)
      }).catch(err => {
        console.log(err)
      })
    }
  },
  data: function () {
    return {
      invoice: process.env.VUE_APP_SLP_ADDRESS,
    }
  },
  mounted: function () {
    let _this = this

    // setup bitsocket to monitor transactions on the BCH address
    let query = {
      "v": 3,
      "q": {
        "find": {"out.e.a":process.env.VUE_APP_BCH_ADDRESS}
      }
    }
 
    this.bitsocket = new EventSource('https://bitsocket.bch.sx/s/'+btoa(JSON.stringify(query)))
    this.bitsocket.onmessage = function(event) {
      let res = JSON.parse(event.data)
      if (res.type == "open") console.log("BITSOCKET OPEN")
      if (res.type != "mempool") return

      res.data.forEach(item => {
        // query for slpdb
        let query = {
          "v": 3,
          "q": {
            "find": {"tx.h":item.tx.h}
          }
        }

        // fetch the parsed tx details
        // TODO: It would be best to use a SLP socket query db that already includes the parsed SLP data.
        //       I tried using https://developer.bitcoin.com/slp/docs/js/socket but couldn't get it working. It looks like the right solution.
        axios.get('https://slpdb.fountainhead.cash/q/'+btoa(JSON.stringify(query)))
        .then(res => {
          if (res.data && res.data.u && res.data.u[0] && res.data.u[0].slp) {
            // we have SLP token
            let slp = res.data.u[0].slp
            if (slp.valid && slp.detail.symbol == process.env.VUE_APP_SLP_SYMBOL) {
              // TODO: check all the amounts and see they total 1
              _this.vend()
              _this.$router.push('thanks')
            }
          }
        }).catch(err => {
          console.log(err)
        })
      })
    }    
  },
  beforeDestroy: function () {
    this.bitsocket.close()
    console.log("BITSOCKET CLOSED")
  }
}
</script>

<style>
.qr {
  margin: 25px;
}
</style>
