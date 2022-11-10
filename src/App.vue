<template>
  <img alt="Mastodon logo" class="app-logo" src="./assets/logo.png">
  <AccountLogin v-if="!loginPerformed && !access.host && !access.code" @data="setData"/>
  <MassFollow v-else :access="access"/>
</template>

<script>
import MassFollow from "@/components/MassFollow";
import AccountLogin from "@/components/AccountLogin";

export default {
  name: 'App',
  data(){
    return {
      loginPerformed: false,
      access: {
        host: null,
        code: null
      }
    }
  },
  components: {
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
      this.host = host;
      this.code = token;
    },
    tryCode(){
      const params = new Proxy(new URLSearchParams(window.location.search), {
        get: (searchParams, prop) => searchParams.get(prop),
      });
      return params.code || null;
    },
    parseURL(url) {
      let a = document.createElement('a');
      a.href = url;
      if(a.origin){
        return `${a.origin}`;
      }
      return null;
    },
  },
  created() {
    if(this.readCode){
      const host = this.parseURL(window.location);
      if(host){
        this.access.host = host;
        this.access.code = this.readCode;
        this.loginPerformed = true;
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
