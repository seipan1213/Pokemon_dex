<template>
	<div id="Pokemon-Details">
		<h1 id="Error" v-if="errorFlag">ERROR AXIOS</h1>
		<div id="Success" v-else>
			<div id="Details" v-if="loadedFlag">
				<router-link to="/">
					<p id="back">BACK</p>
				</router-link>
				<h1>No.{{pod.id}} {{pod.name}}</h1>
        <img class="poke-detail-img" :src="pod.front_default" width=300 height=300>
        <img class="poke-detail-img" :src="pod.back_default" width=300 height=300>
        <p class="description">{{pod_s.description}}</p>
        <table>
          <tbody>
            <tr><th>type</th><td>
              <ul class="detail-table" v-for="v in pod.types" v-bind:key="v.id">
                <li class="detail-table__type">{{ v.type.name }}</li>
                </ul></td></tr>
            <tr><th>stuts</th><td>
              <ul class="detail-table">
              <li>hp:{{pod.hp}}</li>
              <li>attack:{{pod.attack}}</li>
              <li>defense:{{pod.defense}}</li>
              <li>special_attack:{{pod.special_attack}}</li>
              <li>special_defense:{{pod.special_defense}}</li>
              <li>speed:{{pod.speed}}</li>
              </ul></td></tr>
            <tr><th>size</th><td>
              <ul class="detail-table">
              <li>height: {{pod.height}}</li>
              <li>weight: {{pod.weight}}</li>
              </ul></td>
            </tr>
          </tbody>
        </table>
			</div>
			<h1 id="Loading" v-else>Loading...</h1>
		</div>
	</div>
</template>
<script>
export default {
	name: "App",
	data(){
		return{
      pod: null,
      pod_s: null,
			loadedFlag: false,
			errorFlag: false,
      errormsg: null
		};
	},
	methods:{},
	async mounted() {
		const res1 = await this.$axios.get("https://pokeapi.co/api/v2/pokemon/" +  this.$route.params.id)
		.catch(error =>{
      console.log(error.response)
      this.errormsg = error.response
			this.errorFlag = true;
    })
    const res2 = await this.$axios.get("https://pokeapi.co/api/v2/pokemon-species/" +  this.$route.params.id)
		.catch(error =>{
      console.log(error.response)
      this.errormsg = error.response
			this.errorFlag = true;
		})
		this.pod = {
			id: res1.data.id,
			name: res1.data.name,
			hp: res1.data.stats[0].base_stat,
			attack: res1.data.stats[1].base_stat,
			defense: res1.data.stats[2].base_stat,
			special_attack: res1.data.stats[3].base_stat,
			special_defense: res1.data.stats[4].base_stat,
			speed: res1.data.stats[5].base_stat,
			types: res1.data.types,
			weight: res1.data.weight,
			height: res1.data.height,
			front_default: res1.data.sprites.front_default,
			back_default: res1.data.sprites.back_default
    }
    this.pod_s = {
      description: res2.data.flavor_text_entries[0].flavor_text
    }
		this.loadedFlag = true
	},
};
</script>

<style>
#Pokemon-Details{
	float: none;
	width: auto;
  min-width: 100vh;
	height: auto;
	min-height: 100vh;
	margin: 0 auto;
  padding: 80px 0;
	display: block;
	background-color: rgb(180, 220, 235);
}
#back{
	position: absolute;
	left: 25px;
	top: 10px;
  display: inline-block;
  font-weight: bold;
  padding: 0.5em 1em;
  text-decoration: none;
  border-left: solid 4px rgb(70, 200, 235);
  border-right: solid 4px rgb(70, 200, 235);
  color: rgb(70, 200, 235);
  background: white;
  padding: 0.5em 1em;
  transition: .4s;
}
#back:hover{
  background: rgb(70, 200, 235);
  color: white;
}
#Details{
  width: 700px;
  min-height: 100vh;
  margin: 0 auto;
  padding: 20px 0;
  border-width: 0;
  border-radius: 20px;
  background-color: white;
}
.poke-detail-img{
  display:inline-block;
	background-color: white;
}
.description{
  width: 500px;
  margin: 0 auto;
  font-size: 20px;
}
table{
	width: 80%;
	margin: 20px auto;
  border-collapse: collapse;
}
table tr{
  border-bottom: solid 2px white;
}
table tr:last-child{
  border-bottom: none;
}
table th{
  position: relative;
  text-align: left;
  width: 30%;
  background-color: rgb(70, 200, 235);
  color: white;
  text-align: center;
  padding: 10px 0;
}
table th:after{
  display: block;
  content: "";
  width: 0px;
  height: 0px;
  position: absolute;
  top:calc(50% - 10px);
  right:-10px;
  border-left: 10px solid rgb(70, 200, 235);
  border-top: 10px solid transparent;
  border-bottom: 10px solid transparent;
}
table td{
  text-align: left;
  width: 70%;
  text-align: center;
  background-color: whitesmoke;
  padding: 10px 0;
}
ul.detail-table{
  list-style: none;
}
ul.detail-table li{
  text-align: left;
}
.detail-table__type{
  line-height: 5px;
}
</style>