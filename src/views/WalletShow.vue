<template>
  <div>
    <v-layout align-center justify-center column>
      <v-flex xs12>
        <Avarkey v-bind:address="address" width="135" height="135"/>
        <v-card-text>
          <span class="title">Account Name</span>
        </v-card-text>
        <v-card-text>
          <span class="subheading">{{ address }}</span>
        </v-card-text>
        <v-card-actions>
          <v-layout row wrap justify-space-between>
            <v-btn xs6 block @click="withdraw(address, 'ETH')"><font-awesome-icon :icon="ethereum"/>&nbsp;Send</v-btn>
            <v-btn xs6 block @click="show(true)"><font-awesome-icon :icon="qrcode"/>&nbsp;Receive</v-btn>
            <v-btn xs6 block @click="show(false)"><font-awesome-icon :icon="pkey"/>&nbsp;Privatekey</v-btn>
            <v-btn xs6 block @click="addToken(20)">+ ERC20</v-btn>
            <v-btn xs6 block @click="addToken(721)">+ ERC721</v-btn>
          </v-layout>
        </v-card-actions>
      </v-flex>
    </v-layout>
    <v-layout align-center justify-center row>
      <v-flex xs12>
        <TokenList ref="tokenList" />
      </v-flex>
    </v-layout>
    <b-modal hide-footer hide-header ref="modalRef">
      <component v-bind:is="modalBody"></component>
    </b-modal>
  </div>
</template>

<script>
import TokenList from '@/components/TokenList.vue'
import DepositQrcode from '@/components/DepositQrcode.vue'
import ExportPrivatekey from '@/components/ExportPrivatekey.vue'
import RegisterToken from '@/components/RegisterToken.vue'
// import Withdraw from '@/components/Withdraw.vue'
import Avarkey from '@/components/Avarkey.vue'
import { faEthereum } from '@fortawesome/free-brands-svg-icons'
import { faQrcode } from '@fortawesome/free-solid-svg-icons'
import { faKey } from '@fortawesome/free-solid-svg-icons'

export default {
  props: {
    wallet: {
      type: Object,
      required: true
    }
  },
  components: {
    TokenList,
    DepositQrcode,
    ExportPrivatekey,
    RegisterToken,
    Avarkey
  },
  data() {
    return {
      address: window.wallet.account.address(),
      modalBody: null
    }
  },
  mounted() {
    this.$on('update',(r) => {
      if (r) this.$refs.tokenList.update()
    })
    this.$refs.tokenList.update()
  },
  methods: {
    show : function (isQR) {
      this.modalBody = isQR?DepositQrcode:ExportPrivatekey
      this.$refs.modalRef.show()
      if(this.modalBody.resetData)
        this.modalBody.resetData()
    },
    withdraw: function (address, name) {
      this.$router.push({path:'/withdraw?t='+address+'&n='+name})
      // this.modalBody = Withdraw
      // this.$refs.modalRef.show()
      // if(this.modalBody.resetData)
      //   this.modalBody.resetData()
    },
    addToken : function(erc){
      switch (erc) {
        case 20:
        case 721:
        this.modalBody = RegisterToken
        this.$refs.modalRef.show()
        if(this.modalBody.resetData)
          this.modalBody.resetData(erc)          
          break;
        default:
          break;
      }
    }
  },
  computed: {
    qrcode () {
      return faQrcode;
    },
    ethereum() {
      return faEthereum;
    },
    pkey() {
      return faKey;
    }
  }
}
</script>

<style scoped>
.location {
  margin-bottom: 0;
}
.location > .icon {
  margin-left: 10px;
}
.wallet-header > .title {
  margin: 0;
}
.list-group {
  margin: 0;
  padding: 0;
  list-style: none;
}
.list-group > .list-item {
  padding: 1em 0;
  border-bottom: solid 1px #e5e5e5;
}
</style>
