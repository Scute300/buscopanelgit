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
        v-if="report == []"
        />
        <div v-else class="container">
            <post
            :posts="report"
            :images="images"
            :isonepost="false"
            :reportante="reportante"
            :id="userid"
            />
        </div>
    </div>
</template>

<script>
import day from '../../components/day'
import nigth from '../../components/night'
import navbar from '../../components/navbar'
import loading from '../../components/loading'
import moment from 'moment-timezone'
import post from '../../components/post'
moment.tz.setDefault('America/Mexico_City')

    export default {
        components: {
            day,nigth,navbar, loading, post
        },
        data(){
            return {
                time:'',
                hour:'',
                report: [],
                images: [],
                reportante : {},
                userid: 0,
                respondiendo: false
            }
        },
        async created(){
            await this.date()
            await this.gettoken()
        },
        async mounted(){
            this.getreport()
        },
        methods: {
			async date(){
			let hour = moment().format('A')
			let time = moment().format('HH')
			this.time = hour
			this.hour= time
			},
			async gettoken(){
				const petratoken = await localStorage.getItem('user-token')
				if(petratoken == null){
					this.$router.push('/')
				}
            },
            async getreport(){
				const token = await localStorage.getItem('user-token')
                await this.$axios.post('api/v2/panel/onereport/'+this.$route.params.id,{
                    type: 'post'
                },{headers: {Authorization: `Bearer ${token}`}, timeout:60000})
                .then(response=>{
                    let r = response.data.data.report
                    let push = this.report.concat(r)
                    this.report = push
                    this.images = response.data.data.report.post.images
                    this.reportante = response.data.data.reportante
                    this.userid = response.data.data.report.post.user_id
                    this.loading = false
                }).catch(error=>{
                    alert(error.response.data.message)
                })
            }
        }
        
    }
</script>

<style scoped>
#imagen {
    background-repeat: no-repeat;
    background-position: center;
    background-size: auto 100%;
    background-color: black;
}
</style>