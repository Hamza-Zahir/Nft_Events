<template>
  <div>
    <div class="container">
      <div class="graph">
        <div v-if="!dates.length" class="loading">
          <span>Loading ...</span>
        </div>
        <GraphBar v-if="!dates.length" :dates="dates_" :counts="amounts_" />
        <GraphBar v-if="dates.length" :dates="dates" :counts="amounts" />
      </div>

      <div class="graph">
        <div v-if="!dates.length" class="loading">
          <span>Loading ...</span>
        </div>

        <GraphLine v-if="!dates.length" :dates="dates_" :counts="amounts_" />
        <GraphLine v-if="dates.length" :dates="dates" :counts="amounts" />
      </div>
    </div>
  </div>
</template>

<script>
import GraphBar from "./graphBar.vue";
import GraphLine from "./graphLine.vue";
import CcontractAbi from "../json/ContractABI.json";
import Web3 from "web3";
const contractAddres = "0xeF881176e420e846A02C768e2B0ccC0e54F2Cf50";
import big from "big.js";
// ..................................
// import { ethers } from "ethers";

// import eventesMint from "../json/eventesMint.json";
// import eventsData from "../json/eventsData.json";
const dates_ = [
  "2022/08/21",
  "2022/08/22",
  "2022/08/23",
  "2022/08/24",
  "2022/08/25",
  "2022/08/26",
  "2022/08/27",
  "2022/08/28",
  "2022/08/29",
  "2022/08/30",
  "2022/08/31",
  "2022/09/01",
  "2022/09/02",
  "2022/09/03",
  "2022/09/04",
  "2022/09/05",
  "2022/09/06",
  "2022/09/07",
  "2022/09/08",
  "2022/09/09",
];
const amounts_ = [
  150, 1350, 555900, 350900, 109450, 122050, 165300, 77600, 49700, 32800, 90500,
  31350, 40550, 16350, 23350, 24700, 15000, 6350, 30950, 2200,
];

export default {
  components: { GraphBar, GraphLine },
  data() {
    return {
      dates_,
      amounts_,
      dates: [],
      amounts: [],
      data: {},
    };
  },
  methods: {
    async fetchData() {
      // const key = "ZKICSRwI2Ouce2zraXRx435tvMdMiFWKhiogMh84";

      const web3 = new Web3(
        `wss://magical-serene-lambo.bsc.discover.quiknode.pro/3bd52722522dfcfadd38b09af38c2986af661ab4/`
      );
      const NFTContract1 = new web3.eth.Contract(CcontractAbi, contractAddres);

      let latestBlock = await web3.eth.getBlock("latest");
      latestBlock = latestBlock.number;

      const firstblock = 20582647;

      for (
        let i = latestBlock;
        i >= firstblock;
        i = Number(big(i).minus(5000).toString())
      ) {
        const from = Number(big(i).minus(5000).toString());
        const to = i - 1;

        try {
          await NFTContract1.getPastEvents(
            "Transfer",
            {
              fromBlock: from,
              toBlock: to,
            },
            async (error, events) => {
              if (events && events.length) {
                await this.generaitEvents(events);
              }
            }
          );
        } catch (error) {
          console.log(error);
        }
      }
    },

    async formatDate(timestamp) {
      let date = new Date(timestamp);

      date = `${date.getFullYear()}/${
        date.getMonth().toString().length == 1 ? "0" : ""
      }${date.getMonth() + 1}/${
        date.getDate().toString().length == 1 ? "0" : ""
      }${date.getDate()}`;

      return date;
    },

    async generaitEvents(_events) {
      // var url = 'https://magical-serene-lambo.bsc.discover.quiknode.pro/3bd52722522dfcfadd38b09af38c2986af661ab4/';
      // var url = 'https://bsc-dataseed.binance.org';
      // var url = 'https://eth.getblock.io/mainnet/e9a50e8e-5454-494d-9737-34b27705f40c';
      // var url = "wss://eth.getblock.io/mainnet/?e9a50e8e-5454-494d-9737-34b27705f40c"
      // var web3 = new ethers.providers.JsonRpcProvider(url);
      // const provider = new ethers.providers.HttpProvider("https://eth.getblock.io/mainnet/e9a50e8e-5454-494d-9737-34b27705f40c");
      const ethereum = window.ethereum;
      const web3 = new Web3(Web3.givenProvider || ethereum);
      try {
        await _events.map(async (e) => {
          const _amount = 1 * 50;
          let tx = await web3.eth.getBlock(e.blockNumber);
          let _date = await this.formatDate(tx.timestamp * 1000);

          if (!this.data[_date]) {
            this.data[_date] = _amount;
          } else {
            this.data[_date] = this.data[_date] + _amount;
          }
        });
      } catch (error) {}
    },
    async manageEvents() {
      await this.fetchData().then(() => {
        this.dates = [];
        this.amounts = [];
        const keys = Object.keys(this.data).sort();
        keys.map((key) => {
          this.dates.push(key);
          this.amounts.push(this.data[key]);
        });
      });
    },
  },
  mounted() {
    this.manageEvents();
  },
};
</script>

<style>
body {
  background: #050d352c;
}
.container {
  max-width: 770px;
  margin: 20px auto;
}
.graph {
  margin: 40px 0;
  border: 1px solid;
  border-color: transparent transparent slategray slategray;
  padding: 10px;
  position: relative;
}
.graph .loading {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -80%);
  width: 120px;
  height: 120px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.graph .loading ::before {
  content: "";
  position: absolute;
  top: 0%;
  left: 0%;
  width: calc(100% - 14px);
  height: calc(100% - 14px);
  border: 7px solid;
  border-color: #70809085 #70809085 #70809085 #70809042;
  border-radius: 50%;
  box-shadow: 0px 0px 7px 0px #646464b9 inset;
  animation: 2s loading infinite linear;
}
@keyframes loading {
  to {
    transform: rotate(360deg);
  }
}
</style>
