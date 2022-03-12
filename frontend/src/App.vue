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

<style lang="scss">
$shadowBlue: rgba(234, 184, 96);
$shadowGrey: rgba(16, 12, 16);
$shadowBox: 0 0 20px 0 $shadowGrey;
$shadowBoxHover: 0 0 20px 1px $shadowBlue;
$lineargradient: linear-gradient(
    217deg,
    rgba(20, 20, 20),
    rgba(171, 164, 164) 70.71%
  ),
  linear-gradient(127deg, rgba(77, 75, 75), rgba(94, 91, 91) 70.71%),
  linear-gradient(336deg, rgba(59, 52, 52), rgba(84, 77, 77) 70.71%);
$conicGradient: conic-gradient(
  at -30% 50%,
  rgba(59, 52, 52, 0.85) 18%,
  rgba(77, 75, 75, 0.5) 30%,
  rgba(20, 20, 20, 0.85) 40%
);

h1 {
  font-family: "Gill Sans", sans-serif;
  font-size: 1.6em;
  color: white;
  text-shadow: 0 0 10px rgba(0, 0, 0.61);
  padding-top: 10px;
}

#spanTitle {
  display: flex;
  justify-content: center;
  align-items: center;
}
.nav-link {
  display: flex;
  flex-direction: row;
}
.inputsearch {
  background-color: #f0f2f5;
  border-radius: 20px;
  text-align: center;
}
.nameSearch {
  text-decoration: none;
  color: rgba(229, 223, 223, 0.61);
}
a:link {
  text-decoration: none;
}
.imgSearch {
  object-fit: cover;
  height: 45px;
  width: 45px;
  margin: 10px;
  border-radius: 100%;
}
.results {
  position: absolute;
}
.result {
  width: 221px;
  border: 1px solid #dbdbdb;
  background-color: white;
  &:hover {
    background-color: #90b3d6;
  }
}
.navigation {
  min-height: 64px;
}
nav ul li:hover {
  transition: 0.3s;
  transform: scale(1.1);
}
.userPageLink {
  object-fit: cover;
  height: 40px;
  width: 40px;
  margin-top: 0;
  margin-bottom: 0;
  margin-right: 40px;
  border-radius: 100%;
}

.signup {
  box-sizing: content-box;
  background: $conicGradient;
}
.homeLinkBig {
  width: 41px;
  margin-right: 10px;
  transition: transform 2s;
}
.homeLinkBig:hover {
  transform: rotate3d(1, 2.5, 1.5, 360deg);
}

.setting {
  height: 32px;
}
.logout {
  height: 30px;
  width: 80px;
}
.navbar {
  background: $lineargradient;
  height: auto;
  box-shadow: 0 0 15px 0 rgba(239, 229, 229, 0.61);
  transition: width 1s, height 1s, background-color 1s, transform 1s;
  &:hover {
    box-shadow: $shadowBoxHover;
    span {
      opacity: 0;
    }
    .spanLetterG {
      animation: letters 2s ease 300ms forwards;
      text-shadow: 0 0 20px grey;
    }
    .spanLetterR {
      animation: letters 2s ease 500ms forwards;
      text-shadow: 0 0 20px grey;
    }
    .spanLetterO {
      animation: letters 2s ease 100ms forwards;
      text-shadow: 0 0 20px white;
    }
    .spanLetterU {
      animation: letters 2s ease 150ms forwards;
      text-shadow: 0 0 20px grey;
    }
    .spanLetterP {
      animation: letters 2s ease 200ms forwards;
      text-shadow: 0 0 20px white;
    }
    .spanLetterOO {
      animation: letters 2s ease 250ms forwards;
      text-shadow: 0 0 20px grey;
    }
    .spanLetterM {
      animation: letters 2s ease 300ms forwards;
      text-shadow: 0 0 20px grey;
    }
    .spanLetterA {
      animation: letters 2s ease 120ms forwards;
      text-shadow: 0 0 20px white;
    }
    .spanLetterN {
      animation: letters 2s ease 100ms forwards;
      text-shadow: 0 0 20px white;
    }
    .spanLetterI {
      animation: letters 2s ease 110ms forwards;
      text-shadow: 0 0 20px grey;
    }
    .spanLetterAA {
      animation: letters 2s ease 100ms forwards;
      text-shadow: 0 0 20px white;
    }
  }
}
@keyframes letters {
  0% {
    opacity: 0;
    color: black;
  }
  20% {
    opacity: 1;
    color: grey;
  }
  40% {
    opacity: 0;
    color: white;
  }
  60% {
    opacity: 1;
    color: grey;
  }
  80% {
    opacity: 0;
    color: black;
  }
  100% {
    opacity: 1;
    color: white;
  }
}
body {
  margin: 0;
  background-attachment: fixed;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #374a6b;
}

* {
  box-sizing: border-box;
}

@media only screen and (min-width: 1000px) {
  .cross {
    display: none;
    height: 30px;
    position: absolute;
  }
}

@media only screen and (max-width: 1000px) {
  .userPageLink {
    margin: 0;
  }
  .loupe {
    height: 30px;
    margin-top: 8px;
    margin-left: 10px;
    &:hover {
      transition: 0.3s;
      transform: scale(1.1);
    }
  }
  .inputsearch {
    display: none;
  }
  .cross {
    height: 35px;
    margin-left: 20px;
    display: none;
  }
  .flex_input {
    display: flex;
  }
}

@media only screen and (max-width: 570px) {
  .navbar {
    height: 120px;
  }
  .homeLinkBig {
    margin-bottom: 15px;
  }
  li {
    margin-top: -15px;
  }
}
</style>

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
