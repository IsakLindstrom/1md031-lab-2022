<template>

<div>
  <header class="header">

    <img id="imageheader" src="https://img.artpal.com/319351/19-20-4-20-20-29-48m.jpg"   >
    <h1 id="title">
      Välkommen till HamBörjat
    </h1>
  </header>

  <main>
    <section class="valbakgrund">
      <h2> Välj burgare nedan:</h2>
    </section>

    <div class="wrapper">

          <Burger v-for="burger in burgers"
                  v-bind:burger="burger"
                  v-on:selected="setSelectedBurger($event)"
                  v-bind:key="burger.name"/>

    </div>
    <section class="info">
      <h3> Kundinformation</h3>
      <p> Du måste äta upp inom loppet av 4 minuter
        annars sker en straffavgift på 320kr
      </p>
      <h3>
        Beställningsinformation:
      </h3>
      <p>
        <label for="fullname">Namn tack!</label><br>
        <input type="name" id="fullname" v-model="fn" required="required" placeholder="För- och Efternamn">
      </p>
      <p>
        <label for="email">E-mail</label><br>
        <input type="email" id="email" v-model="em" required="required" placeholder="E-mail adress">
      </p>

      <h3>
        Betalningsmetoder
      </h3>
      <p>
        <label class="valbetalning" for="betalningsmetod">Val av betalningsmedel 	&#8594; </label>

        <select id="betalningsmetod" name="betalning" >
          <option >Lösgodis</option>
          <option>Swish</option>
          <option>Klarna</option>
          <option selected="selected" >Låna från grannen</option>
          <option>Sms-lån</option>

        </select>
      </p>

      <h3> Kön </h3>
      <p>

        <input type="radio" id="gender" name="kön" value="man">
        <label for="male">Man</label><br>
      </p>

      <p>

        <input type="radio" id="gender" name="kön"  value="kvinna">
        <label for="female">Kvinna</label><br>
      </p>
      <p>

        <input type="radio" id="gender" name="kön" value="icke-binär">
        <label for="icke">Icke-binär</label><br>
      </p>
      <p>

        <input  checked="checked" type="radio" id="gender" name="kön" value="inte relevant">
        <label for="noga">Inte relevant</label><br>
      </p>




      <button class="submit-button" v-on:click="getCustomerInfo" type="submit">


        <img id="orderpicture" src="https://www.butiksnytt.se/wp-content/uploads/2019/11/Edward-Blom-Circle-K.jpg">

        <p id="orderid">Beställ</p>
      </button>

    </section>
  <div id="map">
    <div class="map"  id="dots" v-on:click="setLocation">
      <h1 id="clickhere" >Välj beställningsadress</h1>
      <div  v-bind:style="{ left: this.customerInfo.location.x + 'px',
                      top: this.customerInfo.location.y + 'px' }">
        T
      </div>
    </div>

  </div>



    <hr style="background-color:#F99A02; border:none; height:1px;">
    <footer>
      &#169;  HamBörjat AB
    </footer>
  </main>
</div>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'
const socket = io();

const theMenu = menu;

//constructor object
//function MenuItem(name, url,kcal,god, risk){
//this.name= name;
//this.url = url;
//this.kcal = kcal;
//this.god = god;
//this.risk = risk;
//}




// const burger1 = new MenuItem("Ed","https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcS-OBRsKLfDAxFDwZhELU-QClZmkHfKbc1i-g&usqp=CAU","4800 kcal",true,true);
// const burger2 = new MenuItem("Serb","https://tastykitchen.com/recipes/wp-content/uploads/sites/2/2016/09/pljeskavica-serbian-burger-recipe-5-410x273.jpg","500 kcal",false,false);
// const burger3 = new MenuItem("Enorm","https://i.guim.co.uk/img/media/5eb07f5a85a200b7c3c0f4b13d02bd306e9153b4/0_1292_3024_1814/master/3024.jpg?width=1200&height=900&quality=85&auto=format&fit=crop&s=39fa8fa2c81b4af67c4ce7a061ff1f63","7289 kcal",true,true);
// const burger4 = new MenuItem("lök","https://res.cloudinary.com/hksqkdlah/image/upload/35218_sfs-grilled-bacon-burgers-with-caramelized-onions-15.jpg","900 kcal",false,true);

