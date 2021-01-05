<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <h1>{{count}}</h1>
  <h1>{{double}}</h1>
  <!-- <ul>
    <li v-for = "number in numbers" :key="number">
      <h1>{{number}}</h1>
    </li>
  </ul>
  <h1>{{person.name}}</h1> -->
  
  {{err}}
  <Suspense>
    <template #default>
      <div>
        <async-show></async-show>
        <dog-show></dog-show>
      </div>
    </template>
    <template #fallback>
      loading
    </template>
  </Suspense>
  <h1 v-if="loading">Loading!...</h1>
  <img :src="result.message" v-if="loaded">
  <h1>{{greetings}}</h1>
  <h1>x: {{x}},y:{{y}} </h1>
  <button @click = "increase">👍+1</button>
  <button @click = "UpdateGreeting">👍UpdateGreeting</button>
</template>

<script lang="ts">
import { ref,computed, reactive,toRefs,watch,onErrorCaptured} from 'vue'
import useMousePosition from './hooks/useMousePosition'
import useURLLoader from './hooks/useURLLoaders'

import AsyncShow from './components/AsyncShow.vue'
import DogShow from './components/DogShow.vue'
interface DataProps {
  count: number;
  double: number;
  increase: () => void;
  numbers: number [];
  person: {name?: string};
}
interface DogResult{
  message: string;
  status: string;
  
}
interface CatResult{
  id: string;
  url: string;
  width: number;
  height: number;
}
export default {
  name: 'App',
  components: {
    AsyncShow,
    DogShow,
  },
  setup(){
    // const count = ref (0)
    // const double = computed(()=>{
    //   return count.value *2
    // })
    // const increase = () => {
    //    count.value ++
    // }
    const err = ref(null)
    onErrorCaptured((e: any)=>{
      err.value = e
      return true
    })
    const data: DataProps = reactive({
      count: 0,
      increase:() => {
        data.count ++
      },
      double: computed(()=> data.count * 2),
      numbers : [0,1,2],
      person: {}
    })
    const {x,y} = useMousePosition()
    // const {result,loading,loaded,error} = useURLLoader<DogResult>('https://dog.ceo/api/breeds/image/random')
    const {result,loading,loaded,error} = useURLLoader<CatResult[]>('https://api.thecatapi.com/v1/images/search?limit=1')
    watch(result,()=>{
      if(result.value){
        console.log('value',result.value[0].url)
      }
    })
    const greetings = ref('')
    const UpdateGreeting = () => {
      greetings.value += 'Hello!'
    }
    watch(greetings,()=>{
      document.title = 'updated' + greetings.value
    })
    
    const refData = toRefs(data)
    return {
      // data
      // count,
      // increase,
      // double
      ...refData,
      greetings,
      UpdateGreeting,
      x,y,
      result,loading,loaded,error,err
    }
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
