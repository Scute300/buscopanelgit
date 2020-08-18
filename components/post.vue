<template>
    <div class="container">
        <div class="columns" v-for="post of posts">
            <div class="column is-12">
                <div class="card">
                    <div class="card-content">
                        <div class="media">
                        <div class="media-left">
                            <figure class="image is-48x48">
                            <img :src="post.post.user.avatar" alt="Placeholder image">
                            </figure>
                        </div>
                        <div class="media-content">
                            <p class="title is-4">{{post.post.user.username}}</p>
                            <p class="subtitle is-6">{{post.post.user.bio}}</p>
                        </div>
                        </div>
                        <div class="content">
                            {{post.report}}
                        </div>
                    </div>
                    <div v-if="isonepost == true" class="card-image">
                        <figure class="image is-4by3">
                        <img :src="post.post.images[0].url" alt="Placeholder image">
                        </figure>
                    </div> 
                    <div v-else v-for="image of images" class="card-image">
                        <figure class="image is-4by3">
                        <img :src="image.url" alt="Placeholder image">
                        </figure>
                    </div> 
                    <button 
                    @click="moderar()"
                    :disabled="moderate == true"
                    v-if="isonepost == false" class="button is-warning is-fullwidth">Moderar</button>
                    <button
                    @click="eliminarymoderar(id, 'moderaryban')"
                    :disabled="moderate == true"
                    v-if="isonepost == false" class="button is-danger is-fullwidth">Moderar post y eliminar usuario</button>
                    <button    
                    :disabled="moderate == true" 
                    v-if="isonepost == false" class="button is-primary is-fullwidth">Quitar reporte</button>
                <div id="footer">
                    <nuxt-link 
                    v-if="isonepost == true"
                    :to="`/onereport/${post.id}`">Ver reporte completo</nuxt-link>
                    <nuxt-link 
                    v-else
                    to="/home">Regresar</nuxt-link>
                </div>             
                </div>
            </div>
        </div>
        <div v-if="isonepost == false" class="columns is-multiline">
            <div class="column is-12">
                <h2 class="title is-4">Reportante:</h2>
            </div>
            <div class="column is-12">
                <div class="box">
                    <article class="media">
                        <div class="media-left">
                        <figure class="image is-64x64">
                            <img :src="reportante.avatar" alt="Image">
                        </figure>
                        </div>
                        <div class="media-content">
                            <div class="content">
                                <p>
                                <strong>{{reportante.username}}</strong> <small>{{reportante.name}}</small> <small>31m</small>
                                <br>
                                {{reportante.bio}}
                                </p>
                            </div>
                        </div>
                    </article>
                    <button
                    :disabled="moderate == true" 
                    @click="eliminarymoderar(reportante.id, 'banreportante')"
                    class="button is-warning is-fullwidth">Moderar usuario</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'post',
        props: {
            posts : Array,
            isonepost: Boolean,
            images: Array,
            reportante: Object, 
            id: Number
        },
        data(){
            return{
                moderate: false
            }
        },
        created(){

        },
        methods:{
            async moderar(){
                const token = await localStorage.getItem('user-token')
                await this.$axios.delete('api/v2/panel/deletepost/'+this.$route.params.id
                ,{headers: {Authorization: `Bearer ${token}`}, timeout:60000})
                .then(response=>{
                    alert(response.data.data)
                    this.$router.push('/home')
                }).catch(error =>{
                    alert(error.response.data.message)
                })
            },
            async eliminarymoderar(id, type){
                this.moderate = true
                const token = await localStorage.getItem('user-token')
                switch(type){
                    case 'moderaryban':
                        await this.$axios.post('api/v2/panel/banuser/'+id
                        ,{},{headers: {Authorization: `Bearer ${token}`}, timeout:60000})
                        .then(response=>{
                            alert(response.data.data)
                            this.$router.push('/home')
                        }).catch(error =>{
                            alert(error.response.data.message)
                        })
                        await this.moderar()
                    break    
                    case 'banreportante':
                        await this.$axios.post('api/v2/panel/banuser/'+id
                        ,{},{headers: {Authorization: `Bearer ${token}`}, timeout:60000})
                        .then(response=>{
                            alert(response.data.data)
                            this.$router.push('/home')
                        }).catch(error =>{
                            alert(error.response.data.message)
                        })
                }
                this.moderate = false
            },
            async eliminarpost(){
                this.moderate == false
                const token = await localStorage.getItem('user-token')
                this.$axios.delete('api/v2/panel/deletereport'+ this.$route.params.id,
                {headers: {Authorization: `Bearer ${token}`}, timeout:60000})
                .then(response =>{
                    this.moderate = false
                    alert(response.data.data)
                    this.$router.push('/home')
                }).catch(error =>{
                    this.moderate = false
                    alert(error.response.data.message)
                })
            }
        }
    }
</script>

<style scoped>
.nofull{
width: 450px; 
height: 250px
}
.full{
    width: 100%;
    height: 500px;
}
#footer{
    width: 100%;
    display: flex;
    justify-content: center;
}

</style>