<template>
  <div>
    <h1>Products</h1>
    <div v-for="product in products.products.edges" :key="product.node.id">
      <p>{{ product.node.title }}</p>
      <ul>
          <li v-for="variant in product.node.variants.edges" :key="variant.node.id">
              {{ variant.node.title }}
          </li>
      </ul>
      <img :src="product.node.media.edges[0].node.image.originalSrc" alt="" />
    </div>
  </div>
</template>

<script>
import ProductsList from '~/components/ProductsList.vue'
import { gql } from 'nuxt-graphql-request';

export default {
  components: {
    ProductsList,
  },
  data() {
      return {
          products: []
      }
  },
  async fetch() {
      const query = gql`
          query productQuery($first: Int) {
            products(first: $first) {
              edges {
                node {
                  id
                  ... on Product {
                    title,
                    variants(first: 5) {
                        edges {
                            node {
                                id,
                                title
                            }
                        }
                    },
                    media(first: 10) {
                        edges {
                          node {
                            mediaContentType,
                            alt,
                            ...on MediaImage {
                                image {
                                  originalSrc
                                }
                              }
                           }
                        }
                    }
                  }
                }
              }
            }
          }
        `;

      const variables = { first: 5 };

      this.products = await this.$graphql.shopify.request(query, variables);

      console.log(this.products);
    }
};
</script>