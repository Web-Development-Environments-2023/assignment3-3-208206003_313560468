<template>
  <div>
    <div v-if="$root.store.username">
      <NotFound></NotFound>
    </div>
    <div v-else class="container">
      <h1 class="title" v-if="!$root.store.username">Login Page </h1>
      <b-form @submit.prevent="onLogin">
        <b-form-group id="input-group-Username" label-cols-sm="3" label="Username:" label-for="Username" >
          
          <b-form-input id="Username" v-model="$v.form.username.$model" type="text" :state="validateState('username')">
          </b-form-input>
          <b-form-invalid-feedback>
            Username is required
          </b-form-invalid-feedback>
        </b-form-group>

        <b-form-group id="input-group-Password" label-cols-sm="3" label="Password:" label-for="Password">
          <b-form-input id="Password" type="password" v-model="$v.form.password.$model"
            :state="validateState('password')"></b-form-input>
          <b-form-invalid-feedback>
            Password is required
          </b-form-invalid-feedback>
        </b-form-group>

        <b-button type="submit" variant="primary" style="width: 100px; display: block" class="mx-auto w-100">Login
        </b-button>
      </b-form>
      <b-alert class="mt-2" v-if="form.submitError" variant="warning" dismissible show>
        Login failed: {{ form.submitError }}
      </b-alert>
    </div>
    <div id="registerRequest" style="text-align: center; margin-right: 50%; margin-top: 5%;" >
      <h1 style="color:white"> Do not have an account yet?</h1>
      <b-button  style="max-width: 100px; background-color: green;">
        <router-link to="register" style="color: white;"> Join us !</router-link>
      </b-button>
    
    </div>
  </div>
</template>

<script>
import { required } from "vuelidate/lib/validators";
import NotFound from "../pages/NotFoundPage";
export default {
  components: {
    NotFound,
  },
  name: "Login",
  data() {
    return {
      form: {
        username: "",
        password: "",
        submitError: undefined,
      },
    };
  },
  validations: {
    form: {
      username: {
        required,
      },
      password: {
        required,
      },
    },
  },
  methods: {
    validateState(param) {
      const { $dirty, $error } = this.$v.form[param];
      return $dirty ? !$error : null;
    },
    async Login() {
      try {
        const response = await this.axios.post(
          this.$root.store.server_domain + "/login",
          {
            user_name: this.form.username,
            password: this.form.password,
          }
        );
        
     
        console.log(this.$root.store.login);

        await this.$root.store.produceUserData();
        this.$root.store.login(this.form.username);
        this.$router.push("/");
      } catch (err) {
        console.log(err.response);
        this.form.submitError = err.response.data.message;
      }
    },
    onLogin() {
     
      this.form.submitError = undefined;
      this.$v.form.$touch();
      if (this.$v.form.$anyError) {
        return;
      }
      

      this.Login();
    },
  },
};
</script>
<style lang="scss" scoped>
.container {
  max-width: 400px;
}

h1 {
  padding-top: 25px;
  padding-left: 25px;
  color: rgb(248, 248, 217);
  font-family: Frank Ruhl Libre, Georgia;

}
</style>
