<script>
export default { 
    data() {
    return {
        categoriesId: null,
    }
  },
    props: {
        categories: {
            type: Array,
            required: true,
        }

    },
    watch: {
        categories() {
            this.categoriesId =this.categories.length
            ? this.categories[0].id
            : null;
        }

    },
  
 
}
</script>

<template>
    <form>
        <h2>Filtra in base alle categorie</h2>
        <label for="categories">categorie: </label>
        <select class="form-select" id="categories" @change="$emit('changeCategories', categoriesId)" v-model="categoriesId">

            <option v-for="item in categories" 
            value=""
            :key="item.id"
            :value="categories.id">
            {{ categories.title }}
                
            </option>
        </select>





    </form>
    <!-- <div v-for="item in arrCategories" :key="item.id">
    <div>
      {{ item.title }}
    </div>
  </div> -->
        
    
</template>
<style>
</style>