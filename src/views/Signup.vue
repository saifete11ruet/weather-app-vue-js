<template>
  <v-app id="inspire">
    <v-main>
      <v-container fluid fill-height>
        <v-layout align-center justify-center>
          <v-flex xs12 sm8 md4>
            <v-card class="elevation-12 signup-card">
              <!-- <v-toolbar dark color="primary">
                <v-toolbar-title>Signup</v-toolbar-title>
              </v-toolbar> -->
              <v-card-text>
                <h2 class="signup-text">Sign Up</h2>
                <v-alert
                  v-model="alertSuccess"
                  border="left"
                  close-text="Close Alert"
                  color="success"
                  dark
                  dismissible
                >
                  Thanks for registering with us
                </v-alert>
                <v-alert
                  v-model="alertFailed"
                  border="left"
                  close-text="Close Alert"
                  color="red"
                  dark
                  dismissible
                >
                  Registration failed. Please try again
                </v-alert>
                <v-form ref="form" v-model="valid" lazy-validation>
                  <v-text-field
                    prepend-icon="mdi-account"
                    name="name"
                    label="Name"
                    type="text"
                    v-model="name"
                    :rules="nameRules"
                    required
                  ></v-text-field>
                  <v-text-field
                    prepend-icon="mdi-phone"
                    name="phone"
                    label="Phone (Ex. 01XXXXXXXXX)"
                    type="text"
                    v-model="phone"
                    :rules="phoneRules"
                    required
                  ></v-text-field>
                  <v-text-field
                    id="password"
                    prepend-icon="mdi-lock"
                    name="password"
                    label="Password"
                    :type="show ? 'text' : 'password'"
                    :append-icon="show ? 'mdi-eye' : 'mdi-eye-off'"
                    @click:append="show = !show"
                    v-model="password"
                    :rules="passwordRules"
                    required
                  ></v-text-field>
                </v-form>
              </v-card-text>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn
                  class="mb-4"
                  color="primary"
                  :disabled="!name || !phone || !password || !valid"
                  :loading="loading"
                  @click="signup"
                >
                  Sign Up
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-flex>
        </v-layout>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
  export default {
    name: "Signup",
    data: () => ({
      valid: true,
      show: false,
      loading: false,
      alertSuccess: false,
      alertFailed: false,
      name: "",
      nameRules: [
        (v) => !!v || "Name is required",
        (v) => (v && v.length <= 30) || "Name must be less than 30 characters",
      ],
      phone: "",
      phoneRules: [
        (v) => !!v || "Phone is required",
        (v) => /^01{1}\d{9}$/.test(v) || "Phone must be valid",
      ],
      password: "",
      passwordRules: [
        (v) => !!v || "Password is required",
        (v) =>
          /^(?=.{8,})(?=.*[0-9])(?=.*[~`!@#$%^&*)(+=._-]).*$/.test(v) ||
          "Password must be Minimum 8 characters including 1 number and 1 special character",
      ],
    }),

    methods: {
      signup() {
        this.$refs.form.validate();
        if (this.valid) {
          this.loading = true;
          localStorage.setItem("name", this.name);
          localStorage.setItem("phone", this.phone);
          this.alertSuccess = true;
          this.$refs.form.reset();
          this.loading = false;
        }
      },
    },
  };
</script>

<style scoped>
  div#inspire {
    background-image: url("../assets/signup-bg.jpg");
    background-size: cover;
  }
  h2.signup-text {
    text-align: center;
    margin: 34px 0;
    font-size: 34px;
  }
  .signup-card {
    background-color: rgba(255, 255, 255, 0.6) !important;
    padding: 12px;
  }
</style>
