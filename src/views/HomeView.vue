<template>
    <div class="welcome-overlay">
        <h1 class="welcome-sign">Welcome to Macdonald!</h1>
        <img class="welcome-background" src="https://media.istockphoto.com/id/1128687113/photo/healthy-food-selection-with-fruits-vegetables-seeds-super-foods-cereals.jpg?s=612x612&w=0&k=20&c=aRK0IbovFgrFzcbNszfHctAd4AHZl8khYMiDTLZ5Qjw=" alt="welcome background" title="welcome background">

    </div>
    
    <section class="menu">
      <nav>Menu:</nav>
      
      <div class="burgers">
      <Burger v-for="burger in burgers"
              v-bind:burger="burger" 
              v-bind:key="burger.itemName"
              v-on:addedBurger="updateOrder($event)"/>
      </div>
    </section>

    <section id = "info">
        <form>
            <h2>Contact information:</h2>
            <p>
                <label for="name">Name</label><br>
                <input type="text" id="name" v-model="formValues.name" required="required" placeholder="Arne Kajser">
            </p>
            <p>
                <label for="email">E-post</label><br>
                <input type="email" id="email" v-model="formValues.email" required="required" placeholder="arne.kaiser@sts.com">
            </p>
            <p>
                <input type="radio" id = "male" name="gender" v-model="formValues.gender" value="male">
                <label for="male">Male</label><br>

                <input type="radio" id = "female" name="gender" v-model="formValues.gender" value="female">
                <label for="female">Female</label><br>

                <input type="radio" id = "other" name="gender" v-model="formValues.gender" value="other">
                <label for="other">Other</label><br>
            </p>
            <p>
                <label for="payMethod">Payment method</label><br>
                <select id="payMethod" v-model="formValues.payMethod">
                    <option value="VISA">VISA</option>
                    <option value="Paypal">Paypal</option>
                    <option value="Master Card">Master Card</option>
                    <option value="American Express">American Express</option>
                    <option value="Ali Express">Ali Express</option>
                </select> 
            </p>
        </form>
        
        <div class="map" v-on:click="addOrder">
          <div>
            <div id="dot" v-bind:style="{ left: location.x + 'px', top: location.y + 'px'}">
              T
            </div>
          </div>
        </div>

        <button id = "btn-submit" type="submit" v-on:click="submitOrder()">
          Submit!
        </button>

    </section>
    <hr>

    <footer>
        Burger King foot lettuce
    </footer>
  </template>
  
  <script>
  import Burger from '../components/OneBurger.vue'
  import io from 'socket.io-client'
  import menu from '../assets/menu.json'
  
  const socket = io("localhost:3000");
  
  // Currently not used..
  function MenuItem(name, url, kcal, gluten, lactos) {
      this.itemName = name;
      this.imgURL = url;
      this.kcal = kcal;
      this.gluten = gluten;
      this.lactos = lactos;
 };

  export default {
    name: 'HomeView',
    components: {
      Burger
    },
    data: function () {
      return {
        burgers: menu,
        formValues:{
          name: "",
          email: "",
          gender: "",
          payMethod: ""
        },
        orderedBurgers: {},
        location: { x: -20,
                    y: -20
        }
      }
    },
    methods: {
      getOrderNumber: function () {
        return Math.floor(Math.random()*100000);
      },
      
      addOrder: function (event) {
        
        var offset = {x: event.currentTarget.getBoundingClientRect().left,
                      y: event.currentTarget.getBoundingClientRect().top};
        
        var scrollOffset = {x: event.currentTarget.scrollLeft,
                            y: event.currentTarget.scrollTop};

        this.location = { x: event.clientX - 10 - offset.x + scrollOffset.x,
                          y: event.clientY - 10 - offset.y + scrollOffset.y};
                          
      },

      updateOrder: function(event) {
        console.log("Event:", event)
        this.orderedBurgers[event.name] = event.amount
        console.log("Current order: ", this.orderedBurgers)
      },
      
      submitOrder: function (){
        
        console.log("Submitted values:", this.formValues);
        console.log("Payment method selected:", this.formValues.payMethod);

        socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                  location: this.location,
                                  contact: this.formValues,
                                  orderItems: this.orderedBurgers
                                }
                   );

        // Clear inputs:
        this.formValues.name = "";
        this.formValues.email = "";
        this.formValues.gender = "";
        this.formValues.payMethod = "";
        this.location = {x:-20, y:-20};
      }
    }
  }
  </script>
  
  <style>
    body {
    font-family: Arial, Helvetica, sans-serif;
}

/* Background */
body:before {
  background-image: url("https://i.pinimg.com/736x/c8/16/e1/c816e160d188190d6e2c03ed9fde549e.jpg");
  background-size: 40px 40px;
  
  content: "";
  display: block;
  position: fixed;
  width: 100%;
  height: 100%;
  left: 0;
  top: 0;
  z-index: -1;
  opacity: 80%;
}

/* Welcome sign */
.welcome-background {
  position: absolute; 
  width: 100%;
  height: 100%;
  top: 0;
  left: 0; 
  border-radius: 15px; 
  opacity: 0.7; 
  z-index: 1;
}
.welcome-overlay {
  position: relative;
  left:30%;
  width: 40%;
  height: 150px; 
  border-radius: 15px; 
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.2); 
  display: flex; 
  align-items: center; 
  justify-content: center;
}
.welcome-sign {
  color: white;
  font-size: 36px;
  font-weight: bold;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.8);
  z-index: 2;
}

/* Menu */
.menu {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  justify-content: center;
  justify-items: center;
  align-items: center;
  
  gap: 20px;
  padding: 40px;
  background-color: rgb(0,0,0,0.93);
  
  border-radius: 15px;
  border-style: dashed;
  border-color: rgb(249, 193, 89);
  border-width: 3px;
}
.menu nav {
  grid-column: span 4;
  font-size: 24px;
  font-weight: bold;  
  color: rgb(249, 193, 89);   
  margin-bottom: 10px;      
}
.menu .burgers {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  gap: 40px;
  color: #444;
  margin: 0 0 0 50px
}
.allergies {
  color: #841003;
  font-weight: bold;
}

/* Info */
#info {
  background-color: rgb(249, 193, 89, 0.85);
  border-radius: 15px;
  border-color: black;
  border-style: dashed;
  border-width: 2px;

  display: grid;
  grid-template-columns: 1fr 2fr;
  gap: 20px; 
  padding: 20px; 
}
section {
  margin: 30px 20px 30px 20px;
  padding: 1px 10px 10px;
}
.map{
  grid-column: 2;
  overflow: auto;
  height: 450px;
  border-radius: 8px;
  border-style: dashed;
  border-width: 1px;
}
.map div{
  position: relative;
  background: url("/img/polacks.jpg");
  width: 1920px; 
  height: 1078px; 
  cursor: crosshair;
}
#dot {
  position: absolute;
  background: black;
  color: white;
  border-radius: 10px;
  width:20px;
  height:20px;
  text-align: center;
}
#btn-submit{
    grid-column: span 2;
    margin-top: 5px;
    background-color: #78ae56;
    border-radius: 4px;
    height: 35px;
    font-size: 15px;
    font-weight: bold;
}
#btn-submit:hover {
    cursor: pointer; 
    background-color: #6a984d;
}
  </style>

