<template>
  <v-app>
    <!-- top bar par défaut de vuetify -->
    
    <!-- fin top bar par défaut de vuetify -->
    <v-main>
      <v-container fluid>
        <v-card
          color="#3853D8"
          height="150px"
          tile
          flat
          class="d-flex align-center justify-center"
          dark
          >
            <v-row>
                <v-col cols="12" sm="12">
                    <h1 v-if="chosenCategory" class="text-center">{{ chosenCategory }}</h1>
                </v-col>
            </v-row>
        </v-card>
        <v-card tile class="mx-16 mt-n10" color="white">
          <v-row>
            <v-col cols="12" sm="3" class="mt-n6 pr-0">
              <v-toolbar flat outlined>
                <v-toolbar-title><strong>Filtres</strong></v-toolbar-title>
                <v-spacer></v-spacer>
                <v-divider vertical></v-divider>
                <v-btn icon class="ml-1">
                  <v-icon color="#3853D8" @click="reset('all')"> mdi-sync </v-icon>
                </v-btn>
              </v-toolbar>
            </v-col>
            <v-col cols="12" sm="3" class="mt-n6 px-0">
              <v-toolbar flat outlined>
                <v-toolbar-title class="d-flex align-center">
                  Classer par: 
                </v-toolbar-title>
                <v-spacer></v-spacer>
                <v-select
                  class="d-flex align-center"
                  label="Choisir"
                  variant="outlined"
                  :items="['Prix croissant', 'Prix décroissant', 'A-Z', 'Z-A']"
                  v-model="orderBy"
                ></v-select>
              </v-toolbar>
            </v-col>
            <v-col cols="12" sm="6" class="mt-n6 px-0">
              <v-toolbar flat outlined>

              </v-toolbar>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="3" class="">
              <!-- Recherche par prix -->
              <v-card flat outlined tile>
                <v-toolbar flat>
                  <v-icon color="black" class="mr-2">msi-chevron-down</v-icon>
                  <strong>PRIX</strong>
                  <v-spacer></v-spacer>
                  <v-icon 
                    color="grey" 
                    small
                    @click="reset('price_range')"
                  >mdi-close</v-icon>
                </v-toolbar>
                <v-toolbar flat>
                  <v-text-field
                    placeholder="50"
                    filled
                    rounded
                    dense
                    class="mx-2"
                    v-model="price_range[0]"
                  ></v-text-field>
                  <v-text-field
                    placeholder="$1900"
                    filled
                    rounded
                    dense
                    class="mx-2"
                    v-model="price_range[1]"
                  ></v-text-field>
                </v-toolbar>
                <v-range-slider 
                  color="blue" 
                  :min="price_mini" 
                  :max="price_maxi" 
                  v-model="price_range"
                >
                </v-range-slider>
              </v-card>
              <!-- Recherche par note -->
              <v-card flat outlined tile>
                <v-toolbar flat>
                  <v-icon color="black" class="mr-2">msi-chevron-down</v-icon>
                  <strong>NOTE</strong>
                  <v-spacer></v-spacer>
                  <v-icon 
                    color="grey" 
                    small
                    @click="reset('rating')"
                  >mdi-close</v-icon>
                </v-toolbar>
                <v-rating
                  v-model="rating"
                  bg-color="orange-lighten-1"
                  color="blue"
                ></v-rating>
                
              </v-card>
              <!-- Recherche par catégories -->
              <v-card flat outlined tile>
                <v-toolbar flat>
                  <v-icon color="black" class="mr-2">mdi-chevron-down</v-icon>
                  <strong>CATÉGORIES</strong>
                  <v-spacer></v-spacer>
                  <v-icon 
                    color="grey" 
                    small
                    @click="reset('chosenCategory')"
                  >mdi-close</v-icon>
                </v-toolbar>
                <v-list dense class="mt-n5">
                  <v-list-item v-for="categorie in categories" :key="categorie.title">
                    <v-list-item-content>
                      <v-list-item-title
                        v-text="categorie.title"
                        class="ml-8"
                        @click="filterCategory(categorie.title)"
                      ></v-list-item-title>
                    </v-list-item-content>
                    <v-list-item-action>
                      <v-list-item-subtitle
                        v-text="categorie.count"
                      ></v-list-item-subtitle>
                    </v-list-item-action>
                  </v-list-item>
                </v-list>
              </v-card>
            </v-col>
            <v-col cols="9" class="">
              <v-row >
                <v-col 
                  cols="12" 
                  md="4" 
                  v-for="(product, i) in this.sortedProducts" 
                  :key="i"
                  :class="((i + 1) % 3 !== 0) ? 'pa-0' : 'py-0 pl-0'"
                >
                  <v-card 
                    height="575"
                    flat outlined tile
                  >
                    <v-card-actions>
                      <v-spacer></v-spacer>
                      <v-btn color="black" small dark>{{ product.price }}€</v-btn>
                    </v-card-actions>
                    <v-img
                      :src="product.image"
                      width="200"
                      height="200"
                      contain
                    ></v-img>
                    <v-card-text class="">
                      <h4>{{ product.title }}</h4>
                      <p>{{ product.description }}</p>
                      <small>{{ product.category }}</small><br>
                      <small>Note : {{ product.rating.rate }} sur {{ product.rating.count }} avis</small>
                    </v-card-text>
                  </v-card>
                </v-col>
              </v-row>
            </v-col>
          </v-row>
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>

