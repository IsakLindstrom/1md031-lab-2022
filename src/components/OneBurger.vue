<template>
    <div class="box">


     <h2 id="burgername">{{ burger.name }}</h2>
      <img v-bind:src="burger.url" style="width: 280px; height:250px">

      <ul>
        <li>
          {{ burger.kCal }}
        </li>
        <li v-if="burger.risk ==='Riskfylld'"  class="gluten" >
          {{burger.risk}}
        </li>
        <li v-else>
          {{burger.risk}}
        </li>
        <li v-if="burger.god ==='Motbjudande'" class="gluten">
          {{burger.god}}
        </li>
        <li v-else>
          {{burger.god}}
        </li>
      </ul>


      <button id="selectbutton" v-on:click="selectThisBurger">&#43;</button>
      <h3 id="amountOrdered">{{amountOrdered}}</h3>
      <button id="unselectButton" v-on:click="unselectThisBurger">&#8722;</button>

    </div>
  </template>
  
  <script>
  export default {
    name: 'OneBurger',
    props: {
      burger: Object
    }, methods:{
      selectThisBurger: function(){

        this.amountOrdered +=1;
        this.$emit("selected",{name:this.burger.name,amount:this.amountOrdered})

      },
      unselectThisBurger: function(){

        if(this.amountOrdered>0){
          this.amountOrdered-=1;
        }
        this.$emit("selected",{name:this.burger.name,amount:this.amountOrdered}  )
      },

    }, data: function(){
      return {
        amountOrdered: 0,
      }
    },
  }
  </script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
  .box{
    border: solid;
    border-color: #F99A02;
    border-width:7px;



  }
  .box:hover{
    opacity: 0.95;

  }

  .gluten {

    font-weight: bold;
  }
  #selectbutton{
    cursor: pointer;
    color: #F99A02;
    margin-bottom: 10px;
    display: inline-block;
    margin-right:10px;
    height:30px;
    width:30px;
    font-size:25px;
    transition-duration: 0.25s;
    line-height: 0px;
  }
  #selectbutton:hover{
    transform: scale(1.1);

  }
  #selectbutton:active{
    cursor: pointer;
    color: #F99A02;
    color: #19ff00;
  }
  #unselectButton{
    cursor: pointer;
    color: #F99A02;
    margin-bottom: 10px;
    display: inline-block;
    margin-left:10px;
    height:30px;
    width:30px;
    transition-duration: 0.25s;
    font-size:25px;
    line-height: 0px;
  }
  #unselectButton:hover{
    transform: scale(1.1);

  }

  #unselectButton:active{
    cursor: pointer;
    color: #F99A02;
    color: red;
  }
  #burgername{
    text-align: center;
  }
  #amountOrdered {
    display: inline-block;
  }
  </style>
  