<template>
  <div class="menu-items">
    <h2>{{ burger.itemName }}</h2>
    <img v-bind:src="burger.imgURL" alt="Couldn't load image" title="Perfect">
    <h3 id="kcal">({{ burger.kcal }} kCal)</h3>
    <ul title="Allergic?" class="allergies">
      <li v-if="burger.gluten">Contains gluten</li>
      <li v-else></li>
      <li v-if="burger.lactos">Contains lactose</li>
      <li v-else></li>
    </ul>

    <div id="amount">
      <button id="btn-decrease" v-on:click="decreaseAmount">-</button>
      <h2>{{ amountOrdered }}</h2>
      <button id="btn-increase" v-on:click="increaseAmount">+</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object,
    required: true
  },
  data: function () {
    return {
      amountOrdered: 0,
    }
  },
  methods: {
    decreaseAmount: function () {
      this.amountOrdered -= 1;
      this.$emit('addedBurger', { name:this.burger.itemName,
                                    amount: this.amountOrdered
                                  })
    },
    increaseAmount: function () {
      this.amountOrdered += 1;
      this.$emit('addedBurger', { name:this.burger.itemName,
                                    amount: this.amountOrdered
                                  })
    }
  }
}
</script>

<style scoped>
  .menu-items {
    justify-items: center;
    width: 250px;
    height: 400px;
    background-color: rgb(249, 193, 89);
    border: 1px solid #ccc;
    padding: 10px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    border-radius: 5px;      
  }
  .menu-items img {
    width: 100%;        
    height: 200px;    
    object-fit: cover;            
    border-radius: 5px;           
  }
  h2 {
      font-size: 18px; 
      margin: 10px 0; 
  }
  h3 {
    font-size: 13px;
    color: black;
  }
  #amount {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center; 
    color: black;
    padding: 10px;
    gap: 10px;
  }
  #amount button {
    width: 40px;
    font-size: 15px;
  }
  #btn-decrease:hover{
    cursor: pointer;
    background-color: #e6977f;
  }
  #btn-increase:hover{
    cursor: pointer; 
    background-color: #93d760; 
  }
</style>