<template>
  <section class="panelWrap">
    <div class="box">
      <div class="left">
        {{ $metamask.toDecimals($metamask.fromToken(balance, decimals)) }}
        {{ symbol }}
      </div>
      <div class="right address" @click="handleClick">
        <span v-if="hasPending" class="iconfont icon-icon_loading"></span>
        {{ $metamask.hideAddress(address) }}
      </div>
    </div>
    <div class="dialogWrap" @click.self="handleClick" v-show="dialogVisible">
      <section class="dialogBox">
        <!-- address -->
        <div class="dialogAddressBox">
          <a
            class="address"
            :href="`${explorer}/address/${address}`"
            target="_blank"
          >
            {{ $metamask.hideAddress(address) }}
          </a>
        </div>
        <!-- list title -->
        <div class="listTitleBox">
          <p class="title">
            {{ $i18n.locale === "en" ? "Recent Transactions" : "最近的交易" }}
          </p>
          <p class="clear" @click="handleClear">
            {{ $i18n.locale === "en" ? "clear all" : "清除所有" }}
          </p>
        </div>
        <!-- list -->
        <ul class="listBox" v-if="list.length">
          <li v-for="(item, index) in list" :key="index">
            <a
              class="hash"
              :href="`${explorer}/tx/${item.txHash}`"
              target="_blank"
            >
              {{ $metamask.hideAddress(item.txHash) }}
            </a>
            <p
              v-if="item.status === 'pending'"
              class="status iconfont icon-icon_loading"
            ></p>
            <p
              v-if="item.status === 'success'"
              class="status iconfont icon-icon_success"
            ></p>
            <p
              v-if="item.status === 'failed'"
              class="status iconfont icon-icon_failed"
            ></p>
          </li>
        </ul>
        <p class="nodata" v-else>NO DATA</p>
      </section>
    </div>
  </section>
</template>

