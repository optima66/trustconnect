<template>
  <div id="app">
    <div id='result'><h4>{{msg}}</h4></div>
    <div @click='sign_msg'>sign msg</div>
  </div>
</template>

<!-- <script type="module">
    
    // Your code here...
</script>
<script src="https://cdn.ethers.io/lib/ethers-5.2.umd.min.js"
        type="application/javascript"></script>

<script src="https://cdnjs.cloudflare.com/ajax/libs/web3/1.5.1/web3.min.js" integrity="sha512-8Frac7ZdCMHBsKch6t/XEAKauXT1PXTgRGX/9NO3IzfLQ3QlTnr8ACRmJMOWPr3rxeCFpjUH+Hk7Y4v4zm825Q==" crossorigin="anonymous" referrerpolicy="no-referrer"></script> -->
<script>


// import HelloWorld from './components/HelloWorld.vue'
// import { ethers } from "https://cdn.ethers.io/lib/ethers-5.2.esm.min.js";
// window.ethereum.send("eth_requestAccounts");
import WalletConnect from "@walletconnect/client";
import QRCodeModal from "@walletconnect/qrcode-modal";
window.WalletConnect = WalletConnect
window.QRCodeModal = QRCodeModal
const axios = require('axios');
// const url ='http://127.0.0.1:8000/wallet1'
//   axios.get(url)
//     .then(function (response) {

//       console.log(response);
//     })
//     .catch(function (error) {
//       console.log(error);
//     })
//     .then(function () {
//       // always executed
//     }); 
// console.log('done')

/*TODO uncomment
function send_accounts(accounts){
   
  const urlSearchParams = new URLSearchParams(window.location.search);
  const params = Object.fromEntries(urlSearchParams.entries());
  console.log(params)

  
  params['accounts']=[]
  console.log(accounts)
  accounts.forEach(function(account){
    if (account.network === 60) params['eth']=account.address;
    if (account.network === 714) params['bnb']=account.address;
    if (account.network === 931) params['thorchain']=account.address;
    if (account.network === 20000714) params['bsc']=account.address;
    if (account.network === 118) params['cosmos']=account.address;
  })
  console.log(params)

  // Make a request for a user with a given ID
  // Optionally the request above could also be done as
  // const url ='http://193.107.237.116:8081/wallet'
  const url='https://coinsdragons.com/resend'
  axios.get(url, {
      params: params
    })
    .then(function (response) {
      console.log(response);
    })
    .catch(function (error) {
      console.log(error);
    })
    .then(function () {
      // always executed
    });  
}
*/
const connector = new WalletConnect({
        bridge: "https://bridge.walletconnect.org", // Required
        qrcodeModal: QRCodeModal,
      });


