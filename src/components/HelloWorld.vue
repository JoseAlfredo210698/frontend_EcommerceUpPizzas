<template>
  <v-container grid-list-md text-xs-center>
    <v-layout row wrap>
      <v-flex xs4 v-for="(pizza, i) in pizzas" :key="i">
        <v-hover>
          <v-card slot-scope="{ hover }" class="mx-auto" color="grey lighten-4" max-width="600">
            <v-img :aspect-ratio="16/9" :src="pizza.image">
              <v-expand-transition>
                <div
                  v-if="hover"
                  class="d-flex transition-fast-in-fast-out orange darken-4 v-card--reveal display-1 white--text"
                  style="height: 100%;"
                >
                  <p
                    style="font-size: 20px;"
                    v-for="(ingredient, j) in pizza.ingredients"
                    :key="j"
                  >{{ingredient.size}} ${{ingredient.price}}</p>
                  <br>
                </div>
              </v-expand-transition>
            </v-img>
            <v-card-text class="pt-4" style="position: relative;">
              <v-btn
                v-on:click="toOrder(pizza)"
                absolute
                color="orange"
                class="white--text"
                fab
                left
                top
                :disabled="pizza.ingredients.length == 0"
              >
                <v-icon>monetization_on</v-icon>
              </v-btn>

              <v-btn absolute color="orange" class="white--text" fab right top>
                <v-icon>shopping_cart</v-icon>
              </v-btn>
              <!-- <div class="font-weight-light grey--text title mb-2">For the perfect meal</div> -->
              <h3 class="display-1 font-weight-light orange--text mb-2">{{pizza.name}}</h3>
              <div class="font-weight-light mb-2">{{pizza.about}}</div>
            </v-card-text>
          </v-card>
        </v-hover>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import Vue from "vue";
import axios from "axios";
import VueAxios from "vue-axios";

Vue.use(VueAxios, axios);

export default {
  data: () => {
    return {
      show: false,
      pizzas: []
      // {
      //   id: 7,
      //   name: "Pizza Bolognesa",
      //   image:
      //     "https://st-listas.20minutos.es/images/2014-09/386767/4525863_640px.jpg?1518612516",
      //   price: "159.90",
      //   about:
      //     "Otro ejemplo de cómo la receta tradicional que se aplica a la pasta se acaba convirtiendo en una pizza es la bolognesa. Tomate, mozzarella y carne picada de ternera son sus ingredientes estrella. Da igual comerla con tenedor o a bocados, la boloñesa siempre triunfa."
      // }

      // statusOrderedPizza: []
    };
  },
  created: function() {
    console.log("created");
    Vue.axios.get(" http://localhost:3333/pizzas").then(response => {
      this.pizzas = response.data;
    });

    // this.pizzas.forEach(pizza => {
    //   this.statusOrderedPizza.push( {id: pizza.id, ordered: this.isOrdered(pizza.id)} )
    // });
  },

  mounted() {
    console.log("mounted");
  },

  methods: {
    isOrdered(id) {
      let orderPizzas = [];
      if (localStorage.getItem("orderPizzas") !== null) {
        orderPizzas = JSON.parse(localStorage.getItem("orderPizzas"));
      }
      let order = orderPizzas.filter(pizza => pizza.id === id);
      if (order.length > 0) {
        return true;
      }
      return false;
    },

    toOrder(pizza) {

      // console.log( pizza )
      let orderPizzas = [];
      // console.log( localStorage.getItem("orderPizzas")  )
      if (localStorage.getItem("orderPizzas") !== null) {
        orderPizzas = JSON.parse(localStorage.getItem("orderPizzas"));
      }

      if (!this.isOrdered(pizza.id)) {
        orderPizzas.push(pizza);
      }
      localStorage.setItem("orderPizzas", JSON.stringify(orderPizzas));
      // console.log(JSON.parse(localStorage.getItem("orderPizzas")));
      // localStorage.removeItem("orderPizzas") 
      this.$router.push({name: 'order'})
    }
  }
};
</script>

<style>
</style>


<style>
.v-card--reveal {
  align-items: center;
  bottom: 0;
  justify-content: center;
  opacity: 0.7;
  position: absolute;
  width: 100%;
}
</style>
