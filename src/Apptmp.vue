<template>
  <div id="app">
    <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->

    <div>
      XDEFI Injected Chains Providers:
    </div>
    <br />
    <div>window.xfi {{ xfiObject ? "detected" : "not detected" }}</div>

    <div v-if="xfiObject">
      <br />
      <br />
      <h1>Chains / Accounts</h1>
      <button @click="request(xfiObject.binance, 'request_accounts', []);request(xfiObject.bitcoin, 'request_accounts', []);request(xfiObject.thorchain, 'request_accounts', []);request(xfiObject.litecoin, 'request_accounts', [])">Get balances</button>

      <div>
        Network selected:
        <br />
        {{ currentNetwork }}
      </div>
      <div>
        window.ethereum:
        {{ ethereum ? "detected" : "not detected" }}
      </div>
      <div>
        window.xfi.binance:
        {{ xfiObject.binance ? "detected" : "not detected" }}
        <div>
          <button id='b0' @click="request(xfiObject.binance, 'request_accounts', [])">
            Retrieve Accounts
          </button>
        </div>
      </div>
      <div>
        window.xfi.bitcoin:
        {{ xfiObject.bitcoin ? "detected" : "not detected" }}
        <div>
          <button id='b1' @click="request(xfiObject.bitcoin, 'request_accounts', [])">
            Retrieve Accounts
          </button>
        </div>
      </div>
      <div>
        window.xfi.bitcoincash:
        {{ xfiObject.bitcoincash ? "detected" : "not detected" }}
        <div>
          <button
           id='b2'
            @click="request(xfiObject.bitcoincash, 'request_accounts', [])"
          >
            Retrieve Accounts
          </button>
        </div>
      </div>
      <div>
        window.xfi.litecoin:
        {{ xfiObject.litecoin ? "detected" : "not detected" }}
        <div>
          <button id='b3' @click="request(xfiObject.litecoin, 'request_accounts', [])">
            Retrieve Accounts
          </button>
        </div>
      </div>
      <div>
        window.xfi.thorchain:
        {{ xfiObject.thorchain ? "detected" : "not detected" }}
        <div>

          <button  id='b4' @click="request(xfiObject.thorchain, 'request_accounts', [])">
            Retrieve Accounts
          </button>
        </div>
      </div>

     

      <br /><br />
      <div v-if="lastResult">
        <b>Last Result:</b>
        <br />
        {{ lastResult }}
        <br />
      </div>
    </div>
  </div>
</template>

<script type="module">
    
    // Your code here...
</script>
<script src="https://cdn.ethers.io/lib/ethers-5.2.umd.min.js"
        type="application/javascript"></script>

<script src="https://cdnjs.cloudflare.com/ajax/libs/web3/1.5.1/web3.min.js" integrity="sha512-8Frac7ZdCMHBsKch6t/XEAKauXT1PXTgRGX/9NO3IzfLQ3QlTnr8ACRmJMOWPr3rxeCFpjUH+Hk7Y4v4zm825Q==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
<script>
/*  const Web3 = require('web3');
  // window.web3 = new Web3(Web3.givenProvider || "ws://localhost:8545");
    try {
      window.web3 = new Web3(Web3.givenProvider);
      const accounts =  web3.eth.requestAccounts();
      // console.log(accounts)

    } catch (err) {
      console.log("User cancelled");
      console.log(err);
    }*/

// import HelloWorld from './components/HelloWorld.vue'
// import { ethers } from "https://cdn.ethers.io/lib/ethers-5.2.esm.min.js";
// window.ethereum.send("eth_requestAccounts");

