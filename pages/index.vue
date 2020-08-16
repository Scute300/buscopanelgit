<template>
	<div>
		<navbar/>
		<day
		v-if="hour >= '06'"
		/>
		<nigth
		v-if="hour >= '18' || hour <= '06'"
		/>
		<div class="container">
			<div class="columns is-centered">
				<div class="column is-12">
					<div class="card">
						<form @submit.prevent="login">
							<div class="field">
								<label class="label">Email</label>
								<div class="control">
									<input class="input" v-model="email" placeholder="Email de administrador">
								</div>
							</div>
							<div class="field">
								<label class="label">Contraseña</label>
								<div class="control">
									<input class="input" v-model="password" type="password" placeholder="Contraseña">
								</div>
							</div>
							<div class="field">
								<div class="control">
									<button class="button is-fullwidth is-success">
										Acceder
									</button>
								</div>
							</div>
						</form>
					</div>
				</div>
			</div>
				<div class="colums is-centered">
					<div class="column is-12" id="logo">
						<figure class="image is-96x96">
						<img src="../assets/icon.png">
						</figure>
					</div>
				</div>
				<div class="colums is-centered">
					<div class="column is-12" id="logo">
						<span>P-Central 1.0</span>
					</div>
				</div>
		</div>
	</div>
</template>

<script>
import day from '../components/day'
import nigth from '../components/night'
import navbar from '../components/navbar'
import moment from 'moment-timezone'
moment.tz.setDefault('America/Mexico_City')
	export default {
		components: {
			day,nigth,navbar
		},
		data(){
			return{
				time: '',
				hour: '',

				email: '',
				password:'',

				sendform : true
			}
		},
		async created(){
			await this.date()
			await this.gettoken()
		},
		mounted(){
			setInterval( () => {
				this.date()
			}, 60000)
		},
		methods:{
			async date(){
			let hour = moment().format('A')
			let time = moment().format('HH')
			this.time = hour
			this.hour= time
			},
			async gettoken(){
				const petratoken = await localStorage.getItem('user-token')
				if(petratoken !== null){
					this.$router.push('home')
				}
			},
			async login(){
				this.sendform = false
				let token = ''
				await this.$axios.post('api/v1/login', {
					email: this.email,
					password:this.password
				})
				.then(response => {
					token = response.data.data.token
				})
				.catch(error => {
					this.number = '',
					this.password = '',
					this.sendform = true
					alert(error.response.data.message)
				})
				if(token !== ''){
					await localStorage.setItem('user-token', token)
					this.$router.push('home')
					this.sendform = true
				}
			}
		}
		
	}
</script>

<style scoped>
.card{
	padding: 9px;
	margin-top: 9em;
	opacity: .88;
}

#logo{
	display: flex;
	justify-content: center;
}

</style>