<template>
  <div id="app">
    <v-app id="inspire">
      <v-stepper v-model="e1">
        <v-stepper-header>
          <v-stepper-step :editable="stepEditable[0]" :complete="e1 > 1" step="1">Pizza(s)</v-stepper-step>
          <v-divider></v-divider>

          <v-stepper-step :editable="stepEditable[1]" :complete="e1 > 2" step="2">Domicilio</v-stepper-step>
          <v-divider></v-divider>

          <v-stepper-step :editable="stepEditable[2]" step="3">Pago</v-stepper-step>
        </v-stepper-header>

        <v-stepper-items>
          <v-stepper-content step="1">
            <v-container grid-list-md text-xs-center>
              <!-- <v-layout row wrap> -->

              <v-btn round color="primary" dark style="top: 24%; z-index: 1;" fixed right small>
                <h4>Total: $ {{total}}</h4>
              </v-btn>

              <v-layout row wrap align-center justify-center fill-height>
                <v-flex xs12 v-for="(pizza, indexPizza) in pizzas" :key="indexPizza">
                  <v-card style="height: 100%">
                    <v-img :src="pizza.image" :aspect-ratio="5"></v-img>
                    <v-card-title primary-title>
                      <v-flex xs12>
                        <h1>{{pizza.name}}</h1>
                      </v-flex>
                      <v-flex d-flex xs12>
                        <v-list>
                          <span
                            v-for="(item, indexOrder) in pizzasToOrder[indexPizza].request"
                            :key="indexOrder"
                          >
                            <v-list-tile avatar>
                              <v-btn
                                class="gray"
                                :disabled="pizzasToOrder[indexPizza].request.length < 2"
                                outline
                                icon
                                small
                                @click="removePizza(indexOrder, indexPizza)"
                              >
                                <v-icon color="grey lighten-1">close</v-icon>
                              </v-btn>

                              <v-list-tile-content>
                                <v-tooltip bottom>
                                  <template v-slot:activator="{ on }">
                                    <span v-on="on">
                                      <v-list-tile-title>
                                        <span>
                                          <span
                                            class="green--text"
                                            style="fontSize: 20px;"
                                          >${{ item.price }}</span>&ensp;
                                          <span
                                            class="black--text"
                                            style="fontSize: 25px;"
                                          >{{ item.size }}</span>
                                        </span>
                                      </v-list-tile-title>
                                      <v-list-tile-sub-title>{{ item.ingredients }}</v-list-tile-sub-title>
                                    </span>
                                  </template>
                                  <span>{{ item.ingredients }}</span>
                                </v-tooltip>
                              </v-list-tile-content>
                              <v-list-tile-action>
                                <v-btn
                                  class="gray"
                                  icon
                                  small
                                  @click="increaseOrder(indexPizza, indexOrder)"
                                >
                                  <v-icon color="grey lighten-1">expand_less</v-icon>
                                </v-btn>
                                <v-btn icon small>{{pizzasToOrder[indexPizza].quantity[indexOrder]}}</v-btn>

                                <v-btn
                                  class="gray"
                                  icon
                                  small
                                  @click="decreaseOrder(indexOrder, indexPizza)"
                                >
                                  <v-icon color="grey lighten-1">expand_more</v-icon>
                                </v-btn>
                              </v-list-tile-action>
                            </v-list-tile>
                          </span>
                        </v-list>
                      </v-flex>
                    </v-card-title>

                    <div class="text-xs-center">
                      <span
                        v-for="(item, indexIngrediente) in pizza.ingredients.filter(x => !pizzasToOrder[indexPizza].request.includes(x)) "
                        :key="indexIngrediente"
                      >
                        <v-chip
                          @click="addPizza(item, indexPizza)"
                          color="green"
                          text-color="white"
                        >
                          <v-avatar class="green darken-4">
                            <v-icon>add_circle</v-icon>
                          </v-avatar>
                          {{item.size}}
                        </v-chip>
                      </span>
                    </div>

                    <v-btn
                      outline
                      :disabled="pizzas.length == 1"
                      style="bottom: 0%;"
                      color="red lighten-1"
                      fab
                      dark
                      small
                      absolute
                      right
                      @click="viewAlert(pizza.name, pizza.about, indexPizza)"
                    >
                      <v-icon>delete</v-icon>
                    </v-btn>
                  </v-card>
                </v-flex>
              </v-layout>
            </v-container>

            <v-layout row wrap align-center justify-center fill-height>
              <v-flex xs4 text-xs-center>
                <v-btn small round color="primary" :to="{ name: 'home' }">Añadir otra pizza</v-btn>
                <v-btn small round color="primary" @click="nextStep(2)">Continuar</v-btn>
                <v-btn small round color="error" @click="cancelar">Cancelar</v-btn>
              </v-flex>
            </v-layout>
          </v-stepper-content>

          <v-stepper-content step="2">
            <v-container grid-list-md text-xs-center>
              <v-layout row wrap align-center justify-center fill-height>
                <v-form ref="form" v-model="valid" lazy-validation>
                  <v-text-field v-model="userData.name" :rules="nameRules" label="Nombre" required></v-text-field>
                  <v-text-field
                    v-model="userData.address"
                    :rules="nameRules"
                    label="Dirección"
                    required
                  ></v-text-field>
                  <v-text-field
                    v-model="userData.phone"
                    :rules="nameRules"
                    label="Teléfono"
                    required
                  ></v-text-field>
                  <v-text-field
                    v-model="userData.references"
                    :rules="nameRules"
                    label="Referencia"
                    required
                  ></v-text-field>
                  <v-text-field
                    v-model="userData.email"
                    :rules="emailRules"
                    label="Correo"
                    required
                  ></v-text-field>
                  <div>
                    <v-chip color="primary" text-color="white">
                      <v-avatar class="black darken-4">
                        <v-icon>attach_money</v-icon>
                      </v-avatar>
                      Total: ${{total}}
                    </v-chip>
                  </div>

                  <v-btn small :disabled="!valid" round color="primary" @click="validate">Continuar</v-btn>
                  <v-btn small round color="error" @click="cancelar">Cancelar</v-btn>
                </v-form>
              </v-layout>
            </v-container>
          </v-stepper-content>

          <v-stepper-content step="3">
            <v-container grid-list-md text-xs-center>
              <v-layout row wrap align-center justify-center fill-height>
                <v-flex xs8>
                  <v-card>
                    <v-img
                      :aspect-ratio="5"
                      src="https://arc-anglerfish-syd-prod-nzme.s3.amazonaws.com/public/PJCM3X53FRFX5DVQAQLFZ4TEII.jpg"
                    ></v-img>
                    <v-card-text class="pt-4" style="position: relative;">
                      <h3 class="display-1 font-weight-light orange--text mb-2">
                        <strong>Pago de tu Compra</strong>
                      </h3>
                      <div class="font-weight-light mb-2">
                        <br />
                        <div ref="card" />
                        <br />
                        <span v-if="message" style="color: red;">{{message}}</span>
                      </div>
                    </v-card-text>
                  </v-card>
                </v-flex>
              </v-layout>
            </v-container>

            <v-layout row wrap align-center justify-center fill-height>
              <v-flex xs4>
                <v-btn small round color="success" @click="toPay">Comprar</v-btn>
                <v-btn small round color="error" @click="cancelar">Cancelar</v-btn>
              </v-flex>
            </v-layout>
          </v-stepper-content>
        </v-stepper-items>
      </v-stepper>

      <v-layout row justify-center>
        <v-dialog v-model="dialog" max-width="290">
          <v-card>
            <v-card-title
              class="headline"
            >¿Estás seguro de no adquirir la rica Pizza de {{alertData.name}} ?</v-card-title>

            <v-card-text>{{alertData.about}}</v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>

              <v-btn color="green darken-1" flat="flat" @click="dialog = false">No</v-btn>
              <v-btn color="green darken-1" flat="flat" @click="removePizzaOrder()">Si</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-layout>

      <v-snackbar v-model="toasts" :timeout="6000">
        {{textToasts}}
        <v-btn color="pink" flat @click="snackbar = false">Ok</v-btn>
      </v-snackbar>
    </v-app>
  </div>
