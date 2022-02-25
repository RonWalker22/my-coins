<template>
  <div v-if="email !== undefined">
    <h1>{{ msg }}</h1>
    <body>
        <a href="https://my-coins.auth.us-east-2.amazoncognito.com/login?client_id=n2l1crskuigpqp3lrbsj8r7nj&response_type=token&scope=email+openid&redirect_uri=http://localhost:8080">Login</a>
        <div>
            <div></div>
            <input v-model="newCoin.name" placeholder="coin name">
            <input v-model="newCoin.amount" type="text" placeholder="amount">
            <input @click="updateCoin" type="button" value="Update Portfolio">
        </div>

        <div class="container">
            <table>
                <thead>
                    <tr>
                        <th>Coin</th>
                        <th>Amount</th>
                        <th>Price</th>
                        <th>Value</th>
                    </tr>
                </thead>
                <tbody v-if="state === 'ready'">
                    <tr v-for="(coin, i) in coins" :key="coin.name">
                        <td>{{i}}</td>
                        <td>{{coin.amount}}</td>
                        <td>${{coin.price.toFixed(2)}}</td>
                        <td>${{coin.value.toFixed(2)}}</td>
                    </tr>
                </tbody>
            </table>  
            <div v-if="state === 'loading'">
                   <p>loading data...</p>
            </div>
        </div>
    </body>
  </div>
  <div v-else>
              <a href="https://my-coins.auth.us-east-2.amazoncognito.com/login?client_id=n2l1crskuigpqp3lrbsj8r7nj&response_type=token&scope=email+openid&redirect_uri=https://main.d1cm820nfbsmxw.amplifyapp.com/">Login</a>
  </div>
</template>

<script>
// https://my-coins.auth.us-east-2.amazoncognito.com/login?client_id=n2l1crskuigpqp3lrbsj8r7nj&response_type=token&scope=email+openid&redirect_uri=http://localhost:8080/
import axios from 'axios';

export default {
  name: 'MyPortfolio',
  props: {
    msg: String,
    auth: String,
    email: String
  },

  data: function() {
      return {
          coins: [],
          state: "ready",
          newCoin: {
              name: null,
              amount: null,
          },
          userEmail: "",
          url: `https://t3d210uhn7.execute-api.us-east-2.amazonaws.com/test/portfolio?emailId=${this.email}`
      }
  },
  mounted () {
      this.state = "loading"
      this.getEmail();
      this.getCoin();
  },
  methods: {
    getCoin() {
      this.state = "loading"
      axios
        .get(this.url,
        {headers: {
            "Authorization": this.auth
        }})
        .then(response => {
            this.coins = response.data.coins
            this.state = "ready"
        }
        )
    },
    getEmail() {
        if (location.href.split('#').length === 1) {
            return null;
        } 
        var idTokenWithEquals = (location.href.split('#')[1]).split('&')[0];
        var COGNITO_ID_TOKEN = idTokenWithEquals.split('=')[1];
        COGNITO_ID_TOKEN = this.parseJwt(COGNITO_ID_TOKEN);

        this.userEmail = COGNITO_ID_TOKEN.email 

    },
    parseJwt(token) {
        var base64Url = token.split('.')[1];
        var base64 = base64Url.replace(/-/g, '+').replace(/_/g, '/');
        var jsonPayload = decodeURIComponent(atob(base64).split('').map(function(c) {
            return '%' + ('00' + c.charCodeAt(0).toString(16)).slice(-2);
        }).join(''));

        return JSON.parse(jsonPayload);
    },

    updateCoin() {
        this.state = "loading";
        if (this.newCoin.name === null || this.newCoin.amount === null) {
            console.log("empty updatecoin");
            return;
        }
        let extraParams = `&coinId=${this.newCoin.name}&amount=${this.newCoin.amount}`
        axios.put(this.url + extraParams, {}, {
        headers: {
            'Authorization': this.auth,
        }
        }).then((res) => {
            try {
                // this.state = "ready";
                // alert("API offline: UPDATE");
                // this.
                this.info = res.data;
                this.getCoin();
                // this.coins = res.data.coins;
            } catch (error) { 
                // throw error;
                alert("API offline: UPDATE");
                this.state = "ready";
            }
            
        })
   },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import url('~simpledotcss/simple.min.css');

.margin-standard {
    margin-right: 20px;
}
</style>
