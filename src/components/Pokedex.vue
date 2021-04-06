<template>
  <div id="Errorlog" v-if="errorFlag">
    {{errormsg}}  ERROR AXIOS
  </div>
  <div id="Pokedex" v-else >
    <div id="Loadinglog" v-if="loadingFlag">
      <h1>Loading...</h1>
    </div>
    <div id="Loaded" v-else>
      <h1>POKEMON DEX</h1>
      <input class="search-box" type="text" v-model="search" placeholder="Pokemon Name / Type">
      <ul v-for="v in filteredPoke" v-bind:key="v.id">
        <router-link :to="{name: 'pokemon', params: {id: v.id}}">
          <li class="poke-list">
            <img class="poke-img" :src="v.sprite_url" width=75% height=75%>
            <p>No.{{ v.id }} {{ v.name }}</p>
          </li>
        </router-link>
      </ul>
    </div>
 </div>
</template>
<script>
export default {
  name: "Pokedex",
  data() {
    return {
      poke_info: [],
      loadingFlag: true,
      errorFlag: false,
      errormsg: null,
      search: ''
    };
  },
  computed: {
    filteredPoke: function() {
      var pokemons = [];
      for(var i in this.poke_info) {
          var filtered_poke = this.poke_info[i];
          if(filtered_poke.name.indexOf(this.search) !== -1 ||
            filtered_poke.type.indexOf(this.search) !== -1) {
              pokemons.push(filtered_poke);
          }
      }
    return pokemons;
    }
  },
  methods: {},
  async mounted() {
    const res1 = await this.$axios.get("https://pokeapi.co/api/v2/pokemon")
    .catch(error =>{
      console.log(error.response)
      this.errormsg = error.response.status
      this.errorFlag = true;
    })
    const res2 = await this.$axios.get("https://pokeapi.co/api/v2/pokemon?limit="+ res1.data.count)
    .catch(error =>{
        console.log(error.response)
        this.errormsg = error.response.status
        this.errorFlag = true;
    })
    for (const result of res2.data.results) {
    this.$axios.get(result.url.slice(0,-1)).then((res) => {
      this.poke_info.push({
        id: res.data.id,
        name: res.data.name,
        sprite_url: res.data.sprites.front_default,
        type: res.data.types[0].type.name
      })
    }).then(()=> {
      if(this.poke_info.length == res1.data.count){
        this.poke_info.sort((a,b) =>{
          if (a.id > b.id){
            return 1
          }else{
            return -1
          }
        })
      this.loadingFlag = false
      }
    })}
  }
};
</script>
<style>
#Pokedex{
  margin-top: 100px;
	text-align: center;
}
.poke-list{
  border-style: solid;
  border-width:1px;
  width: 250px;
  height: 250px;
  margin: 10px;
  border-color: lightgray;
  border-radius: 10px 10px 0px 0px;
  background: white;
  display: block;
  float: left;
  box-shadow: 3px 3px 3px rgba(0, 0, 0, 0.1);
  text-align: center;
  color: black;
}
.poke-img{
  border-style: solid;
  border-width:0px;
  border-color: white;
  border-radius: 9px 9px 0px 0px;
  padding-right: 31px;
  padding-left: 31px;
  background-color: rgb(180, 220, 235);
}
.search-box{
  border-style: solid;
  height: 40px;
  width: -webkit-calc(50% - 50px);
  padding-left: 15px;
  background-image: url(../assets/search_icon.png);
  background-size:30px;
  background-position: right 10px center;
  background-repeat: no-repeat;
  border-width: 0px;
  border-color: lightgray;
  box-shadow: 0 1px 2px 0 rgba(60, 64, 67, .30), 0 1px 3px 1px rgba(60, 64, 67, .15);
  border-radius: 5px;
  font-size: 12pt;
}
</style>
