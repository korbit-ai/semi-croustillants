<template>
  <div class="container vh-100">
    <div class="row vh-100">
      <div class="col-lg-10 col-xl-9 mx-auto my-auto">
        <div class="card card-authentification flex-row my-5">
          <div class="card-img-left d-none d-md-flex">
             <!-- Background image for card set in CSS! -->
          </div>
          <div class="card-body">
            <h5 class="card-title text-center">Register</h5>
            <form class="form-authentification">
              <div class="form-label-group">
                <input type="text" id="inputUserame" class="form-control" placeholder="Username" required autofocus>
                <label for="inputUserame">Username</label>
              </div>

              <div class="form-label-group">
                <input type="email" id="inputEmail" class="form-control" placeholder="Email address" required>
                <label for="inputEmail">Email address</label>
              </div>
              
              <hr>

              <div class="form-label-group">
                <input type="password" id="inputPassword" class="form-control" placeholder="Password" required>
                <label for="inputPassword">Password</label>
              </div>
              
              <div class="form-label-group">
                <input type="password" id="inputConfirmPassword" class="form-control" placeholder="Password" required>
                <label for="inputConfirmPassword">Confirm password</label>
              </div>

              <button class="btn btn-lg btn-primary btn-block text-uppercase" type="submit">Register</button>
              <a class="d-block text-center mt-2 small" href="/signin">Sign In</a>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
	/* eslint-disable */
  export default {
		async beforeMount() {
      this.options.headers.Authorization = 'Bearer '+this.$store.getters.getUserToken
      await this.isUserLoggin()
  	},
    data: () => ({
		mailError: false,
		passwordError: false,
		sendFail: false,
		sendSucess: false,
		sendProgress: false,
		emailInput: '',
		passwordInput: '',
		regEmail: /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,24}))$/,
		user:{
			email: '',
			password: '',
			user_agents: window.navigator.userAgent
		},
		options: {
      data: {},
      headers: {
        'Authorization': ''
			},
    },
	}),
	methods: {
		isUserLoggin: async function() {
      var isConnected = await this.$store.dispatch('isConnected', this.options)
      if(isConnected){
        window.location.href = '/'
      }
    },
		loginAccount: function(){
			this.mailError = this.emailInput == '' ? true : false
			this.passwordError = this.passwordInput == '' ? true : false
			if(!this.mailError && !this.passwordError){
				this.user.email = this.emailInput
				this.user.password = this.passwordInput
				this.sendProgress = true
				this.$axioshttp
				.post('http://localhost:5000/api/users/login', this.user, this.header)
				.then(response => {
					this.successLogin(response.data.data.id, response.data.data.username)
					this.$router.push('user-panel')
				})
				.catch(error => {
					this.sendFail = true
				})
				.finally(() => this.sendProgress = false)
			}
		},
		isEmailValid: function(){
			this.mailError = (this.emailInput == '')? true : (this.regEmail.test(this.emailInput)) ? false : true;
		},
		passwordChange: function(){
			this.passwordError = false
		},
		reset: function(event){
			this.emailInput = this.passwordInput = ''
			this.mailError = this.passwordError = false
		},
		close: function(event){
			this.sendSucess = false
			this.sendFail = false
		},
		successLogin: function(id, username){
			this.$store.commit('login', {id: id, name: username})
		}
	}
}
</script>