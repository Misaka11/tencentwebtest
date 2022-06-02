<template>
  <div id="app">
    <a id="LoginWithAmazon">
      <img
        border="0"
        alt="Login with Amazon"
        src="https://images-na.ssl-images-amazon.com/images/G/01/lwa/btnLWA_gold_156x32.png"
        width="156"
        height="32"
      />
    </a>
    <br />
    <a id="Logout">Logout</a>
    <br />
    {{ rdata }}
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "App",
  components: {
    HelloWorld,
  },
  data() {
    return {
      rdata: "no data",
    };
  },
  created() {
    window.onAmazonLoginReady = function () {
      amazon.Login.setClientId(
        "amzn1.application-oa2-client.292775ca65044ae69cae9783da4848b5"
      );
      console.log("hello, im ready");
      document.getElementById("LoginWithAmazon").onclick = function () {
        this.rdata = "click on LoginWithAmazon";
        setTimeout(window.doLogin, 1);
        return false;
      };
      window.doLogin = function () {
        var options = {};
        options.scope = "profile";
        options.pkce = true;
        amazon.Login.authorize(options, function (response) {
          if (response.error) {
            alert("oauth error " + response.error);
            return;
          }
          amazon.Login.retrieveToken(response.code, function (response) {
            if (response.error) {
              alert("oauth error " + response.error);
              return;
            }
            amazon.Login.retrieveProfile(
              response.access_token,
              function (response) {
                alert("Hello, " + response.profile.Name);
                alert(
                  "Your e-mail address is " + response.profile.PrimaryEmail
                );
                alert("Your unique ID is " + response.profile.CustomerId);
                if (window.console && window.console.log)
                  window.console.log(response);
              }
            );
          });
        });
      };
      document.getElementById("Logout").onclick = function () {
        amazon.Login.logout();
      };
    };
    (function (d) {
      var a = d.createElement("script");
      a.type = "text/javascript";
      a.async = true;
      a.id = "amazon-login-sdk";
      a.src = "https://assets.loginwithamazon.com/sdk/na/login1.js";
      d.getElementById("amazon-root").appendChild(a);
    })(document);
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
