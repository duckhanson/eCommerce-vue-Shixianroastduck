<template>
    <div class="page-category">
        <LoadingCircle/>
        <div class="columns is-multiline">
            <div class="column is-12">
                <h2 class="is-size-2 has-text-centered">{{ category.name }}</h2>
            </div>

            <ProductBox 
                v-for="product in category.products"
                v-bind:key="product.id"
                v-bind:product="product" />
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import { toast } from 'bulma-toast'
import LoadingCircle from '@/components/LoadingCircle'
import ProductBox from '@/components/ProductBox'

export default {
    name: 'Category',

    components: {
        LoadingCircle,
        ProductBox,
    },

    data() {
        return {
            category: {
                products: []
            }
        }
    },

    mounted() {
        this.getCategory()
    },

    watch: {
        $route(to, from) {
            if (to.name === 'Category') {
                this.getCategory()
            }
        }
    },

    methods: {
        async getCategory() {
            this.$store.commit('setIsLoading', true)
            const category_slug = this.$route.params.category_slug
            console.log(`/api/v1/products/${category_slug}/`)
            await axios
                .get(`/api/v1/products/${category_slug}/`)
                .then(response => {
                    this.category = response.data
                    document.title = this.category.name + ' | ' + this.$store.state.abb_store_name
                })
                .catch(error => {
                    console.log(error)
                    toast({
                        message: 'Something went wrong. Please try again.',
                        type: 'is-danger',
                        dismissible: false,
                        pauseOnHover: true,
                        duration: 2000,
                        position: 'bottom-right',
                        closeOnClick: true,
                    })
                })
            this.$store.commit('setIsLoading', false)
        },
    },
}
</script>