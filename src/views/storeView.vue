<template>
    <div>
        <showStoreProducts @resultat="handleresultat"/>       
    </div>
</template>
<script>
import showStoreProducts from '@/components/showStoreProducts.vue';
export default {   
    name: 'storeView',
    components:{showStoreProducts},
    data (){
        return{
            resultatFinal:0
        }
    },
    methods:{
        handleresultat (value){
            this.resultatFinal = value
        }

    }

}
</script>