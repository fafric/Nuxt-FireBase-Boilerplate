<template>
	<v-container>
		<v-row justify="center">
			<v-col sm="12" md="5">
				<h2 class="text-center subtitle-1 font-weight-bold mb-2">
					Register with email address
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
							<v-tab to="/login">login</v-tab>
							<v-tab to="/register">account registration</v-tab>
						</v-tabs>

						<v-row>
							<v-col sm="12">
								<v-card flat>
									<v-card-text class="pa-0">
										<v-form
											ref="register_form"
											v-model="register_valid"
											lazy-validation
										>
											<v-text-field
												v-model="register_email"
												label="Email"
												:rules="emailRules"
												required
												validate-on-blur
											/>

											<v-text-field
												ref="register_password"
												v-model="register_password"
												label="Password"
												required
												:append-icon="
													show_registerPassword
														? 'mdi-eye'
														: 'mdi-eye-off'
												"
												:type="
													show_registerPassword
														? 'text'
														: 'password'
												"
												:rules="register_passwordRules"
												validate-on-blur
												loading
												@click:append="
													show_registerPassword = !show_registerPassword
												"
											>
												<template v-slot:progress>
													<v-progress-linear
														:value="score.value"
														:color="score.color"
														absolute
														height="2"
													/>
												</template>
											</v-text-field>
											<v-text-field
												v-model="
													register_password_again
												"
												label="Password confirmation"
												required
												:append-icon="
													show_registerPassword
														? 'mdi-eye'
														: 'mdi-eye-off'
												"
												:type="
													show_registerPassword
														? 'text'
														: 'password'
												"
												validate-on-blur
												:rules="
													register_passwordAgainRules
												"
												@click:append="
													show_registerPassword = !show_registerPassword
												"
											/>

											<v-alert
												v-if="registerErrorMsg"
												dense
												text
												type="error"
											>
												{{ registerErrorMsg }}
											</v-alert>

											<v-btn
												:disabled="!register_valid"
												color="blue darken-3"
												class="mr-4 white--text"
												@click="email_register"
											>
												Registration
											</v-btn>
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
import zxcvbn from 'zxcvbn'

export default {
	layout: 'signin',
	components: {
		//
	},
	data () {
		return {
			registerErrorMsg: '',
			tab: null,
			register_valid: true,
			register_email: '',
			register_password: '',
			register_password_again: '',
			emailRules: [
				(v) => {
					if (v) {
						return (
							/.+@.+\..+/.test(v) ||
							'Please enter a valid email address'
						)
					}else{
						return true
					}
				}
			],
			register_passwordRules: [
				(v) => !!v || 'Please enter your password',
				(v) =>
					zxcvbn(v).score >= 3 ||
					'Make sure you have a strong password with a mixture of uppercase and lowercase letters, numbers and symbols'
			],
			register_passwordAgainRules: [
				(v) => {
					if (v) {
						return (
							this.$refs.register_password.value === v ||
							'Does not match password'
						)
					}else{
						return true
					}
				}
			],
			show_registerPassword: false
		}
	},
	computed: {
		progress() {
			return this.score.value
		},
		score() {
			const result = zxcvbn(this.register_password)

			switch (result.score) {
				case 4:
					return {
						color: 'green',
						value: 100
					}
				case 3:
					return {
						color: 'light-green lighten-1',
						value: 75
					}
				case 2:
					return {
						color: 'amber accent-2',
						value: 50
					}
				case 1:
					return {
						color: 'deep-orange lighten-1',
						value: 25
					}
				default:
					return {
						color: 'red darken-3',
						value: 0
					}
			}
		}
	},
	methods: {
		email_register: function(err) {
			if (this.$refs.register_form.validate()) {
				this.$store
					.dispatch('signUp', {
						email: this.register_email,
						password: this.register_password
					})
					.then(() => {
						this.register_email = ''
						this.register_password = ''
						this.$router.push({
							name: 'index',
							params: {
								dashboard_msg: true,
								dashboard_msg_text:
									'Account registration is complete.'
							}
						})
					})
					.catch((err) => {
						console.log(err)
						if (err.code === 'auth/email-already-in-use') {
							this.registerErrorMsg =
								'This email address is already registered.'
						} else if (err.code === 'auth/invalid-email') {
							this.registerErrorMsg = 'Invalid email address.'
						} else {
							this.registerErrorMsg =
								'Could not register due to an error.'
						}
					})
			}
		}
	}
}
</script>