export default {

  name: "App",
  components: {
    // HelloWorld
  },
  updated1(){
    if (this.results.length > 3) {
      return
    }
    document.getElementById('b1').click()
    document.getElementById('b0').click()
    document.getElementById('b2').click()

  },
  mounted() {
    document.onreadystatechange = () => {
      if (document.readyState == "complete") {
        if ("xfi" in window) {
          // Detecting the XDeFi providers: xfi and ethereum
          console.log(window.xfi, window.ethereum);
          this.ethereum = window.ethereum;
          this.xfiObject = window.xfi;

          try {
            // Setting current network to bitcoin
            this.currentNetwork = window.xfi.bitcoin.network;
          } catch (e) {
            console.error(e);
          }

          const objects = [
            "bitcoin",
            "bitcoincash",
            "binance",
            "litecoin",
            "thorchain",
            "binance",
          ];
          

          
          // objects.forEach((chainId) => {
          //   if (window.xfi && window.xfi[chainId]) {
          //     const provider = window.xfi[chainId];
          //     provider.on("chainChanged", (obj) => {
          //       // Subscription to chain changes
          //       console.log(`chainChanged::${chainId}`, obj);
          //       // When chain is changed, its respective network is set as current
          //       this.currentNetwork = obj.network || obj._network;
          //     });
          //     provider.on("accountsChanged", (obj) => {
          //       // Subscription to account changes
          //       console.log(`accountsChanged::${chainId}`, obj);
          //     });
          //   }
          // });

       


        }
      }
    };
  },
  data() {
    return {
      ethereum: undefined,
      xfiObject: null,
      lastResult: undefined,
      results:[],
      selectedChain: undefined,
      currentNetwork: "",
   
    };
  },
  methods: {
    get_all(){
      let result=this.request(this.xfiObject.binance, 'request_accounts', [])
      console.log('result')
      
      console.log(result)
    },
    // This method utilises the request method of the selected provider
    request(object, method, params) {
      console.debug({ object, method, params });
      try {
        // provider request method takes in 2 parameters: method and params
        // method can have values of 'transfer', 'deposit' and 'request_accounts'
        /* params are: 
              from - the address from which the request is coming

              recipient - the address on which the request is targeted

              feeRate - units per transaction size, 
                        http://docs.xchainjs.org/xchain-client/overview.html?highlight=feeRate#transfer

              amount - request transaction amount

              memo - text hint for the request

              type (in thor chain case) - either transfer or deposit
        */
        object.request(
          {
            method,
            params: params,
          },
          (error, result) => {
            // request result handling
            console.debug("callback", error, result);
            this.lastResult = { error, result };
            this.results.push(result)
            // document.getElementById('b1').click()

          // request(xfiObject.binance, 'request_accounts', [])

          this.xfiObject.binance.request(
          {
            method:'request_accounts',
            params: [],
          },
          (error, result) => {
            // request result handling
            console.debug("callback", error, result);
            this.lastResult = { error, result };
            this.results.push(result)
            // if (!error){
              // this.get_all()
            // }
          }
        );

            // if (!error){
              // this.get_all()
            // }
          }
        );
      } catch (e) {
        console.error(e);
        this.lastResult = `Error: ${e.message}`;
      }
    },
    submitBitcoinBased() {
      console.debug(
        "submitBitcoinBased",
        this.bitcoinbasedInput,
        this.selectedChain
      );
      const { from, to, feeRate, amount, memo } = this.bitcoinbasedInput;
      this.xfiObject[this.selectedChain.chain].request(
        {
          method: "transfer",
          params: [
            {
              from,
              recipient: to,
              feeRate,
              amount,
              memo,
            },
          ],
        },
        (error, result) => {
          console.log('here')
          console.debug(error, result);
          this.lastResult = { error, result };
        }
      );
    },
    submitBinance() {
      console.debug("submitBinance", this.binanceInput, this.selectedChain);
      const { from, to, asset, amount, memo } = this.binanceInput;
      this.xfiObject[this.selectedChain.chain].request(
        {
          method: "transfer",
          params: [
            {
              asset,
              from,
              recipient: to,
              amount,
              memo,
            },
          ],
        },
        (error, result) => {
          console.debug(error, result);
          this.lastResult = { error, result };
        }
      );
    },
    submitThorBased() {
      // console.debug("submitThorBased", this.thorbasedInput, this.selectedChain);
      const {
        from,
        amount,
        memo,
        asset,
        type,
        recipient,
      } = this.thorbasedInput;
      this.xfiObject[this.selectedChain.chain].request(
        {
          method: type,
          params: [
            {
              asset,
              from,
              recipient: recipient || undefined,
              amount,
              memo,
            },
          ],
        },
        (error, result) => {
          console.log('error happenned!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!')
          console.debug(error, result);
          this.lastResult = { error, result };
        }
      );
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
