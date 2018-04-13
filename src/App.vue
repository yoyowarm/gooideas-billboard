<template>
  <div id="app">
    <goodIdeasHead></goodIdeasHead>
    <goodIdeasFilter @bechange= "getStatus"></goodIdeasFilter>
    <router-view v-for="posts in talkFilter" :posts ="posts" />
    <!-- <wantToknow v-for= "(talk,id) in talkFilter" :talk='talk'></wantToknow> -->
    
     <div class="footer">
     <p>Copyright © 2017 <a href="">好想工作室</a></p>
     <p>All Rights Reserved</p>
  </div>
  </div>
</template>

<script>
import axios from 'axios';
import moment from 'moment'
import goodIdeasHead from'./components/goodIdeasHead';
import goodIdeasFilter from './components/goodIdeasFilter'
import wantToknow from './components/wantToknow'

export default {
  name: 'app',

  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      posts:[],
      status: 'all'
    }
  },
  components: {
    goodIdeasHead,
    wantToknow,
    goodIdeasFilter
  },
  computed: {
    talkFilter(){
      var vm = this
       return this.posts.filter(function(item){
          return item.status.includes(vm.status)
   			});
    }
  },
  methods:{
      getStatus(e){
        this.status = e
        console.log(this.status)
      }
  },
  created(){
    var vm = this
    axios.get(`https://devche.com/api/speech/data`)
    .then(response =>{
      vm.posts = response.data.result
      .sort((a,b) => {
      let beforDay = new Date(a.speech_date);
      let afterDay = new Date(b.speech_date)
      return   afterDay - beforDay
      })
      .map( item => {
      var obj = {}
        obj.checkSchedule = function(){
          // console.log('checkSchedule',this)
          const activity_date = moment(item.speech_date).format('YYYYMMDD') 
          const now = moment().format('YYYYMMDD');  
          const diff = now - activity_date;
          // console.log(activity_date)
          console.log(diff)
          if (diff > 5)  return "all finished";
          if (diff < 0)  return "all soon";
          if (diff >= 0 && diff <= 4)  return "all showing";
          
        }
        vm.$set(item , 'status', obj.checkSchedule());
        // console.log(item)
       return item
       
         
      });
      // this.result.posts = this.posts;
      
    })
  }
}
</script>

<style lang="scss">
*::-webkit-scrollbar {
display: none;
}
#app {
   min-height: 100vh
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}


.footer {
    width: 100%;
    height: 80px;
    background-color: rgba(30,139,166,.56);
    color:#fff;
    text-align: center;
    font-size: 12px;
    font-weight: 300;
    padding : 20px;
    margin-top: 2vw;
    p {
        margin-bottom: 10px
    }
    a {
        color: #fff
    }
   
}
</style>
