<template>
    <div>
        <section>
            <specialOfert />
        </section>
        <div
            class="columns  is-three-quarters-mobile is-two-thirds-tablet is-half-desktop is-one-third-widescreen is-one-quarter-fullhd">
            <div class="column" v-if="condition2">
                <div class="hero caixa">
                    <h3 class="is-size-5 is-underlined">Cart List:</h3>
                    <table class="table">
                        <tbody v-for="(item, i) in cartFinal" :key="i">
                            <th class="is-italic">{{ item.product }}<strong></strong></th>
                            <td>${{ item.price }}</td>
                            <td>
                                <button class="button  is-small is-rounded is-light" @click="resta(item)">-</button>
                            </td>
                            <td>{{ item.amount }}</td>
                            <td>
                                <button class="button is-small is-rounded is-light" @click="suma(item)">+</button>
                            </td>
                            <td>${{ item.total }}</td>
                            <td>
                                <button class="button  is-small  is-light" @click="deleteItem(item)">X</button>
                            </td>

                        </tbody>
                    </table>
                    <div class="has-text-right mr-4">
                        <h1 class="is-size-6"><strong>Cost:</strong>$ {{ resultat }}</h1>

                        <h1 class="is-size-6 "><strong>Discount 15%:</strong> $ {{ discount }}</h1>

                        <h1 class="is-size-5"><strong>Total:</strong>$ <strong>{{ resultatDiscount }}</strong></h1>
                        <hr>
                        <div>
                            <button class="button button_add  mt-3" @click="showCheckOut()">Chekout</button>
                        </div>
                    </div>
                </div>
            </div>
            <div class="columns is-multiline is-centered is-mobile mt-5">
                <div v-for="(item, i) in getPosts" :key="i">
                    <div class="card m-3">
                        <div>
                            <div class="card-image">
                                <figure class="image" style="height:45vh;  width:40vh">
                                    <img :src="item.img" alt="image film">
                                </figure>
                            </div>
                            <div class="card-content">
                                <p class="is-size-6"> <strong> {{ item.text }}</strong></p>
                                <p class="is-size-6">Price: $ <strong>{{ item.price }}</strong> </p>
                                <button class="button button_add"
                                    @click="showProduct(item.price, item.text, item.amount, item.id), mostrar()">Add</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
import { mapGetters } from 'vuex';
import { mapState } from 'vuex';
import specialOfert from '@/components/specialOfert.vue'
export default {
    name: 'showStoreProducts',
    components: { specialOfert },
    data() {
        return {
            cart: [],
            result: [],
            cartFinal: [],
            totalAmount: 0,
            condition2: false,
            resultat: 0,
            resultatDiscount: 0,
            discount: 0,
            price: 0,
            product: '',
            amount: 1,
            total: 0,
            id: 0,
            found: false

        }
    },
    computed: {
        ...mapGetters(['getPosts']),
        ...mapState(['checkout']),
    },
    methods: {
        showProduct(itemPrice, itemText, itemAmount, itemId) {
            var cartProto = {
                price: itemPrice,
                product: itemText,
                amount: itemAmount,
                id: itemId,
                total: itemPrice,
            }

            this.cart.push(cartProto)
            let result = this.cart.map(element => element.id)
            let resultado = result.filter(item => item === itemId)
            if (resultado.length > 1) {
                this.found = true
            } else {
                this.found = false
            }
            if (this.found == true) {
                this.cartFinal.forEach((element) => {
                    if (element.id === itemId) {
                        element.amount++
                        element.total = element.price * element.amount
                    }
                })

            } else {
                this.cartFinal.push(cartProto)
                localStorage.setItem('cart', JSON.stringify(this.cartFinal));
            }
            this.calculTotal()
        },

        resta(item) {
            item.amount--
            if (item.amount == 0) {
                this.deleteItem(item)
            }
            item.total = item.price * item.amount
            this.calculTotal()
        },

        suma(item) {
            item.amount++
            item.total = item.price * item.amount
            this.calculTotal()

        },

        calculTotal() {
            let suma = this.cartFinal.map(element => element.total);
            this.resultat = suma.reduce((accu, item) => (accu + item), 0)
            if (this.resultat >= 100) {
                this.discount = this.resultat * 15 / 100
                this.resultatDiscount = this.resultat - this.discount
            } else {
                this.resultatDiscount = this.resultat
                this.discount = 0
            }

        },

        deleteItem(item) {
            let arr = this.cartFinal.indexOf(item)
            this.cartFinal.splice(arr, 1)
            alert(` ${item.product} will be removed from cart`)
            this.cart = []
            this.cart = this.cartFinal
            this.cart = [...this.cart]
            this.cartFinal = [...this.cartFinal];
            this.mostrar()
            this.calculTotal()
        },

        mounted() {
            if (localStorage.cart) this.cart = localStorage.cart;
        },
        showCheckOut() {
            this.$store.state.checkout.push(this.cartFinal)
            this.$router.push('CheckOut')
        },
        mostrar() {
            if (this.cartFinal.length >= 1) {
                this.condition2 = true
            } else {
                this.condition2 = false
            }
        }

    },
    watch: {
        name(newName) {
            localStorage.cart = newName;
        },
        resultatDiscount: function () {
            this.$emit('resultat', this.resultatDiscount)
        }
    }
}

</script>
<style scoped>
.button_add {
    background-color: rgb(3, 187, 46);
    color: white
}

.caixa {
    margin: 5vh;
}

.table {
    padding: 0;
    margin: 0;
}

.caixa {
    padding: 0;
    margin: 0;
}
</style>