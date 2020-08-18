<template>
    <div>
		<navbar/>
		<day
		v-if="hour >= '06'"
		/>
		<nigth
		v-if="hour >= '18' || hour <= '06'"
		/>
        <loading
        v-if="loading == true"
        />
        <div v-else class="container">
            <div class="columns">
                <div class="column" id="tabs">
                    <div class="tabs is-toggle is-toggle-rounded">
                        <ul>
                            <li class="is-active">
                                <a>
                                    <span>Posts</span>
                                </a>
                            </li>
                            <li>
                                <a>
                                    <span>Cvs</span>
                                </a>
                            </li>
                        </ul>
                    </div>
                </div>
            </div>

            <post
            :posts="reports"
            :isonepost="true"
            />
        </div>
    </div>
</template>

<script>
import day from '../components/day'
import nigth from '../components/night'
import navbar from '../components/navbar'
import loading from '../components/loading'
import moment from 'moment-timezone'
import post from '../components/post'
import { report } from 'process'
moment.tz.setDefault('America/Mexico_City')

    export default {
        components: {
            day,nigth,navbar, loading, post
        },
        data(){
            return{ 
				time: '',
                hour: '',
                sendalert: false,
                menu: {enviado: true, en_proceso : false, finalizado: false},
                loading:true,
                
                consulta: '',
                reports : [],
                page: 1,
                interval: false

            }
        },
        async created(){
            console.log('hola mundo')
           await  this.getr('reports')
            await this.date()
        }, 
        mounted(){
            this.interval = setInterval( async() => {
            await this.date()
            await this.getr('reports')
            }, 900000)
        },
        methods:{
            
			async date(){
                let hour = moment().format('A')
                let time = moment().format('HH')
                this.time = hour
                this.hour= time
			},
			async gettoken(){
				const token = await localStorage.getItem('user-token')
				if(token == null){
					this.$router.push('/')
				}
            },

            async getr(dato){
                const token = await localStorage.getItem('user-token')
                console.log(token)
                await this.$axios.post('api/v2/panel/getreports/'+dato,{
                    page: this.page
                },{headers: {Authorization: `Bearer ${token}`}, timeout:60000})
                .then(response=>{
                    let r = response.data.data.data
                    let push = this.reports.concat(r)
                    this.reports = push
                    this.loading = false
                }).catch(error=>{
                    alert(error.response.data.message)
                })
            }
        }

        
    }
</script>

<style scoped>
#footpage{
    display: flex;
    justify-content: center;
}
#tabs{
    display: flex;
    justify-content: center;
    margin-top: 25px;
}
#premium{
    color: #00FF00
}


</style>