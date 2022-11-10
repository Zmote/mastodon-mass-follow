<template>
  <img alt="Mastodon logo" class="app-logo" src="./assets/logo.png">
  <h1>Mastodon Mass Follow</h1>
  <div class="row mt-4">
    <p>First Create an Application from Preferences > Development > New Application, use this Webpage as Application
      URL and Redirection URL</p>
  </div>
  <AccountLogin v-if="!access.host && !access.code" @host="setHost"/>
  <AccessToken v-if="access.host && access.code && !access.token" :access="access" @data="setData"/>
  <MassFollow v-if="access.host && access.code && access.token && access.type" :access="access"/>
</template>

<script>
import MassFollow from "@/components/MassFollow";
import AccountLogin from "@/components/AccountLogin";
import AccessToken from "@/components/AccessToken";

export default {
  name: 'App',
  data(){
    return {
      tokenAccessed: null,
      access: {
        host: null,
        code: null,
        token: null,
        type: null
      }
    }
  },
  components: {
    AccessToken,
    AccountLogin,
    MassFollow
  },
  computed: {
    readCode(){
      return this.tryCode();
    }
  },
  methods: {
    setHost(host){
      this.access.host = host;
    },
    setData(token, type){
      this.access.token = token;
      this.access.type = type;
    },
    tryCode(){
      const params = new Proxy(new URLSearchParams(window.location.search), {
        get: (searchParams, prop) => searchParams.get(prop),
      });
      return params.code || null;
    }
  },
  created() {
    if(this.readCode){
      if(document.referrer){
        this.access.host = document.referrer;
        this.access.code = this.readCode;
      }
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 1rem;
}

.app-logo{
  width: 100px;
  height: auto;
}
</style>
