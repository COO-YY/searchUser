<template>
        <div class="row">
            <h2 v-if="isFirst">欢迎搜索信息</h2>
            <h2 v-else-if="isLoading">正在搜索信息</h2>
            <h2 v-else-if="errMsg">搜索信息失败 ----- {{errMsg}}</h2>
            <div v-else class="card" v-for="(user,index) in users" :key="index">
                <a :href="user.userurl" target="_blank">
                    <img :src="user.imgurl" style="width:100px" />
                </a>
                <p class="card-text">{{user.username}}</p>
            </div>
        </div>
</template>

<script>
import axios from 'axios'
export default {
    data(){
        return{
            isFirst:true,
            isLoading:false,
            errMsg:'',
            users:[]

        }
    },
    mounted(){
        this.$bus.$on('searchAjax',this.searchAjax)
    },
    methods:{
        searchAjax(keyword){
            this.isFirst = false;
            this.isLoading = true;

            axios({
                url:"https://api.github.com/search/users",
                params:{
                    q:keyword
                }
            }).then(res=>{
                this.isLoading = false;
                this.users = res.data.items.map(item => ({
                    username:item.login,
                    imgurl:item.avatar_url,
                    userurl:item.url
                }))
                console.log(res.data.items);

            }).catch(err=>{
                console.log(err);
                this.errMsg = err.message;
            })
        }
    }
}
</script>

<style>
.card{
    float: left;
    width: 33.333%;
    padding: .75rem;
    margin-bottom: 2rem;
    border: 1px solid #efefef;
    text-align: center;
}

.card > img{
    margin-bottom: .75rem;
    border-radius: 100px;
}

.card-text{
    font-size: 85%;
}
</style>