<script>
import { CHAINS_INFO } from "../../constant";
export default {
  name: "aometamask", // 这里需要注意下，我们是采用的全局注入我们的组件，所以在后面因为我们的组件后，会直接使用这个命名的标签
  props: ["browserurl", "tokenInfo"],
  data() {
    return {
      chainId: "",
      address: "",
      balance: "",
      explorer: "",
      symbol: "",
      decimals: "",
      dialogVisible: false,
      list: [],
      hasPending: false,
    };
  },
  watch: {
    browserurl() {
      this.getBrowserUrl();
    },
  },
  components: {},
  created() {
    if (!this.$i18n || !this.$i18n.locale) {
      this.$i18n = {};
      this.$set(this.$i18n, "locale", "zh");
    }
    // 获取地址
    this.$metamask.connect().then((accounts) => {
      this.address = accounts[0];
      // 获取symbol和decimals
      this.getTokenInfo();

      // 获取余额
      this.$metamask.getBalance(this.address).then((balance) => {
        console.log("balance", balance);
        this.balance = balance;
      });
      // 获取记录
      this.$metamask.getTxRecord().then((list) => {
        this.list = list;
      });
      // 获取区块浏览器URL
      this.getBrowserUrl();
      this.$metamask.onChainChanged(() => {
        this.getBrowserUrl();
      });

      // pending list
      this.$metamask.getPendingTxRecord().then((list) => {
        this.hasPending = list.length ? true : false;
      });

      this.$metamask.onTxStatusChanged((list) => {
        console.log("--- onTxStatusChanged ---");
        this.list = list;
        this.$metamask.getPendingTxRecord().then((list) => {
          this.hasPending = list.length ? true : false;
        });
      });
    });
  },
  methods: {
    handleClick() {
      this.dialogVisible = !this.dialogVisible;
    },
    handleClear() {
      this.$metamask.clearTxRecord();
    },
    getTokenInfo() {
      if (this.tokenInfo && this.tokenInfo.symbol && this.tokenInfo.decimals) {
        this.symbol = this.tokenInfo.symbol;
        this.decimals = this.tokenInfo.decimals;
      } else {
        this.$metamask.getChainId().then((chainId) => {
          this.chainId = chainId;
          if (Object.keys(CHAINS_INFO).includes(chainId)) {
            this.symbol = CHAINS_INFO[chainId].symbol;
            this.decimals = CHAINS_INFO[chainId].decimals;
          } else {
            if (
              this.tokenInfo &&
              this.tokenInfo.symbol &&
              this.tokenInfo.decimals
            ) {
              this.symbol = this.tokenInfo.symbol;
              this.decimals = this.tokenInfo.decimals;
            } else {
              console.error("缺少tokenInfo信息");
            }
          }
        });
      }
    },
    getBrowserUrl() {
      if (this.browserurl) {
        this.explorer = this.browserurl;
      } else {
        this.$metamask.getChainId().then((chainId) => {
          this.chainId = chainId;
          if (Object.keys(CHAINS_INFO).includes(chainId)) {
            this.explorer = CHAINS_INFO[chainId].explorer;
          } else {
            if (this.browserurl) {
              this.explorer = this.browserurl;
            } else {
              console.error("缺少browserurl信息");
            }
          }
        });
      }
    },
  },
};
</script>
<style scoped>
.panelWrap {
  display: inline-block;
}
.panelWrap .address {
  cursor: pointer;
}
.panelWrap p {
  margin: 0;
}
.panelWrap > .box {
  display: flex;
  border-radius: 5px;
  overflow: hidden;
}
.panelWrap > .box > div {
  padding: 0 10px;
  color: #fff;
  line-height: 30px;
}
.panelWrap > .box > div.left {
  background-color: #496fa0;
  text-transform: uppercase;
}
.panelWrap > .box > div.right {
  background-color: #2a2a3f;
}
.panelWrap > .box > div.right span {
  display: inline-block;
}
</style>
<style scoped>
.panelWrap .dialogWrap {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.75);
}
.panelWrap .dialogWrap > .dialogBox {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 80%;
  max-width: 600px;
  transform: translateX(-50%) translateY(-50%);
  background-color: #fff;
  border-radius: 3px;
  text-align: left;
  line-height: 1;
  box-sizing: border-box;
}
.panelWrap .dialogWrap > .dialogBox .dialogAddressBox {
  padding: 30px 15px 20px;
}
.panelWrap .dialogWrap > .dialogBox .dialogAddressBox .address {
  color: #000;
  font-size: 20px;
}
.panelWrap .dialogWrap > .dialogBox .listTitleBox {
  display: flex;
  justify-content: space-between;
  padding: 15px 15px 0;
  background-color: #f7f8fa;
}
.panelWrap .dialogWrap > .dialogBox .listTitleBox .title {
  font-size: 14px;
  color: #606266;
}
.panelWrap .dialogWrap > .dialogBox .listTitleBox .clear {
  font-size: 14px;
  color: #04c19e;
  cursor: pointer;
}
.panelWrap .dialogWrap > .dialogBox .listBox {
  margin: 0;
  padding: 15px;
  list-style: none;
  background-color: #f7f8fa;
}

.panelWrap .dialogWrap > .dialogBox .listBox li {
  display: flex;
  justify-content: space-between;
  height: 40px;
  line-height: 40px;
}
.panelWrap .dialogWrap > .dialogBox .nodata {
  padding: 15px;
  font-size: 14px;
  text-align: center;
  background-color: #f7f8fa;
}
</style>
<style scoped>
@font-face {
  font-family: "iconfont"; /* Project id  */
  src: url("./iconfont.ttf?t=1623317988312") format("truetype");
}
@keyframes animate {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

.iconfont {
  font-family: "iconfont" !important;
  font-size: 16px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
.icon-icon_loading {
  animation: animate 2s linear infinite;
}
.icon-icon_loading:before {
  content: "\e64a";
  color: #409eff;
}

.icon-icon_failed:before {
  content: "\e65d";
  color: #f56c6c;
}

.icon-icon_success:before {
  content: "\e65f";
  color: #67c23a;
}
</style>