</template>


 <script lang="ts">
import { Component, Vue } from "vue-property-decorator";

let stripe = Stripe("pk_test_gxEfDTN4lr9YbbREgh4rj3kj00p6DMtyCC");
let elements = stripe.elements();
let card = undefined;

// @Component({

// })
//export default class Order extends Vue {}

export default {
  data() {
    return {
      dialog: false,

      alertData: {
        indexPizza: "",
        pizzaName: "",
        about: ""
      },
      stepEditable: [true, false, false],
      valid: true,
      nameRules: [v => !!v || "Requerido"],
      emailRules: [
        v => !!v || "Requerido",
        v => /.+@.+/.test(v) || "Correo inválido"
      ],
      phoneRules: [v => !!v || "Requerido"],
      referencesRules: [v => !!v || "Requerido"],
      addressRules: [v => !!v || "Requerido"],
      total: 0.0,
      message: "",
      e1: 0,
      userData: {
        name: "",
        email: "",
        phone: "",
        address: "",
        references: "",
        token_card: "",
        type_card: ""
      },

      pizzas: [] as any,
      pizzasToOrder: [] as any,
      toasts: false,
      textToasts: ""
    };
  },
  created: function() {
    let orderPizzas = JSON.parse(String(localStorage.getItem("orderPizzas")));
    orderPizzas.forEach((pizza: any) => {
      this.pizzasToOrder.push({
        // id: pizza.id,
        pizza: pizza.name,
        quantity: [1],
        request: [pizza.ingredients[0]]
      });
    });

    console.log(orderPizzas);
    console.log(this.pizzasToOrder);

    this.pizzas = orderPizzas;
  },

  mounted() {
    this.getTotal();
    let style = {
      base: {
        color: "#32325d",
        fontFamily: '"Helvetica Neue", Helvetica, sans-serif',
        fontSmoothing: "antialiased",
        fontSize: "16px",
        "::placeholder": {
          color: "#aab7c4"
        }
      },
      invalid: {
        color: "#fa755a",
        iconColor: "#fa755a"
      }
    };

    card = elements.create("card", { style: style });
    card.mount(this.$refs.card);
  },

  methods: {
    async toPay() {
      this.message = "";
      await stripe.createToken(card).then((result: any) => {
        if (result.error) {
          this.message = result.error.message;
        } else {
          this.userData.token_card = result.token.id;
          this.userData.type_card = result.token.card.brand;

          let orders = [];
          this.pizzasToOrder.forEach(order => {
            let sizes = [];
            order.request.forEach((element, index) => {
              sizes.push({
                price: element.price,
                size: element.size,
                quantity: order.quantity[index]
              });
            });
            orders.push({ pizza: order.pizza, request: sizes });
          });

          Vue.axios
            .post(" http://localhost:3333/order", {
              orders: orders,
              userData: this.userData,
              total: this.total
            })
            .then(response => {
              console.log(response);
              if (response.data.status == 201) {
                localStorage.removeItem("orderPizzas");
                this.toasts = true;
                this.textToasts = "¡Compra exitosa!";
                this.$router.push({ name: "home" });
              }
            });
        }
        console.log("result>", result);
      });
    },

    async addPizza(pizza, indexPizza) {
      this.pizzasToOrder[indexPizza].request.push(pizza);
      this.pizzasToOrder[indexPizza].quantity.push(1);
      this.getTotal();
    },
    async removePizza(indexOrder, indexPizza) {
      this.pizzasToOrder[indexPizza].request.splice(indexOrder, 1);
      this.pizzasToOrder[indexPizza].quantity.splice(indexOrder, 1);
      this.getTotal();
    },

    validate() {
      console.log(this.$refs.form.input);
      if (this.$refs.form.validate()) {
        this.snackbar = true;
        console.log("ok");
        this.e1 = 3;
        this.stepEditable[2] = true;
      } else {
        console.log("error");
        this.stepEditable[2] = false;
      }
    },

    async removePizzaOrder(indexPizza) {
      this.pizzas.splice(this.alertData.indexPizza, 1);
      this.pizzasToOrder.splice(this.alertData.indexPizza, 1);
      this.dialog = false;
      this.getTotal();
    },
    async increaseOrder(indexPizza, indexOrder) {
      let quantity = (this.pizzasToOrder[indexPizza].quantity[indexOrder] += 1);
      // this.$emit(this.pizzasToOrder[indexPizza].quantity[indexOrder], quantity)
      // this.pizzasToOrder[indexPizza].quantity = quantity
      console.log(this.pizzasToOrder[indexPizza].quantity);
      this.getTotal();
    },
    async decreaseOrder(indexPizza, indexOrder) {
      if (this.pizzasToOrder[indexPizza].quantity[indexOrder] > 1) {
        this.pizzasToOrder[indexPizza].quantity[indexOrder] -= 1;
        this.getTotal();
      }
    },
    cancelar() {
      localStorage.removeItem("orderPizzas");
      console.log(localStorage.getItem("orderPizzas"));
      this.$router.push({ name: "home" });
    },

    getTotal() {
      console.log(this.total);
      this.total = 0.0;
      this.pizzasToOrder.forEach(order => {
        order.request.forEach((element, index) => {
          this.total += element.price * order.quantity[index];
        });
      });
      this.total = this.total.toFixed(2);
      console.log(this.total);
    },
    viewAlert(name, about, indexPizza) {
      this.alertData.indexPizza = indexPizza;
      this.alertData.name = name;
      this.alertData.about = about;
      console.log(this.alertData);
      this.dialog = true;
    },
    nextStep(fase) {
      this.e1 = fase;
      this.stepEditable[1] = true;
    },
    toArray(array) {
      return array.split(",");
    }
  }
};
</script>

