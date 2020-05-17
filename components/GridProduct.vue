<template>
  <div class="product">
    <nuxt-link :to="'/products/' + productData.handle">
      <img :src="productData.images.edges[0].node.transformedSrc">
      <div class="product__slate">
        <p>{{ productData.title }}</p>
      </div>
    </nuxt-link>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import VueApollo from 'vue-apollo'
import gql from 'graphql-tag'

export default Vue.extend({
  name: 'GridProduct',
  props: {
    productData: Object
  },
  data() {
    return {
      isAdding: false
    }
  },
  methods: {
    async addToCart () {
      console.log(`Adding ${this.productData.title} to cart...`)
      this.isAdding = true
      let result = await this.$apollo.mutate({
        mutation: gql`
          mutation ($checkoutId: ID!, $lineItems: [CheckoutLineItemInput!]!) {
            checkoutLineItemsAdd(checkoutId: $checkoutId, lineItems: $lineItems) {
              userErrors {
                message
                field
              }
              checkout {
                id
              }
            }
          }
        `,
        variables: {
          checkoutId: this.$store.state.checkoutId,
          lineItems: [{variantId: this.productData.variant.node.id, quantity: 1}]
        }
      })
      this.isAdding = false
      console.log(`Added ${this.productData.title} to cart.`)
      this.$store.dispatch('fetchCart')
    }
  }
})
</script>

<style lang="scss">
@import "~assets/scss/variables.scss";

.product {
  border-bottom: 1px solid #fff;
  display: flex;
  flex-direction: column;
  flex: 33%;
  margin: 0 10px 15px 0;
  padding-bottom: 15px;
  max-width: 50%;

  img {
    width: 100%;
  }
}

.product__slate {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  margin-top: 10px;
}

</style>