import axios from 'axios'
export default {
  name: 'App',
  data() {
    return {
      products: [],
      sortedProducts: [],
      rating: undefined,
      url: 'https://fakestoreapi.com/products',
      price_mini: 0,
      price_maxi: 1000,
      price_range: [0, 1000],
      categories: [],
      chosenCategory: undefined,
      orderBy: undefined
    }
  },
  watch: {
    price_range(newValue, oldValue) {
      this.onDataChange();
    },
    rating(newValue, oldValue) {
      this.onDataChange();
    },
    chosenCategory(newValue, oldValue) {
      this.onDataChange();
    },
    orderBy() {
      this.reOrder();
    }
  },
  methods: {
    reOrder() {
      switch(this.orderBy) {
        case 'Prix croissant':
          this.sortedProducts.sort((a, b) => a.price - b.price);
          break;
        case 'Prix décroissant':
          this.sortedProducts.sort((a, b) => b.price - a.price);
          break;
        case 'A-Z':
          this.sortedProducts.sort((a, b) => a.title.localeCompare(b.title));
          break;
        case 'Z-A':
          this.sortedProducts.sort((a, b) => b.title.localeCompare(a.title));
          break;
      }
    },
    filterCategory(element) {
      this.chosenCategory = element;
      //console.log(element);
    },
    onDataChange(variableName) {
      let newSortedProducts;
      this.sortedProducts = this.products;
      
      // Price range
      newSortedProducts = this.sortedProducts.filter(
        (produit) => produit.price >= this.price_range[0] && produit.price <= this.price_range[1]
      );
      this.sortedProducts = newSortedProducts;
      
      // Rating
      if (this.rating != undefined) 
      {
        newSortedProducts = this.sortedProducts.filter(
          (produit) => produit.rating.rate >= (this.rating-1) && produit.rating.rate <= this.rating
        );
        this.sortedProducts = newSortedProducts;
      }
      
      // Catégories
      if (this.chosenCategory != undefined) 
      {
        newSortedProducts = this.sortedProducts.filter(
          (produit) => produit.category == this.chosenCategory
        );
        this.sortedProducts = newSortedProducts;
      }
      this.reOrder();
    },
    reset(filter) {
      switch(filter) {
        case 'price_range': 
          this.price_range = [0, 1000];
          break;
        case 'rating':
          this.rating = undefined;
          break;
        case 'chosenCategory':
          this.chosenCategory = undefined;
          break;
        case 'all':
          this.price_range = [0, 1000];
          this.rating = undefined;
          this.chosenCategory = undefined;
          break;
      }
    },
  },
  mounted() {
    axios
      .get(this.url)
      .then(response => {
        this.products = response.data;
        this.sortedProducts = this.products;

        this.sortedProducts.forEach((produit) => {
          if (this.categories.length == 0) {
            this.categories.push({ title: produit.category, count: "1" });
          } else {
            let catIsFound = this.categories.find(category => category.title === produit.category);
            if (catIsFound) {
              catIsFound.count++;
            } else {
              this.categories.push({ title: produit.category, count: "1" });
            }
          }   
        });

      });
  }
};
</script>