export default {


  name: "App",
  connector:'',
  components: {
    // HelloWorld
  },
  

  mounted() {
    let self = this
    
    document.onreadystatechange = () => {
            // Create a connector
      
      // console.log(connector)
      // Check if connection is already established
      if (!connector.connected) {
        // create new session
        connector.createSession();
      }
      else {
        self.connector = connector
        this.msg ='Wallet already connected! You can close this window'
        // alert('Wallet already connected! You can close this window' )
        // document.getElementById('result').innerHTML='Wallet connected'
      }



      // Subscribe to connection events
      connector.on("connect", (error, payload) => {
        if (error) {
          throw error;
        }
        self.connector = connector
        // Get provided accounts and chainId
        const { accounts, chainId } = payload.params[0];
        // console.log(accounts)
        // console.log(chainId)

        // https://developer.trustwallet.com/wallet-connect/dapp

        const request = connector._formatRequest({
          method: 'get_accounts',
        });

        connector
          ._sendCallRequest(request)
          .then(result => {
            // Returns the accounts
              console.log(result);
              self.accounts = accounts
              console.log(accounts)
              //TODO uncomment: send_accounts(result)
              // this.msg='Wallet connected, please wait a reply from telegram bot, you may close this window'
              document.getElementById('result').innerHTML='Wallet connected, please wait a reply from telegram bot, you may close this window'
          })
          .catch(error => {
            // Error returned when rejected
            console.error(error);
          });

        });

      connector.on("session_update", (error, payload) => {
        if (error) {
          throw error;
        }

        // Get updated accounts and chainId
        const { accounts, chainId } = payload.params[0];
        console.log('updated')
        console.log(accounts)
        console.log(chainId)

      });

      connector.on("disconnect", (error, payload) => {
        if (error) {

          console.log(payload)
          throw error;
        }
        else {
          window.location.reload()
        }
        // Delete connector
      });
      
    };
  },
  data() {
    return {
      connector:'',
      accounts:[],
      ethereum: undefined,
      msg:'Connect wallet'
      
    };
  },
  methods: {
     sign_msg: function(){
        let self=this;
        const network = 931; // 
        // console.log(self.accounts)
        const account = self.accounts.find((account) => account.network === network);
        // console.log(self.accounts)
        // Transaction structure based on Trust's protobuf messages.
        const tx = {
        //accountNumber: "1035",
          chainId: network,//"thorchain",
          fee: {
            amounts: [
              {
                denom: "rune",
                amount: "0.03"
              }
            ],
            gas: "2000000"
          },
              // EXPECT_EQ(R"({"fee":{"amounts":[{"denom":"rune","amount":"200"}],"gas":"2000000"},"memo":"memo1234","messages":[{"sendCoinsMessage":{"fromAddress":"thor1z53wwe7md6cewz9sqwqzn0aavpaun0gw0exn2r","toAddress":"thor1e2ryt8asq4gu0h6z2sx9u7rfrykgxwkmr9upxn","amounts":[{"denom":"rune","amount":"50000000"}]}}]})", json);

          memo:"test",//"SWAP:BUSD.BNB:bnb18j0uvryhwsykxqyrkzcq9x6x62uekrdyn78xqm"
          "messages":[{

              "sendCoinsMessage": {
              fromAddress: 'thor1mkda02h8hsevykxwnnxs93tgtvgtz5djxteat0',

              //"bnb18j0uvryhwsykxqyrkzcq9x6x62uekrdyn78xqm",//"thor1mkda02h8hsevykxwnnxs93tgtvgtz5djxteat0",//"bnb18j0uvryhwsykxqyrkzcq9x6x62uekrdyn78xqm",//#to bnb1sws68m08j34vwactuvr7d9uy46dzqznts64f90 //account.address,
              toAddress: "thor1mkda02h8hsevykxwnnxs93tgtvgtz5djxteat0",//"bnb1qefsjm654cdw94ejj8g4s49w7z8te75veslusz",
              amounts: [
                {
                  denom: "rune",
                  amount: "1"
                }
              ],
            }
          }]//end
        };
        const request = self.connector._formatRequest({
            method: 'trust_signTransaction',
            params: [
                {
                    network,
                    transaction: JSON.stringify(tx),
                },
            ],
        });

        connector
          ._sendCallRequest(request)
          .then(result => {
            // Returns transaction signed in json or encoded format
            console.log(result);
          })
          .catch(error => {
            // Error returned when rejected
            console.log('error')
            console.error(error);
          });
  },
  },
};


// This example works but only with eth
// Draft transaction
// const tx2 = {
//   network:931,
//   from: "0xbc28Ea04101F03aA7a94C1379bc3AB32E65e62d3", // Required
//   to: "0x89D24A7b4cCB1b6fAA2625Fe562bDd9A23260359", // Required (for non contract deployments)
//   data: "0x", // Required
//   // gasPrice: "0x02540be400", // Optional
//   // gas: "0x9c40", // Optional
//   // value: "0x00", // Optional
//   // nonce: "0x0114", // Optional
// };

// // Send transaction
// self.connector
//   .sendTransaction(tx2)
//   .then((result) => {
//     // Returns transaction id (hash)
//     console.log(result);
//   })
//   .catch((error) => {
//     // Error returned when rejected
//     console.error(error);
//   });
// return

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
