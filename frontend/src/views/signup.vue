<!-- eslint-disable -->
<template>
  <div class="signup">
    <div class="wrapper fadeInDown">
      <div class="formContent">
        <!-- Tabs Titles -->
        <h2 @click="switchToSignup" v-if="mode == 'signup'" class="active">
          Sign Up
        </h2>
        <h2
          @click="switchToSignup"
          v-if="mode == 'login'"
          class="inactive underlineHover"
        >
          Sign Up
        </h2>
        <h2 @click="switchToLogin" v-if="mode == 'login'" class="active">
          Login
        </h2>
        <h2
          @click="switchToLogin"
          v-if="mode == 'signup'"
          class="inactive underlineHover"
        >
          Login
        </h2>
        <!-- Login Form -->
        <form v-on:submit.prevent="login">
          <input
            v-if="mode == 'signup'"
            type="text"
            id="nom"
            name="nom"
            placeholder="Ton nom"
            required
          />
          <p v-if="errorNom && mode == 'signup'" class="red">
            Ton nom doit contenir au moins 3 caractères
          </p>
          <input
            v-if="mode == 'signup'"
            v-model="spanName"
            type="text"
            id="prenom"
            name="prenom"
            placeholder="Ton prenom"
            required
          />
          <p v-if="errorPrenom && mode == 'signup'" class="red">
            Ton prénom doit contenir au moins 3 caractères
          </p>
          <input
            v-if="mode == 'signup'"
            type="text"
            id="email"
            name="email"
            placeholder="Ton email"
            required
          />
          <p v-if="errorEmail && mode == 'signup'" class="red">
            Email invalide
          </p>
          <p v-if="sameEmail && mode == 'signup'" class="red">
            Cet Email est déja utilisé.
          </p>
          <input
            v-if="mode == 'signup'"
            type="password"
            class="password"
            name="login"
            placeholder="password"
            required
          />
          <input
            v-if="mode == 'login'"
            type="text"
            id="emailCheck"
            name="email"
            placeholder="Ton email"
            required
          />
          <input
            v-if="mode == 'login'"
            type="password"
            id="passwordCheck"
            name="login"
            placeholder="password"
            required
          />
          <p v-if="errorMdp && mode == 'signup'" class="red">
            Ton mot de passe doit contenir 8 caractères, une majuscule, une
            minuscule et un caractère spécial
          </p>
          <input
            v-if="mode == 'signup'"
            @click="signup"
            type="submit"
            value="INSCRIPTION"
          />
          <input
            v-if="mode == 'login'"
            @click="login"
            type="submit"
            value="CONNEXION"
          />
        </form>
        <div v-if="incorrect == true && mode == 'login'" class="error">
          <p>Email ou mot de passe incorrect</p>
        </div>
        <div id="formFooter">
          <p class="underlineHover">Bienvenue <span id="spanName">{{ spanName }}</span> ! </p>
        </div>
      </div>
    </div>
  </div>
</template>
<!-- eslint-disable -->
<style lang="scss" scoped>
@import "../assets/css/signup.scss";
</style>
<!-- eslint-disable -->
<script>
import axios from "axios";
const CryptoJS = require("crypto-js");

export default {
  data() {
    return {
      sameEmail: false,
      mode: "signup",
      email: "",
      errorEmail: false,
      password: "",
      errorMdp: false,
      nom: "",
      errorNom: false,
      prenom: "",
      errorPrenom: false,
      incorrect: false,
      spanName: "",
    };
  },
  methods: {
    switchToSignup() {
      this.mode = "signup";
    },
    switchToLogin() {
      this.mode = "login";
    },
    validation() {
      let validEmail = new RegExp(
        /^(([^<>()\]\\.,;:\s@"]+(\.[^<>()\]\\.,;:\s@"]+)*)|(".+"))@(([0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
      );
      let validName = new RegExp(
        /^[a-zA-Z]+[a-zA-Z]+[a-zA-Z]+(([',. -][a-zA-Z ])?[a-zA-Z]*){3,}$/
      );
      let validPassword = new RegExp(
        /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?])[A-Za-z\d@$!%*?]{8,}$/
      );
      validEmail.test(this.email)
        ? (this.errorEmail = false)
        : (this.errorEmail = true);
      validName.test(this.nom)
        ? (this.errorNom = false)
        : (this.errorNom = true);
      validName.test(this.prenom)
        ? (this.errorPrenom = false)
        : (this.errorPrenom = true);
      validPassword.test(this.password)
        ? (this.errorMdp = false)
        : (this.errorMdp = true);
      console.log(validPassword.test(this.password));
      return (
        validEmail.test(this.email) &&
        validName.test(this.nom) &&
        validName.test(this.prenom) &&
        validPassword.test(this.password)
      );
    },
    signup() {
      this.nom = document.querySelector("#nom").value;
      this.prenom = document.querySelector("#prenom").value;
      this.password = document.querySelector(".password").value;
      this.email = document.querySelector("#email").value;
      if (this.validation()) {
        const self = this;
        axios
          .post("http://localhost:3000/api/user/signup", {
            nom: this.nom,
            prenom: this.prenom,
            email: this.email,
            password: this.password,
          })
          .then(function (response) {
            console.log(response);
            axios
              .post("http://localhost:3000/api/user/login", {
                email: self.email,
                password: self.password,
              })
              .then(function (response) {
                const token = response.data.token;
                const num = response.data.userId;
                const userId = CryptoJS.AES.encrypt(
                  num.toString(),
                  self.$store.state.CryptoKey
                ).toString();
                document.cookie = `user-token=${token}; SameSite=Lax; Secure; max-age=86400;`;
                document.cookie = `userId=${userId}; SameSite=Lax; Secure; max-age=86400;`;
                self.$router.push("/home");
                self.$router.go();
              })
              .catch(function (error) {
                if (error) {
                  self.incorrect = true;
                }
              });
          })
          .catch(function (error) {
            console.log(error);
            self.sameEmail = true;
          });
      }
    },
    login() {
      this.password = document.querySelector("#passwordCheck").value;
      this.email = document.querySelector("#emailCheck").value;
      const self = this;
      axios
        .post("http://localhost:3000/api/user/login", {
          email: this.email,
          password: this.password,
        })
        .then(function (response) {
          const token = response.data.token;
          const num = response.data.userId;
          const userId = CryptoJS.AES.encrypt(
            num.toString(),
            self.$store.state.CryptoKey
          ).toString();
          document.cookie = `user-token=${token}; SameSite=Lax; Secure; max-age=86400;`;
          document.cookie = `userId=${userId}; SameSite=Lax; Secure; max-age=86400;`;
          self.$router.push("/home");
          self.$router.go();
        })
        .catch(function (error) {
          if (error) {
            self.incorrect = true;
          }
        });
    },
  },
  mounted() {
    if (document.cookie) {
      const userIdCookie = document.cookie
        .split("; ")
        .find((row) => row.startsWith("userId="))
        .split("=")[1];
      console.log(userIdCookie);
      if (userIdCookie) {
        this.$router.push("/home");
      }
    }
  },
};
</script>
