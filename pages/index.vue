<template>
  <div>
    <main>
      <section class="py-5 text-center container">
        <div class="row py-lg-5">
          <div class="col-lg-6 col-md-8 mx-auto">
            <h1 class="fw-normal">
              {{$t('CLAIM_CREDENTIALS')}}
            </h1>
            <p class="lead text-muted fw-normal mb-4">
              {{$t('SELECT_CREDENTIALS_MSG')}}
            </p>
              <form>
                <div class="d-flex flex-column align-items-md-center align-items-sm-start text-start">
                  <div class="form-check col-md-9 col-sm-12 mb-3" v-for="issuable in issuables.credentials" :key="issuable.credentialData.title">
                    <input class="form-check-input me-4" type="checkbox" :id="'issuable-' + issuable.credentialData.title" :name="'issuable-' + issuable.credentialData.title" :value="issuable.credentialData.title" v-model="checkedCredentials">
                    <label class="form-check-label">{{/*issuable.credentialData.title*/}}{{issuable.credentialData.title}}</label>
                    <!--<button type="button" data-bs-toggle="modal" :data-bs-target="'#credentilModal'+issuable.type" class="text-primary _view-btn mb-2"><i class="bi bi-box-arrow-up-right p-1"></i></button>-->
                    <!--Credendtial Modal -->
                    <div class="modal fade" :id="'credentilModal' + issuable.type" tabindex="-1" :aria-labelledby="'credentilModal'+issuable.type" aria-hidden="true">
                      <div class="modal-dialog">
                        <div class="modal-content">
                          <div class="modal-header">
                            <h5 class="modal-title" id="exampleModalLabel">{{$t('EDIT_CREDENTIAL')}}</h5>
                            <div class="col _edit-btn d-flex flex-column align-items-right justify-content-right text-end">
                              <a @click="enableCredentialEditor ? disableInput() : enableInput()" href="#enable" :class="enableCredentialEditor ? 'p-0 text-success fst-italic' : 'p-0 text-primary fst-italic' "><i :class="enableCredentialEditor ? 'bi bi-check-square' : 'bi bi-pencil-square'"></i></a>
                            </div>
                          </div>
                          <!--<credential-editor :issuable="issuable" :enableEditor="enableCredentialEditor" class="modal-body" />-->
                          <div class="modal-footer">
                            <button type="button" class="btn btn-primary" data-bs-dismiss="modal" @click="reset">{{$t('CLOSE')}}</button>
                          </div>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </form>
              <button @click="goToWallet(wallets[0].id)" class="btn btn-primary py-2 px-5 _cbtn" :disabled="this.checkedCredentials.length > 0 ? false : true"><img v-if="btnLoading" src="loader.gif" width="20px"/><span v-else>{{$t('CONFIRM')}}</span></button>
          </div>
        </div>
      </section>
    </main>
    <footer class="fixed-bottom footer mt-auto py-3 bg-light">
      <div class="container">
        <span class="text-muted">{{ $config.copyright }}</span>
      </div>
    </footer>
  </div>
</template>

<script>
import CredentialEditor from '../components/CredentialEditor.vue'
import NavBar from '../components/NavBar.vue'

export default {
  components: { CredentialEditor, NavBar },
  data () {
    return {
      checkedCredentials: [],
      enableCredentialEditor: false,
      btnLoading: false
    }
  },
  computed: {
    availableLocales () {
      console.log("locales", this.$i18n.locales)
      return this.$i18n.locales.filter(i => i.code !== this.$i18n.locale)
    },
    sessionId() {
      console.log("SESSION ID", this.$route.query)
      return this.$route.query.sessionId
    }
  },
  async asyncData ({ $axios, query }) {
    const wallets = await $axios.$get('/issuer-api/wallets/list')
    const issuables = await $axios.$get('/issuer-api/credentials/listIssuables', { params: query })
    return { wallets, issuables }
  },
  methods: {
    reset(){
      this.enableCredentialEditor=false
      console.log(this.issuables[0])
    },
    enableInput(){
      this.enableCredentialEditor=true;
      this.btnDisabled=false;
    },
    disableInput(){
      this.enableCredentialEditor=false;
      this.btnDisabled=true;
    },
    async goToWallet (walletId) {
      this.btnLoading = true;
      console.log("Selected issuables:", this.checkedCredentials)
      let selectedIssuables = {
        credentials: this.issuables.credentials.filter(c => this.checkedCredentials.findIndex(cc => cc == c.credentialData.title) >= 0)
      }
      console.log("Selected issuables:", selectedIssuables)
      const params = this.sessionId != null ? { "sessionId": this.sessionId } : { "walletId": walletId }
      const walletUrl = await this.$axios.$post('/issuer-api/credentials/issuance/request', selectedIssuables, { params: params })
      setTimeout(()=>{window.location = walletUrl}, 2000)
    },
    tester(){
      console.warn(this.issuables.credentials)
    }
  }
}
</script>

<style scoped>
label{
  font-size: 20px;
  margin-top: -3px;
  font-weight: 600;
}
button._view-btn{
  background-color: transparent;
  border: none;
  padding: 0;
  margin: 0;
  font-size: 18px;
  font-weight: 600;
}
.left-inner-addon {
    position: relative;
}
.left-inner-addon input {
    padding-left: 35px !important;
}
.left-inner-addon i {
    position: absolute;
    padding: 17px 15px;
    pointer-events: none;
}
.right-inner-addon {
    position: relative;
}
.right-inner-addon input {
    padding-right: 35px !important;
}
.right-inner-addon i {
    position: absolute;
    right: 0px;
    padding: 17px 15px;
    pointer-events: none;
}
.left-and-right-inner-addon {
    position: relative;
}
.left-and-right-inner-addon input {
    padding-right: 35px !important;
    padding-left: 35px !important;
}
.left-and-right-inner-addon i.right {
    position: absolute;
    right: 0px;
    padding: 17px 15px;
    pointer-events: none;
}
.right-inner-addon-b {
    position: relative;
}
.right-inner-addon-b input {
    padding-right: 35px !important;
}
.right-inner-addon-b i {
    position: absolute;
    right: 0px;
    padding: 17px 15px;
    pointer-events: none;
}
._forms input {
    width: 100%;
		padding: 1em !important;
		margin: 0em !important;
		box-sizing: border-box;
}
._edit-btn button{
  background-color: transparent;
  border: none;
}
._cbtn{
  width: 165px;
  height: 45px
}
._cbtn img{
  margin-top: -3px
}
@media only screen and (max-width: 600px) {
  label{
  font-size: 14px;
  margin-top: -3px;
  font-weight: 600;
}
button._view-btn{
  background-color: transparent;
  border: none;
  padding: 0;
  margin: 0;
  font-size: 13px;
  font-weight: 600;
}
}
</style>