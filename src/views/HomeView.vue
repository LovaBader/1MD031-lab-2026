<template>

<header class="bildheader">
            <h1>Smashed burgers haket</h1>
        </header>
        <main>
            <h2>Tja och välkommen till Sveriges mest smashade burgare!</h2>
            <section id="burgers">
                <header class="burgersheader">
                    <h3>Burgare</h3>
                </header>

                <header class="valbara">
                    <h4>Välj mellan dessa burgare</h4>
                </header>

                <section id="treburgare">
                    <Burger
                        v-for="burger in burgers"
                        v-bind:burger="burger"
                        v-bind:key="burger.name"
                        v-on:orderedBurger="addToOrder($event)"
                    />
                </section>
            </section>
                

            <section id="kundinfo">
                <header class="kundinfoheader">
                    <h3>Kundinformation</h3>
                </header>
                <p>Viktig kundinformation</p>

                <p>
                    <label for="fullname">Full Name</label><br>
                    <input type="text" id="fullname" v-model="fullName" required="required" placeholder="Full name">
                </p>

                <p>
                    <label for="email">E-mail</label><br>
                    <input type="email" id="email" v-model="email" required="required" placeholder="E-mail address">
                </p>

                <p>
                    <label for="recipient">Recipient</label><br>
                    <select id="recipient" v-model="recipient">
                        <option>Card</option>
                        <option>Swish</option>
                        <option>Klarna</option>
                        <option>Mastercard</option>
                    </select>
                </p>

                <p>
                    <input type="radio" id="female" v-model="gender" value="Female" checked="checked">
                    <label for="female">Female</label><br>

                    <input type="radio" id="male" v-model="gender" value="Male">
                    <label for="male">Male</label><br>

                    <input type="radio" id="nonbinary" v-model="gender" value="Non-binary">
                    <label for="nonbinary">Non-binary</label><br>

                    <input type="radio" id="donotwishtoprovide" v-model="gender" value="Prefer not to say">
                    <label for="donotwishtoprovide">Prefer not to say</label>
                </p>

                <div id="map-container">
                    <div id="map" v-on:click="setLocation">
                        <div id="dots">
                            <div v-bind:style="{ left: location.x + 'px', top:location.y + 'px' }">
                                T
                            </div>
                        </div>
                    </div>
                </div>

                <button type="button" v-on:click="placeOrder">
                    <img src="/img/placeorder.png" alt="PLACE ORDER" style="width: 25px; height: 25px;">
                    PLACE ORDER
                </button>
            </section>

            <hr>
                <footer>
                    TACK FÖR DIN BESTÄLLNING!
                </footer>
        </main>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");

function MenuItem(name, url, kCal, gluten, lactose) {
    this.name = name;
    this.url = url;
    this.kCal = kCal;
    this.gluten = gluten;
    this.lactose = lactose;
}




export default {
  name: 'HomeView',
  components: {
    Burger
  
    },
  
  data: function () {
  return {
    burgers: menu,
    orderedBurgers: {},
    fullName: "",
    email: "",
    recipient: "Card",
    gender: "Female",
    location: { x: 0, y: 0}
  }
},

  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: event.clientX - 10 - offset.x,
                                           y: event.clientY - 10 - offset.y },
                                orderItems: ["Beans", "Curry"]
                              }
                 );
    },

    addToOrder: function (event) {
        this.orderedBurgers[event.name] = event.amount;
    },

    setLocation: function (event) {
        var offset = {
            x: event.currentTarget.getBoundingClientRect().left,
            y: event.currentTarget.getBoundingClientRect().top
        };
        this.location = {
            x: event.clientX - 10 - offset.x,
            y: event.clientY - 10 - offset.y
        };
    },

    placeOrder: function () {
        const itemsArray = [];
        for (const [name, amount] of Object.entries(this.orderedBurgers)) {
            if (amount > 0) {
                itemsArray.push(name + " (" + amount + ")");
            }
        }

        socket.emit("addOrder", {
            orderId: this.getOrderNumber(),
            details: {
                x: this.location.x,
                y: this.location.y,
                fullName: this.fullName,
                email: this.email,
                recipient: this.recipient,
                gender: this.gender
            },
    orderItems: itemsArray
  });
}
    
}
}
</script>

<style>
  #map-container {
    width: 100%;
    height: 400px;
    overflow: scroll;
    margin: 20px 0;
  }

  #map {
    position: relative;
    width: 1920px;
    height: 1078px;
    background: url("/img/polacks.jpg");
    cursor: crosshair;
  }

  #dots div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width: 20px;
    height: 20px;
    text-align: center;
  }

  @import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
body {
    font-size: 12pt;
}

p {
    color: blue;
    margin: 20px;
}

.kundinfoheader {
    color: blue;
}

.burgersheader {
    margin: 20px;
    color: white;
}

.valbara {
    margin: 20px;
    color: white;
}

h1 {
    font-family: 'Times New Roman', serif;
    font-size: 36pt;
}
main, footer, nav ul {
    max-width: 60%;
    margin: 10px auto 10px auto;
}

header {
    max-width: 100%;
}

main {
    background-color: rgba(0, 0, 255, 0.066);
}

/* nav ul li {
    display: inline-block;
    background-color: baby blue;
    padding: 1em;
    margin: 1em;
} */

.bildheader {
    position: relative;
    width: 100%;
    height: 200px;
    overflow: hidden;
}

.bildheader::before {
    content: "";
    position: absolute;
    inset: 0;
    background-image: url("../img/uppsala.jpeg");
    background-size: cover;
    background-position: center;
    opacity: 0.5;
}

.bildheader h1 {
    position: relative;
    color: black;
}

.kundinfoheader {
    position: relative;
    width: 100%;
    height: 200px;
    overflow: hidden;
}

.kundinfoheader::before {
    content: "";
    position: absolute;
    inset: 0;
    background-image: url("../img/UPPSALA.jpg");
    background-size: cover;
    background-position: center;
    opacity: 0.5;
}

.kundinfoheader h3 {
    position: relative;
    color: blue;
    margin: 20px;
}

header h1 {
    width:40rem;
    margin: 50px auto 50px auto;
    text-align: center;
}

nav ul {
    display: grid;
    grid-template-columns: repeat(auto-fill, 9.25em);
    gap: 1em;
    padding: 0;
}

nav li {
    display: block;
    background-color: rgba(0, 0, 255, 0.046);
    padding: 1em;
}

.Very-good {
    color: rgb(0, 128, 15);
}

.Master {
    color: rgb(41, 128, 0);
    font-weight: bold;
}

.allergen {
    color: #ff5500;
    font-weight: bold;
}

#burgers {
    background-color: black;
    color: white;
    border: Dashed;
    border-color: white;
    border-width: 2px;
}

#treburgare {
     display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 20px;
    align-items: start;
}

#treburgare img {
    width: 95%;
    height: 120px;
    object-fit: cover;
}

#kundinfo {
    border: Dashed;
    border-color: black;
    border-width: 2px;
}

button:hover {
   background-color: rgba(255, 0, 217, 0.5);
}


button {
    padding: 5px;
    margin: 20px;
    font-size: 1em;
    border: Ridge;
}

footer {
    text-align: center;
    font-size: 0.8em;
    color: black;
    margin-top: 20px;
}
</style>