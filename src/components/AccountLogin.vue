<template>
<div class="container">
  <div class="row my-2 align-items-center">
    <div class="col-2">
      <label class="col-form-label">
        Client Key
      </label>
    </div>
    <div class="col-10">
      <input type="text"
             class="form-control"
             v-model="clientKey"
             placeholder="Retrieve from Application Page"
      />
    </div>
  </div>
  <div class="row my-2 align-items-center">
    <div class="col-2">
      <label class="col-form-label">
        Your Mastodon Host
      </label>
    </div>
    <div class="col-10">
      <input type="text"
             class="form-control"
             v-model="mastodonHost"
             placeholder="Your Mastodon Host"
      />
    </div>
  </div>
  <div class="row">
    <button class="btn btn-primary" @click="redirectToLogin">Load Login</button>
  </div>
</div>
</template>

<script>
export default {
  name: "AccountLogin",
  data(){
    return {
      responseType: 'code',
      scope: 'read write follow',
      redirectUri: 'https://mastodon-mass-follow.netlify.app',
      clientKey: '',
      mastodonHost : 'https://mastodon.online'
    }
  },
  methods: {
    redirectToLogin(){
      const clientId = `client_id=${this.clientKey}`;
      const scope = `scope=${this.scope}`;
      const redirectUri = `redirect_uri=${this.redirectUri}`;
      const responseType = `response_type=${this.responseType}`;
      const params = [clientId, scope, redirectUri, responseType].join('&')
      document.location = encodeURI(`${this.mastodonHost}/oauth/authorize?${params}`);
    }
  }
}
</script>

<style scoped>

</style>