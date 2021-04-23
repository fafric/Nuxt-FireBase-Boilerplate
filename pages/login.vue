<template>
	<v-container>
		<v-row justify="center">
			<v-col sm="12" md="5">
				<h2 class="text-center subtitle-1 font-weight-bold mb-2">
					Log in with your email address
				</h2>
				<v-row>
					<v-col>
						<v-tabs
							v-model="tab"
							background-color="transparent"
							color="blue accent-2"
							grow
							class="mb-3"
						>
							<v-tab to="/login">Login</v-tab>
							<v-tab to="/register">account registration</v-tab>
						</v-tabs>

						<v-row>
							<v-col sm="12">
								<v-card flat>
									<v-card-text class="pa-0">
										<v-form
											ref="login_form"
											v-model="login_valid"
											lazy-validation
										>
											<v-text-field
												v-model="login_email"
												label="mail address"
												required
											/>

											<v-text-field
												v-model="login_password"
												label="password"
												required
												:append-icon="
													show_loginpassword
														? 'mdi-eye'
														: 'mdi-eye-off'
												"
												:type="
													show_loginpassword
														? 'text'
														: 'password'
												"
												@click:append="
													show_loginpassword = !show_loginpassword
												"
											/>

											<v-alert
												v-if="loginErrorMsg"
												dense
												text
												type="error"
											>
												{{ loginErrorMsg }}
											</v-alert>

											<v-btn
												:disabled="!login_valid"
												color="blue darken-3"
												class="my-4 white--text"
												@click="email_login"
											>
												Login
											</v-btn>
                      {{LoginCheck}}
										</v-form>
									</v-card-text>
								</v-card>
							</v-col>
						</v-row>
						<v-divider class="my-8" />
					</v-col>
				</v-row>
			</v-col>
		</v-row>
	</v-container>
</template>

<script>

export default {
	layout: 'signin',
	components: {
		//
	},
	data () {
		return {
			tab: null,
			login_valid: true,
			login_email: 'test@test.com',
			login_password: '123321',
			show_loginpassword: false,
			loginErrorMsg: '',
			socialLoginErrorMsg: ''
		}
	},
	methods: {
		email_login: function(err) {
			this.$store
				.dispatch('signInWithEmail', {
					email: this.login_email,
					password: this.login_password
				})
				// .then(() => {
        //     db.collection("Users").doc(this.email_login).get().then(
        //         res => {
        //           const UserCompany =  res.data().CompanyName
        //           this.$store.Commit('CompanyName', UserCompany)
        //         }
        //       )
        // })
        .then(() => {
					this.login_email = ''
					this.login_password = ''
					this.$router.push({
						name: 'index'
					})
				})
				.catch((err) => {
					if (err.code === 'auth/user-disabled') {
						this.loginErrorMsg =
							'This account is locked.'
					} else {
						this.loginErrorMsg =
							'The email address or password is incorrect.'
					}
				})
		}
	},
  computed: {
    LoginCheck() {
      if (this.$store.getters.isAuthenticated) {
        this.$router.push({
						name: 'index'
					})
      }
    },
  },
}
</script>
