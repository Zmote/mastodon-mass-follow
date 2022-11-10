<template>
  <img alt="Mastodon logo" class="app-logo" src="./assets/logo.png">
  <AccountLogin v-if="!access.host && !access.code"/>
  <AccessToken v-if="access.host && access.code" :access="access" @data="setData"/>
  <MassFollow v-if="access.host && access.code && access.token" :access="access"/>
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
        token: null
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
    setData(host, token){
      this.access.token = token;
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
