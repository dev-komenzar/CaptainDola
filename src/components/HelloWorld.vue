<template>
  <div class="hello">
    <p>テスト {{vueAppTitle}}</p>
    <div>
      <input type="number" name="currentPrice" id="price" v-model.number="inputPrice">
      <input type="number" name="unit" id="unit" v-model.number="unit">
      <button @click="getPrice">Update</button>
    </div>
    <div>
    <div class="container">
      <div class="item">
        <h2>0.1%刻み</h2>
        <ul>
          <li v-for="item in multipliedPrices" :key="item.index">
            {{item.ratio}}% $ {{ item.price }} : {{ calcProfit(item.price)}}
          </li>
        </ul>
      </div>
      <div class="item">
        <h2>差分（{{unit}}刻み）</h2>
        <ul>
          <li v-for="item in differencedPrices" :key="item.index">
            $ {{ item.price}} : {{ calcProfit(item.price)}}
          </li>
        </ul>
      </div>
    </div>
    <div></div>
  </div>
  </div>
  
</template>

<script>
import axios from 'axios'

export default {
  name: 'HelloWorld',
  props: {
    
  },
  data: function(){
    return{
      inputPrice: 4126000,
      unit: 0.01,
      vueAppTitle: process.env.VUE_APP_TITLE
    }
  },
  computed:{
    multipliedPrices: function(){
      const arr = [-2,-1.66,-1.5,-1.33,-1,-0.9,-0.8,-0.7,-0,6,-0.5,-0.4,-0.3,-0.2,-0.1, 0,0.1,0.2,0.3,0.4,0.5,0.6,0.7,0.8,0.9,1,1.33,1.5,1.66,2].reverse()
      return arr.map((element, index) =>{
        return {
          price: Math.floor((100+element) * this.inputPrice /100),
          id: index,
          ratio: element
        }
      })
    },
    differencedPrices: function(){
      const arr =[...Array(21).keys()].map(i => i - 10).reverse()
      const differenceValue = 10000
      return arr.map((element, index) =>{
        return {
          price: this.inputPrice + differenceValue*element,
          id: index
        }
      })
    },
    
  },
  created(){
    const url = process.env.VUE_APP_TITLE === 'App(development)' ? '/v1/getboard':'/search'
    axios.get(url).then(res =>{
        this.inputPrice = res.data.mid_price
      }).catch(err=>console.log(err))
  },
  methods:{
    calcProfit: function(a){
      return Math.floor((Number(a) - this.inputPrice)*this.unit)
    },
    getPrice(){
      const url = process.env.VUE_APP_TITLE === 'App(development)' ? '/v1/getboard':'/search'
      axios.get(url).then(res =>{
        this.inputPrice = res.data.mid_price
      }).catch(err=>console.log(err))

    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}

a {
  color: #42b983;
}

.container {
  display:flex;
  justify-content: center;
}
</style>
