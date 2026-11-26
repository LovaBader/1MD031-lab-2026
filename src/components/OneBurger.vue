<template>
  <div style="display: inline-block">
    <header>
      <h4>{{ burger.name }}</h4>
      <img :src="burger.img" :alt="burger.name" style="width: 200px">
    </header>

    <ul>
      <li v-if="burger.kCal">{{ burger.kCal }} kCal</li>
      <li v-if="burger.gluten">Contains <span class="allergen">gluten</span></li>
      <li v-if="burger.lactose">Contains <span class="allergen">lactose</span></li>
    </ul>

    <p>
      Ordered: {{ amountOrdered }}
      <button type="button" v-on:click="decreaseOrder">-</button>
      <button type="button" v-on:click="increaseOrder">+</button>
    </p>

  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data: function () {
    return {
      amountOrdered: 0,
    }
  },
  methods: {
  increaseOrder: function () {
    this.amountOrdered += 1;
    this.$emit("orderedBurger", {
      name: this.burger.name,
      amount: this.amountOrdered
    });
  },
    
    decreaseOrder: function () {
      if (this.amountOrdered > 0) {
        this.amountOrdered -= 1;
        this.$emit("orderedBurger", {
          name: this.burger.name,
          amount: this.amountOrdered
        });
      }
    }
  }
}
</script>

<style scoped>
.allergen {
  color: #ff5500;
  font-weight: bold;
}

img {
  width: 95%;
  height: 120px;
  object-fit: cover;
}
</style>