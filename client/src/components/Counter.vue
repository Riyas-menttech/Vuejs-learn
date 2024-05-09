<script setup lang="ts">
defineProps<{
  msg: string
}>()
import {ref,onMounted} from 'vue';
import {reactive} from 'vue'

const state = reactive({continue:true})

const count = ref(0);
const name = ref()

const user = ref({
    name: 'Riyaz',
    place: "indore"
});

navigator.geolocation.getCurrentPosition(position => {
    const latitude = position.coords.latitude;
    const longitude = position.coords.longitude;

    fetch(`https://api.bigdatacloud.net/data/reverse-geocode-client?latitude=${latitude}&longitude=${longitude}&localityLanguage=en`)
        .then(response => response.json())
        .then(data => {
            // console.log(data,'location===>>>Data');
            
            user.value.place = data.localityInfo.administrative[2].name;
        })
        .catch(error => {
            console.error('Error fetching geolocation data:', error);
        });
});


const increment = ()=>{
    count.value++;
}
const decrement = ()=>{
    if(count.value == 0){
        return
    }
    count.value--;
}

const handleClick = ()=>{
   user.value.place = 'Kerala'
}
const query = `query {
        games {
            id,
            title,
            platform
        }
    }
    `;
 const games = ref([])
const handleFetch =async()=>{
  const res = await fetch('http://localhost:4000/graphql',{
    method:'POST',
    body: JSON.stringify({ query }),
    headers: {
        'Content-Type': 'application/json'
      },
  })
  const data = await res.json();
  
  games.value = data.data.games
  console.log(games.value,'data');
  
  
}

onMounted(handleFetch)
const show = ref(false)

</script>

<template >
    <div>
        <h2 class="msg" style="color: red;">{{msg}}</h2>
        <button @click="count++">Inc</button>
        <h1 v-if="show">{{count}}</h1> <h1 v-else="show">Nothing</h1>     <button @click="show = !show">Show</button>

        <button @click="decrement">Dec</button>
    </div>
    <div>
    <h1 @click="handleClick">{{user.place}}</h1>
    </div>
    <h1>{{games[0]?.title}}</h1> 
 
    <div v-if="games?.length > 0" :key="game?.id">
     <h2>Games:</h2>
     <ul>
        <li v-for="game in games">{{ game?.title }} - {{ game?.platform[0] }}</li>
     </ul>
    </div>
    <input :value="name"  @input="e=>name=e.target.value" 
    /> 
<h1>{{name}}</h1>
<a href="#/about">about</a>

<!-- <router-link to="/">NewTodo</router-link> -->
<router-link to="/new-one">NewTodo</router-link>


</template>

<style scoped>
.msg {
  color: red;
}
</style>