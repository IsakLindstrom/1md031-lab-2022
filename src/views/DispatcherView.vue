<template>
    <div id="orders">
      <div id="orderList">
        <div v-for="order in orders" v-bind:key="order">
          {{"Order ID:"+order.orderId}}

          <div  v-for="item in order.orderItems" v-bind:key="item">
            <div v-if="item.amount>0 ">
              {{item.amount +" "+ item.name}}
            </div>

          </div>

          <div>

            {{"Namn: " + order.name}}<br>
            {{" E-mail: "+order.email}}<br>
            {{" Vald betalningsmetod: "+order.payment}}<br>
            {{" Kön: "+order.gender}}
          </div>



        <!-- <h1 id="items">#{{ key }}:  {{ order.orderItems.join(" , ") }}  </h1>
          <h2 id="info">{{order.details.name}}  <br> {{order.details.email}} <br>  {{order.details.payment}}  <br> {{order.details.gender}}</h2>
          -->
        </div>
        <button v-on:click="clearQueue">Clear Queue</button>
      </div>


      <div id="dots" v-bind:style="{ background: 'url(' + require('../../public/img/polacks.jpg')+ ')' }">
          <div v-for="(order, key) in orders" v-bind:style="{ left: order.details.x + 'px', top: order.details.y + 'px'}" v-bind:key="'dots' + key">
            {{ key }}
          </div>
      </div>
    </div>
  </template>
  <script>
  import io from 'socket.io-client'
  const socket = io();

  export default {
    name: 'DispatcherView',
    data: function () {
      return {
        orders: null
      }
    },
    created: function () {
      socket.on('currentQueue', data =>
        this.orders = data.orders);
    },
    methods: {
      clearQueue: function () {
        socket.emit('clearQueue');

      }
    }
  }
  </script>
  <style>
  #orderList {
    top:1em;
    left:1em;
    position: absolute;
    z-index: 2;
    color:black;
    background: rgba(255,255,255, 0.5);
    padding: 1em;
  }
  #dots {
    position: relative;
    margin: 0;
    padding: 0;
    background-repeat: no-repeat;
    width:1920px;
    height: 1078px;
    cursor: crosshair;
  }
  
  #dots div {
    position: absolute;
    background: black;
    color: red;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }

  </style>
  