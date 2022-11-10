<template>
  <div class="container">
    <h1>Mastodon Mass Follow</h1>
    <div class="row">
      <div class="input-group mb-3">
        <input type="text"
               class="form-control"
               v-model="mastodonHost"
               placeholder="Enter the mastodon host for follows, f.ex. https://mastodon.online, no trailing slashes"
               aria-label="Recipient's username"
               aria-describedby="button-addon2"
               @keyup.enter="resetLoadAccounts()"
        />
        <button @click="resetLoadAccounts()" class="btn btn-outline-secondary" type="button" id="button-addon2">
          Load Accounts
          <span v-if="loading" class="spinner-border spinner-border-sm" role="status"></span>
        </button>
      </div>
    </div>
    <div v-if="accounts.length" class="row gy-2 justify-content-center">
      <div v-for="(account, index) in accounts" :key="'account-' + index" class="col-6 col-sm-3 col-md-2 gx-2">
        <div class="card">
          <img :src="account.avatar" class="card-img-top" alt="...">
          <div class="card-body">
            <h5 class="card-title" v-text="account.acct"></h5>
            <button @click="followAccount(account.id)" href="#" class="btn btn-sm btn-primary">
              Follow<span v-if="accountsLoading[account.id]" class="spinner-border spinner-border-sm" role="status"></span>
            </button>
            <details>
              <summary>Note</summary>
              <p class="card-text" v-html="account.note"></p>
            </details>
          </div>
        </div>
      </div>
    </div>
    <div v-if="accounts.length" class="row my-4">
      <button class="btn btn-primary" @click="loadAccounts()">
        Load more<span v-if="loading" class="spinner-border spinner-border-sm" role="status"></span>
      </button>
    </div>
  </div>
</template>

<script>

export default {
  name: 'MassFollow',
  props: {
    access: {
      type: Object,
      required: true
    },
  },
  data() {
    return {
      offset: 0,
      loading: false,
      mastodonHost: null,
      accountsLoading: {},
      accounts: []
    }
  },
  methods: {
    resetLoadAccounts(){
      this.offset = 0;
      this.loadAccounts();
    },
    followAccount(id){
      this.accountsLoading[id] = true;
      fetch(`${this.access.host}api/v1/accounts/${id}/follow`, {
        method: 'POST',
        headers: new Headers({
          'Content-Type': 'application/json',
          'Authorization': `${this.access.type} ${this.access.token}`
        }),
        body: JSON.stringify({
          grant_type: 'write:follows'
        })
      })
          .then(response => response.json())
          .then((json) => {
            console.log("json", json);
          })
          .catch(() => {})
          .then(() => {
            this.accountsLoading[id] = false;
          })
    },
    loadAccounts(){
      this.loading = true;
      fetch(`${this.mastodonHost}/api/v1/directory.json?offset=${this.offset}&local=true&limit=1000`, {
        headers: new Headers({
          'Authorization': `${this.access.type} ${this.access.token}`
        })
      })
          .then(response => response.json())
          .then((json) => {
            if(this.offset){
              this.accounts = this.accounts.concat(json);
            }else {
              this.accounts = json;
            }
            this.offset++;
          })
          .catch(() => {})
          .then(() => (this.loading = false))
    }
  },
  created() {
    this.mastodonHost = this.access.host;
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
