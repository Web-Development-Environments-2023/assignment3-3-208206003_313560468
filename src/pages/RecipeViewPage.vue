<template>
  <div>
    <RecipeView :recipe="recipe" :logged_in="$root.store.username"></RecipeView>
  </div>
</template> 


<script>
import RecipeView from "../components/RecipeView.vue";

export default {
  data() {
    return {
      recipe: null,
      watchIngredients: false,
    };
  },
  async created() {
    let response;
    try {
      response = await this.axios.get(
        this.$root.store.server_domain +
          "/recipes/details/" +
          this.$route.params.recipeId
      );
    } catch (error) {
      console.log("error.response.status", error.response.status);
      this.$router.replace("/NotFound");
      return;
    }
    let {
      id,
      instructions,
      ingredients,
      popularity,
      readyInMinutes,
      image,
      title,
      vegan,
      vegetarian,
      glutenFree,
      servings,
    } = response.data;
    let _recipe = {
      id,
      instructions,
      ingredients,
      popularity,
      readyInMinutes,
      image,
      title,
      vegan,
      vegetarian,
      glutenFree,
      servings,
    };
    this.recipe = _recipe;
    if (!this.$route.params.user_recipe && this.$root.store.username) {
      this.$root.store.addToUserLastWatched(this.recipe);
    }
    if (localStorage.getItem("userFavoriteRecipes")) {
      let favorites = JSON.parse(localStorage.getItem("userFavoriteRecipes"));
      for (let i = 0; i < favorites.length; i++) {
        if (favorites[i].id === this.recipe.id) {
          this.recipe.favorite = true;
          return;
        }
      }
    }
    this.recipe.favorite = false;
  },
  components: {  RecipeView },
};
</script>

<style scoped>
.wrapper {
  display: flex;
}

.wrapped {
  width: 50%;
}

.center {
  display: block;
  margin-left: auto;
  margin-right: auto;
  width: 50%;
}

.Cards {
  padding: 7px;
}

h1 {
  padding-top: 25px;
  padding-left: 25px;
  color: rgb(248, 248, 217);
  font-family: Frank Ruhl Libre, Georgia;
}

/* .recipe-header{

} */
</style>