//Array burgers
const menyn = [theMenu[0], theMenu[1],theMenu[2],theMenu[3]];







export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menyn,
      customerInfo:{
        name:"",
        email:"",
        payment:"",
        selectedGender:"",
        selectedBurger:[],
        location: { x: 0,
                    y: 0
        }

      }



    }
  },
  methods: {


    getCustomerInfo:function(){
      this.customerInfo.name = document.getElementById("fullname").value;
      this.customerInfo.email = document.getElementById("email").value;
      this.customerInfo.payment = document.getElementById("betalningsmetod").value;

      const gender = document.getElementsByName("kön");
      const gendercheck = Array.from(gender).find((radio) => radio.checked);
      this.customerInfo.selectedGender= gendercheck.value;


      socket.emit("addOrder", { orderId: this.getOrderNumber(),
            details: { x: this.customerInfo.location.x,
              y:  this.customerInfo.location.y},

          name: this.customerInfo.name,
          email: this.customerInfo.email,
          payment: this.customerInfo.payment,
          gender: this.customerInfo.selectedGender,
          orderItems: this.customerInfo.selectedBurger


          }

      );
      console.log(this.customerInfo);

    },
    setSelectedBurger: function(event){

      for(let i=0; i<this.customerInfo.selectedBurger.length; i++){
        console.log(this.customerInfo.selectedBurger[i])
        if(this.customerInfo.selectedBurger[i].name===event.name){
          this.customerInfo.selectedBurger[i].amount=event.amount;
          return;
        }
      }
      this.customerInfo.selectedBurger.push(event);

      console.log(this.Burger);
    },
    getOrderNumber: function () {

      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};

      this.customerInfo.location.x = event.clientX - 10 - offset.x;
      this.customerInfo.location.y = event.clientY - 10 - offset.y;


    },
    setLocation: function(event){
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top};

      this.customerInfo.location.x = event.clientX - 10 - offset.x;
      this.customerInfo.location.y = event.clientY - 10 - offset.y;



    }

  }

}


</script>

<style>

  /*noinspection CssUnknownTarget*/
  @import "https://fonts.googleapis.com/css?family=specimen|Lobster";

  option,button,body,select,input{
    font-family:"Lobster", specimen;

    text-align: center;
  }
  body{
    background-color: #2F2FA9;
    color:#F99A02;
  }


  .valbakgrund {
    background-color: #F99A02;
    color: #2F2FA9;

  }


  .submit-button{
    height:80px;
    width:88px;
    background-color:#F99A02;
    margin-bottom: 10px;
    border-style: solid;
    color:#F99A02;
border-width: 5px;
    font-size:20px;
    transition: background-color 0.15s, color 0.15s;
  }

  .submit-button:hover{
    background-color: #19ff00;
    color: black;
    cursor: pointer;
    box-shadow: 5px 5px 10px rgba(0,0,0,0.50);

  }
  .submit-button:active{
    opacity: 0.7;
  }


  li{
    text-align: left;
  }
  #title{
    font-size: 70px;
    text-align:center;
    position: absolute;
    margin-top: -150px;
    width: 100%



  }
  #imageheader{
    position:relative;
    opacity: 0.7;
    object-fit: cover;
    height: 200px;
    width:100%;
    filter: blur(3px);
    -webkit-filter:blur(3px);

  }
  .info{
    border: solid;
    border-color: #F99A02;
    border-width:4px;
    border-opacity: 0.5;

  }

  .wrapper {
    display: grid;

    grid-template-columns: 25% 25% 25% 25%;


  }

  #orderpicture{
    position:relative;
    object-fit: cover;
    margin-right: 7px;
    width:100%;
    height:100%;
  }
#orderid{
  text-align:center;
  position: absolute;
  margin-top: -38px;
  margin-left: 7px;

}
  .map{
    background-image: url("../../public/img/polacks.jpg");
    width:1920px;
    height:1078px;
    overflow:scroll;

  }

#map{
  width:1000px;
  height:400px;
  margin:auto;
  overflow:scroll;






}

#clickhere{
  margin-left: -600px;
}

  #dots div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }
</style>