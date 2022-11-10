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
          Client Secret
        </label>
      </div>
      <div class="col-10">
        <input type="text"
               class="form-control"
               v-model="clientSecret"
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
        <span v-text="access.host"></span>
      </div>
    </div>
    <div class="row">
      <button v-if="!tokenVerified" class="btn btn-primary" @click="getAccessToken">
        Get Access Token<span v-if="loading" class="spinner-border spinner-border-sm" role="status"></span>
      </button>
      <button v-else class="btn btn-success" @click="$emit('data', accessToken, tokenType)">
        Continue
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "AccessToken",
  props: {
    access: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      loading: false,
      tokenVerified: false,
      clientSecret: '',
      clientKey: '',
      accessToken: null,
      tokenType: null
    }
  },
  methods: {
    getAccessToken() {
      this.loading = true;
      fetch(`${this.access.host}oauth/token`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          client_id: this.clientKey,
          client_secret: this.clientSecret,
          redirect_uri: 'https://mastodon-mass-follow.netlify.app',
          grant_type: 'authorization_code',
          scope: 'read write follow',
          code: this.access.code
        })
      })
          .then(response => response.json())
          .then((json) => {
            this.accessToken = json.access_token;
            this.tokenType = json.token_type;
          })
          .then(() => {
            if (this.accessToken) {
              return this.verifyToken();
            }
          })
          .catch(() => {
          })
          .then(() => {
            this.loading = false;
          })
    },
    verifyToken() {
      fetch(`${this.access.host}api/v1/apps/verify_credentials`, {
        headers: new Headers({
          'Authorization': `Bearer ${this.accessToken}`
        })
      })
          .then(response => response.json())
          .then((json) => {
            if(json.website === 'https://mastodon-mass-follow.netlify.app'){
              this.tokenVerified = true;
            }
          })
          .catch(() => {
            this.tokenVerified = false;
          })
    }
  }
}
</script>

<style scoped>

</style>