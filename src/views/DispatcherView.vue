<template>
    <div id="orders">
      <div id="orderList">
        <div v-for="(order, key) in orders" v-bind:key="'order'+key">
          <p2>#{{ key }}: </p2>
          <p2 v-for="(amount, burger) in order.orderItems">{{ burger }}: {{ amount }}. </p2><br>
          <p2 id="contact-info">{{ order.contact.name  }} {{ order.contact.email }}</p2><br><br>
        </div>
        <button v-on:click="clearQueue">Clear Queue</button>
      </div>
      <div id="dots">
          <div v-for="(order, key) in orders" v-bind:style="{ left: order.location.x + 'px', top: order.location.y + 'px'}" v-bind:key="'dots' + key">
            {{ key }}
          </div>
      </div>
    </div>
  </template>
  <script>
  import io from 'socket.io-client'
  const socket = io("localhost:3000");
  
  export default {
    name: 'DispatcherView',
    data: function () {
      return {
        orders: {},
      }
    },
    created: function () {
      socket.on('currentQueue', data =>
        this.orders = data.orders);
    },
    methods: {
      clearQueue: function () {
        socket.emit('clearQueue');
      },
      changeStatus: function(orderId) {
        socket.emit('changeStatus', {orderId: orderId, status: "Annan status"});

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
    background-image: url('/img/polacks.jpg');
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
  #contact-info{
    font-style: italic;
  }
  </style>
  