<template>
    <div class="mt-6">
        <div>
            <section class="columns">
                <div class="column">
                    <img src="@/assets/mononoke-sentada.png" style="height:max-content;">

                </div>
                <div class="column border is-rounded m-5">
                    <div class="box">
                        <p class="title">
                            Create user:
                        </p>
                        <form @submit.prevent="register">
                            <input v-model="usuario" class="input is-rounded m-2" required type="email"
                                placeholder="Enter your email">
                            <input v-model="contrasena" class="input is-rounded m-2" required type="Password"
                                placeholder="Enter your password">
                            <button class="button mt-4 is-warning" type="submit">Create</button>
                        </form>
                    </div>
                </div>
            </section>
        </div>
    </div>
</template>

<script>
import { getAuth, createUserWithEmailAndPassword } from "firebase/auth";
import { mapState } from "vuex";
export default {
    name: 'RegistePage',
    data() {
        return {
            usuario: '',
            contrasena: ''
        }
    },

    computed: {
        ...mapState(['condition'])
    },
    methods: {
        register() {
            const auth = getAuth();
            createUserWithEmailAndPassword(auth, this.usuario, this.contrasena)
                .then((userCredential) => {
                    // Signed in
                    const user = userCredential.user;
                    this.$store.state.condition = false
                    this.$router.push('storeView')
                    alert('User created successfully')
                    // ...
                })
                .catch((error) => {
                    const errorCode = error.code;
                    const errorMessage = error.message;
                    switch (errorCode) {
                        case 'auth/weak-password':
                            alert(' Password should be at least 6 characters')
                            break
                        case 'auth/email-already-in-use':
                            alert('Email already in use')
                            break
                        default:
                            alert('Something went wrong')

                    }
                    // ..
                });
        }
    }
}
</script>
<style>
.reg {
    margin-top: 10vh;
}
</style>
