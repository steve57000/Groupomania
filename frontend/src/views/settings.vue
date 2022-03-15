<!-- eslint-disable -->
<template>
  <div class="container">
    <div class="delete" v-if="deleteAccount">
      <div class="wrapper fadeInDown">
        <div class="formContent">
          <h2 class="suppr" v-if="!modePassword">SUPPRIMER LE PROFIL</h2>
          <!-- Modif Form -->
          <form v-on:submit.prevent="supprimerCompte()">
            <input
              type="text"
              class="password"
              name="login"
              placeholder="Mot de passe"
              required
            />
            <div class="alert alert-danger app" role="alert" v-if="errorMdp">
              Mot de passe incorrect !
            </div>
            <button class="btn btn-danger mt-3 mb-5" @click="supprimerCompte()">
              SUPPRIMER
            </button>
          </form>
        </div>
      </div>
    </div>
    <div class="modifyBox" v-if="!deleteAccount">
      <div class="wrapper fadeInDown">
        <div class="formContent">
          <h2
            class="modif underlineHoverH2"
            v-if="modePassword"
            @click="switchTo()"
          >
            Modifer ton profil
          </h2>
          <h2
            class="modif underlineHoverH2"
            v-if="!modePassword"
            @click="switchTo()"
          >
            Modifer ton mot de passe
          </h2>
          <!-- Modif Form -->
          <form v-on:submit.prevent="modifier, changePassword">
            <input
              type="text"
              id="nom"
              name="nom"
              placeholder="Ton nom"
              v-if="!modePassword"
            />
            <input
              v-if="!modePassword"
              type="text"
              id="prenom"
              name="prenom"
              placeholder="Ton prenom"
            />
            <input
              v-if="!modePassword"
              type="text"
              id="desc"
              name="desc"
              placeholder="Poste chez Groupomania"
            />
            <p class="red app" v-if="long && !modePassword">
              Les champs doivent faire au moins 3 caractères.
            </p>
            <input
              v-if="modePassword"
              type="password"
              id="oldPassword"
              name="login"
              placeholder="Ancien mot de passe"
              required
            />
            <input
              v-if="modePassword"
              type="password"
              class="password"
              name="login"
              placeholder="Nouveau mot de passe"
              required
            />
            <p class="red" v-if="errorNewMdp && modePassword">
              Ton mot de passe doit contenir 8 caractères, une majuscule, une
              minuscule et un caractère spécial
            </p>
            <input
              v-if="!modePassword"
              @click="modifier()"
              type="submit"
              value="MODIFIER"
            />
            <input
              v-if="modePassword"
              @click="changePassword()"
              type="submit"
              value="CHANGER MOT DE PASSE"
            />
            <div class="alert alert-success app" role="alert" v-if="update">
              Utilisateur mis à jour !
            </div>
            <div class="alert alert-danger app" role="alert" v-if="errorMdp">
              Mot de passe incorrect !
            </div>
          </form>
          <div id="formFooter">
            <p @click="switchToDelete()" class="underlineHover" href="#">
              SUPPRIMER LE COMPTE
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<!-- eslint-disable -->
<script>
import axios from "axios";
const CryptoJS = require("crypto-js");

export default {
  data() {
    return {
      long: false,
      errorNewMdp: false,
      deleteAccount: false,
      oldPassword: null,
      modePassword: false,
      update: false,
      nom: null,
      prenom: null,
      password: null,
      desc: null,
      image: null,
      errorMdp: false,
      userId: document.cookie
        ? CryptoJS.AES.decrypt(
            document.cookie
              .split("; ")
              .find((row) => row.startsWith("userId="))
              .split("=")[1],
            this.$store.state.CryptoKey
          ).toString(CryptoJS.enc.Utf8)
        : null,
    };
  },
  methods: {
    supprimerCompte() {
      this.password = document.querySelector(".password")
        ? document.querySelector(".password").value
        : null;
      const self = this;
      axios
        .delete(
          `http://localhost:3000/api/user/delete/${self.userId}`,
          { data: { password: self.password } },
          {
            headers: {
              Authorization: `Bearer ${this.token}`,
            },
          }
        )
        .then(function (response) {
          console.log(response);
          document.cookie = "userId=";
          document.cookie = "user-token=";
          self.$router.go("");
        })
        .catch(function (error) {
          self.errorMdp = true;
          console.log(error);
        });
    },
    switchToDelete() {
      this.deleteAccount = true;
      this.modePassword = false;
    },
    switchTo() {
      this.modePassword
        ? (this.modePassword = false)
        : (this.modePassword = true);
    },
    upload(event) {
      this.image = event.target.files[0];
    },
    changePassword() {
      this.password = document.querySelector(".password")
        ? document.querySelector(".password").value
        : null;
      this.oldPassword = document.querySelector("#oldPassword")
        ? document.querySelector("#oldPassword").value
        : null;
      this.token = document.cookie
        .split("; ")
        .find((row) => row.startsWith("user-token="))
        .split("=")[1];
      let validPassword = new RegExp(
        /^(?=.*\d)(?=.*[a-z])(?=.*[A-Z])(?=.*[!@#$%^&*+.]).{8,}$/
      );
      if (validPassword.test(this.password)) {
        const self = this;
        axios
          .put(
            `http://localhost:3000/api/user/modifyPassword/${self.userId}`,
            {
              oldPassword: self.oldPassword,
              password: self.password,
            },
            {
              headers: {
                Authorization: `Bearer ${this.token}`,
              },
            }
          )
          .then(function (response) {
            console.log(response);
            self.update = true;
          })
          .catch(function (error) {
            self.errorMdp = true;
            self.update = false;
            console.log(error);
          });
      } else {
        console.log(false);
        this.errorNewMdp = true;
        this.update = false;
      }
    },
    modifier() {
      this.nom = document.querySelector("#nom")
        ? document.querySelector("#nom").value
        : null;
      this.prenom = document.querySelector("#prenom")
        ? document.querySelector("#prenom").value
        : null;
      this.desc = document.querySelector("#desc")
        ? document.querySelector("#desc").value
        : null;
      this.token = document.cookie
        .split("; ")
        .find((row) => row.startsWith("user-token="))
        .split("=")[1];
      let validName = new RegExp(
        /^[a-zA-Z]+[a-zA-Z]+[a-zA-Z]+(([',. -][a-zA-Z ])?[a-zA-Z]*){3,}$/
      );
      const self = this;
      if (
        validName.test(this.nom) ||
        validName.test(this.prenom) ||
        validName.test(this.desc)
      ) {
        axios
          .put(
            `http://localhost:3000/api/user/modifyAccount/${self.userId}`,
            {
              prenom: self.prenom,
              nom: self.nom,
              desc: self.desc,
            },
            {
              headers: {
                Authorization: `Bearer ${this.token}`,
              },
            }
          )
          .then(function (response) {
            console.log(response);
            self.update = true;
          })
          .catch(function (error) {
            console.log(error);
          });
      } else {
        self.long = true;
      }
    },
  },
  mounted() {
    if (!this.userId) {
      this.$router.push("/");
    }
  },
};
</script>
<!-- eslint-disable -->
<style lang="scss" scoped>
@import "../assets/css/settings.scss";
</style>
