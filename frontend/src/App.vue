<!-- eslint-disable -->
<template>
  <header>
    <div class="navigation">
      <nav
        class="navbar navbar-expand-sm navbar-light fixed-top nav d-flex justify-content-center flex-nav"
      >
        <a class="nav-link">
          <router-link to="/home">
            <div id="spanTitle">
              <img
                class="homeLinkBig"
                id="nav_groupomania"
                src="./assets/logoCircle.png"
                alt="lien acceuil"
              />
              <h1>
                <span class="spanLetterG">G</span>
                <span class="spanLetterR">r</span>
                <span class="spanLetterO">o</span>
                <span class="spanLetterU">u</span>
                <span class="spanLetterP">p</span>
                <span class="spanLetterOO">o</span>
                <span class="spanLetterM">m</span>
                <span class="spanLetterA">a</span>
                <span class="spanLetterN">n</span>
                <span class="spanLetterI">i</span>
                <span class="spanLetterAA">a</span>
              </h1>
            </div>
          </router-link>
        </a>
        <ul class="nav justify-content-center">
          <li class="nav-item" v-if="user">
            <a class="nav-link">
              <router-link :to="{ name: 'user', params: { userId: user.id } }">
                <img
                  v-bind:src="user.pp"
                  alt="pp"
                  class="userPageLink"
                  @click="refresh"
                />
              </router-link>
            </a>
          </li>
          <li class="nav-item" v-if="user">
            <a class="nav-link">
              <router-link to="/settings">
                <img
                  src="../image/user-cog-solid.svg"
                  alt="setting"
                  class="setting"
                />
              </router-link>
            </a>
          </li>
          <li class="nav-item" v-if="user">
            <a class="nav-link">
              <router-link to="/"
                ><img
                  @click="deconnexion"
                  src="../image/sign-out-alt-solid.svg"
                  alt="logout"
                  class="logout"
              /></router-link>
            </a>
          </li>
          <li class="nav-item" v-if="user">
            <router-link to="/"
              ><img
                @click="appRecherche"
                class="loupe"
                src="../image/search.svg"
                alt="Recherche"
            /></router-link>
          </li>
        </ul>
        <form class="form-inline my-2 my-lg-0" v-if="user">
          <div class="flex_input">
            <input
              class="form-control mr-sm-2 inputsearch"
              type="search"
              placeholder="Cherchez un utilisateur"
              aria-label="Search"
              @keyup="search"
              @click="stopSearch"
              @blur="stopSearch"
            />
            <img
              @click="dispRecherche"
              class="cross"
              src="../image/times-solid.svg"
              alt="close research"
            />
          </div>
          <div class="results" v-if="userSearch && research !== ''">
            <div v-for="result in userSearch" :key="result.id" class="result">
              <router-link
                :to="{ name: 'user', params: { userId: result.id } }"
              >
                <img
                  class="imgSearch"
                  v-bind:src="result.pp"
                  alt=""
                  srcset=""
                />
                <span class="nameSearch"
                  >{{ result.prenom }} {{ result.nom }}</span
                >
              </router-link>
            </div>
          </div>
        </form>
      </nav>
    </div>
  </header>
  <router-view :key="$route.fullPath" />
</template>
<!-- eslint-disable -->
<style lang="scss">
@import "./assets/css/App.scss";
</style>
<!-- eslint-disable -->
<script>
import axios from "axios";
const CryptoJS = require("crypto-js");

export default {
  data() {
    return {
      research: "",
      userSearch: null,
      user: null,
      token: document.cookie
        ? document.cookie
            .split("; ")
            .find((row) => row.startsWith("user-token="))
            .split("=")[1]
        : null,
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
    appRecherche() {
      document.querySelector(".inputsearch").style.display = "block";
      document.querySelector(".cross").style.display = "block";
      document.querySelector(".loupe").style.display = "none";
    },
    dispRecherche() {
      document.querySelector(".inputsearch").style.display = "none";
      document.querySelector(".cross").style.display = "none";
      document.querySelector(".loupe").style.display = "block";
    },
    stopSearch() {
      const self = this;
      setTimeout(function () {
        self.research = null;
        self.userSearch = null;
      }, 100);
    },
    search(event) {
      this.research = event.target.value;
      const self = this;
      axios
        .post(
          "http://localhost:3000/api/user/getAs",
          { nom: self.research },
          {
            headers: {
              Authorization: `Bearer ${self.token}`,
            },
          }
        )
        .then((response) => {
          self.userSearch = response.data;
        })
        .catch(function (error) {
          if (error.response && error.response.status === 403) {
            self.$router.push("/");
          }
        });
    },
    deconnexion() {
      document.cookie = "userId=";
      document.cookie = "user-token=";
      this.$router.go();
    },
    getCurrentUser() {
      const self = this;
      axios
        .post(
          "http://localhost:3000/api/user",
          { userId: self.userId },
          {
            headers: {
              Authorization: `Bearer ${self.token}`,
            },
          }
        )
        .then((response) => (self.user = response.data[0]))
        .catch(function (error) {
          if (error.response && error.response.status === 400) {
            document.cookie = "userId=";
            document.cookie = "user-token=";
            self.$router.push("/");
          }
        });
    },
  },
  mounted() {
    this.getCurrentUser();
    this.stopSearch();
  },
};
</script>

<style lang="scss" scoped>
@import "./assets/css/App.scss";
</style>