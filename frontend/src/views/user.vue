<!-- eslint-disable -->
<template>
  <div class="home">
    <div class="container">
      <div v-if="currentUser" class="infoUser col-12">
        <div class="own" v-if="userId == currentUser.id">
          <label for="image">
            <img
              class="ppChange"
              v-bind:src="currentUser.pp"
              alt="pp"
              srcset=""
            />
            <img
              class="file"
              src="../../image/file-image-regular.svg"
              alt=""
              srcset=""
            />
          </label>
          <input
            @change="modifPP"
            type="file"
            name="image"
            id="image"
            style="display: none"
          />
        </div>
        <div v-else>
          <img class="ppFirst" v-bind:src="currentUser.pp" alt="pp" srcset="" />
        </div>
        <div class="text-user">
          <p class="userName">{{ currentUser.prenom }} {{ currentUser.nom }}</p>
          <p v-if="currentUser.desc" class="metier">
            <strong>Métier :</strong> {{ currentUser.desc }}
          </p>
        </div>
      </div>
      <div v-if="first">
        <div
          v-for="post in posts"
          :key="post.postId"
          class="d-flex justify-content-center"
        >
          <div class="bg-white mt-2 mb-2 col-sm-8 col-lg-6 posts">
            <div>
              <div
                class="d-flex flex-row justify-content-between align-items-center p-2 border-bottom infopost"
              >
                <div class="d-flex flex-row align-items-center px-2">
                  <router-link
                    :to="{ name: 'user', params: { userId: post.authorId } }"
                  >
                    <img
                      class="rounded-circle pp"
                      v-bind:src="post.pp"
                      width="45"
                      alt=""
                    />
                  </router-link>

                  <div class="d-flex flex-column flex-wrap ml-2">
                    <span class="font-weight-bold nomUser"
                      >{{ post.prenom }} {{ post.nom }}</span
                    ><span class="text-black-50"
                      >Posté le {{ formatDate(post.date) }}</span
                    >
                  </div>
                  <img
                    class="deletePost"
                    src="../../image/times-solid.svg"
                    alt="supprimer"
                    v-if="post.authorId == userId || (user && user.admin)"
                    @click="deletePost(post.postId, post.authorId)"
                  />
                </div>
              </div>
            </div>
            <div v-if="post.text != ' '">
              <span class="text">{{ post.text }}</span>
            </div>
            <div v-if="post.imageUrl" class="mb-2">
              <img
                class="img-fluid img-responsive imagePost"
                v-bind:src="post.imageUrl"
                alt=""
              />
            </div>
            <div class="react">
              <div class="like" @click="liked">
                <svg
                  @click="like(post.postId)"
                  v-if="likedPost.includes(post.postId)"
                  aria-label="Je n’aime plus"
                  class="_8-yf5 heart"
                  color="#ed4956"
                  fill="#ed4956"
                  height="30"
                  role="img"
                  viewBox="0 0 48 48"
                  width="24"
                >
                  <path
                    d="M34.6 3.1c-4.5 0-7.9 1.8-10.6 5.6-2.7-3.7-6.1-5.5-10.6-5.5C6 3.1 0 9.6 0 17.6c0 7.3 5.4 12 10.6 16.5.6.5 1.3 1.1 1.9 1.7l2.3 2c4.4 3.9 6.6 5.9 7.6 6.5.5.3 1.1.5 1.6.5s1.1-.2 1.6-.5c1-.6 2.8-2.2 7.8-6.8l2-1.8c.7-.6 1.3-1.2 2-1.7C42.7 29.6 48 25 48 17.6c0-8-6-14.5-13.4-14.5z"
                  ></path>
                </svg>
                <svg
                  @click="like(post.postId)"
                  v-else
                  aria-label="J’aime"
                  class="_8-yf5 svg-react"
                  color="#262626"
                  fill="#262626"
                  height="30"
                  role="img"
                  viewBox="0 0 48 48"
                  width="24"
                >
                  <path
                    d="M34.6 6.1c5.7 0 10.4 5.2 10.4 11.5 0 6.8-5.9 11-11.5 16S25 41.3 24 41.9c-1.1-.7-4.7-4-9.5-8.3-5.7-5-11.5-9.2-11.5-16C3 11.3 7.7 6.1 13.4 6.1c4.2 0 6.5 2 8.1 4.3 1.9 2.6 2.2 3.9 2.5 3.9.3 0 .6-1.3 2.5-3.9 1.6-2.3 3.9-4.3 8.1-4.3m0-3c-4.5 0-7.9 1.8-10.6 5.6-2.7-3.7-6.1-5.5-10.6-5.5C6 3.1 0 9.6 0 17.6c0 7.3 5.4 12 10.6 16.5.6.5 1.3 1.1 1.9 1.7l2.3 2c4.4 3.9 6.6 5.9 7.6 6.5.5.3 1.1.5 1.6.5.6 0 1.1-.2 1.6-.5 1-.6 2.8-2.2 7.8-6.8l2-1.8c.7-.6 1.3-1.2 2-1.7C42.7 29.6 48 25 48 17.6c0-8-6-14.5-13.4-14.5z"
                  ></path>
                </svg>
                <span class="nbr">{{ post.like }}</span>
              </div>
              <div class="comment">
                <svg
                  @click="afficherComment"
                  aria-label="Commenter"
                  class="_8-yf5 svg-react"
                  color="#262626"
                  fill="#262626"
                  height="24"
                  role="img"
                  viewBox="0 0 48 48"
                  width="24"
                >
                  <path
                    clip-rule="evenodd"
                    d="M47.5 46.1l-2.8-11c1.8-3.3 2.8-7.1 2.8-11.1C47.5 11 37 .5 24 .5S.5 11 .5 24 11 47.5 24 47.5c4 0 7.8-1 11.1-2.8l11 2.8c.8.2 1.6-.6 1.4-1.4zm-3-22.1c0 4-1 7-2.6 10-.2.4-.3.9-.2 1.4l2.1 8.4-8.3-2.1c-.5-.1-1-.1-1.4.2-1.8 1-5.2 2.6-10 2.6-11.4 0-20.6-9.2-20.6-20.5S12.7 3.5 24 3.5 44.5 12.7 44.5 24z"
                    fill-rule="evenodd"
                  ></path>
                </svg>
                <span class="nbr">{{ post.comment }}</span>
              </div>
            </div>
            <div class="block-com disp">
              <div
                class="comments"
                v-for="comment in comments"
                :key="comment.idComment"
              >
                <div class="pp" v-if="post.postId == comment.postId">
                  <router-link
                    :to="{ name: 'user', params: { userId: comment.authorId } }"
                  >
                    <img
                      class="rounded-circle pp2"
                      v-bind:src="comment.pp"
                      alt=""
                      srcset=""
                    />
                  </router-link>
                </div>
                <div v-if="post.postId == comment.postId" class="commentaire">
                  <span class="commentAuthor"
                    >{{ comment.prenom }} {{ comment.nom }}</span
                  >
                  <p class="commentText">{{ comment.comment }}</p>
                  <img
                    class="delete"
                    src="../../image/times-solid.svg"
                    alt="supprimer"
                    v-if="comment.id == userId || (user && user.admin)"
                    @click="
                      deleteComment(
                        comment.idComment,
                        comment.authorId,
                        post.postId
                      )
                    "
                  />
                </div>
              </div>
            </div>
            <div class="col-10 d-flex justify-content-center comment">
              <input
                v-on:keyup.enter="comment($event, post.postId)"
                @change="upload"
                type="text"
                class="form-control mt-3 mb-3"
                id="comment"
                aria-describedby="comment"
                placeholder="Ajoutez un commentaire ..."
              />
            </div>
          </div>
        </div>
      </div>
      <div v-else class="noPosts">
        <p class="noPosts_text" v-if="currentUser">
          <strong>{{ currentUser.prenom }} {{ currentUser.nom }}</strong> n'a
          encore jamais publié de posts.
        </p>
      </div>
    </div>
  </div>
</template>
<!-- eslint-disable -->
<style lang="scss" scoped>
@import "../assets/css/user.scss";
</style>
<!-- eslint-disable -->
<script>
import axios from "axios";
const CryptoJS = require("crypto-js");

export default {
  data() {
    return {
      first: null,
      image: null,
      userIdPage: this.$route.params.userId,
      user: null,
      currentUser: null,
      likedPost: [],
      posts: null,
      postsRecive: null,
      token: document.cookie
        ? document.cookie
            .split("; ")
            .find((row) => row.startsWith("user-token="))
            .split("=")[1]
        : null,
      comments: null,
      newComment: null,
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
    formatDate(input) {
      console.log(input);
      const datePart = input.match(/\d+/g),
        year = datePart[0].substring(2), // get only two digits
        month = datePart[1],
        day = datePart[2],
        hour = datePart[3],
        minute = datePart[4];
      return (
        day + "/" + month + "/" + year + "\u00a0" + " à " + hour + "h" + minute
      );
    },
    modifPP(event) {
      this.image = event.target.files[0];
      console.log(this.image);
      this.token = document.cookie
        .split("; ")
        .find((row) => row.startsWith("user-token="))
        .split("=")[1];
      const fd = new FormData();
      if (this.image) {
        fd.append("image", this.image, "image");
      }
      const self = this;
      axios
        .put(`http://localhost:3000/api/user/modify/${self.userId}`, fd, {
          headers: {
            "Content-Type": "multipart/form-data",
            Authorization: `Bearer ${this.token}`,
          },
        })
        .then(function (response) {
          console.log(response);
          self.$router.go();
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    afficherComment(event) {
      let path;
      if (event.path[3].children[3].matches(".react")) {
        path = event.path[3].children[4];
      } else {
        path = event.path[3].children[3];
      }
      if (path.matches(".disp")) {
        path.classList.remove("disp");
      } else {
        path.classList.add("disp");
      }
    },
    upload2(event) {
      this.image = event.target.files[0];
    },
    addPost() {
      this.token = document.cookie
        .split("; ")
        .find((row) => row.startsWith("user-token="))
        .split("=")[1];
      this.text = document.querySelector("#text").value;
      const fd = new FormData();
      fd.append("userId", this.userId);
      if (this.text != "") {
        fd.append("text", this.text);
      }
      if (this.image) {
        fd.append("image", this.image, "image");
      }
      if (this.text || this.image) {
        const self = this;
        axios
          .post("http://localhost:3000/api/post", fd, {
            headers: {
              "Content-Type": "multipart/form-data",
              Authorization: `Bearer ${this.token}`,
            },
          })
          .then(function (response) {
            console.log(response);
            document.querySelector("#text").value = null;
            self.getPost();
          })
          .catch(function (error) {
            console.log(error);
          });
      }
    },
    liked() {
      const self = this;
      axios
        .post("http://localhost:3000/api/like/liked", {
          userId: this.userId,
        })
        .then(function (response) {
          const ObjlikedPosts = response.data;
          self.likedPost = [];
          for (const ObjlikedPost of ObjlikedPosts) {
            self.likedPost.push(ObjlikedPost.postId);
          }
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    deletePost(postId, authorId) {
      const self = this;
      if (this.userId == authorId || (self.user && self.user.admin)) {
        axios
          .delete(`http://localhost:3000/api/post/${postId}`, {
            headers: { Authorization: `Bearer ${this.token}` },
            data: { userId: self.userId, admin: self.user.admin },
          })
          .then((response) => {
            console.log(response);
            self.getPost();
          })
          .catch(function (error) {
            console.log(error);
          });
      }
    },
    like(currentPostId) {
      const self = this;
      axios
        .post("http://localhost:3000/api/like", {
          userId: this.userId,
          postId: currentPostId,
        })
        .then(function (response) {
          console.log(response);
          self.liked();
          self.getPost();
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    deleteComment(id, authorId, currentPostId) {
      const self = this;
      if (this.userId == authorId || (self.user && self.user.admin)) {
        axios
          .delete(`http://localhost:3000/api/comment/${id}/${currentPostId}`, {
            headers: { Authorization: `Bearer ${this.token}` },
            data: { userId: self.userId, admin: self.user.admin },
          })
          .then((response) => {
            console.log(response);
            self.getPost();
          })
          .catch(function (error) {
            console.log(error);
          });
      }
    },
    upload(event) {
      this.newComment = event.target.value;
    },
    comment(event, id) {
      if (this.newComment) {
        const self = this;
        axios
          .post(
            "http://localhost:3000/api/comment",
            {
              comment: this.newComment,
              authorId: this.userId,
              postId: id,
            },
            {
              headers: { Authorization: `Bearer ${this.token}` },
            }
          )
          .then(function (response) {
            console.log(response);
            let pathClass, pathInput;
            if (event.path[2].children[3].matches(".react")) {
              pathClass = event.path[2].children[4];
              pathInput = event.path[2].children[5].children[0];
            } else {
              pathClass = event.path[2].children[3];
              pathInput = event.path[2].children[4].children[0];
            }
            pathClass.classList.remove("disp");
            pathInput.value = null;
            self.getPost();
          })
          .catch(function (error) {
            console.log(error);
          });
      }
    },
    async getUser() {
      const self = this;
      axios
        .post(
          "http://localhost:3000/api/user",
          { userId: self.userIdPage },
          {
            headers: {
              Authorization: `Bearer ${self.token}`,
            },
          }
        )
        .then((response) => {
          this.currentUser = response.data[0];
        })
        .catch(function (error) {
          if (error.response && error.response.status === 403) {
            self.$router.push("/");
          }
        });
    },
    getPost() {
      const self = this;
      axios
        .post(
          "http://localhost:3000/api/post/byAuthor",
          { id: self.userIdPage },
          {
            headers: {
              Authorization: `Bearer ${self.token}`,
            },
          }
        )
        .then((response) => {
          this.posts = response.data;
          this.first = this.posts[0];
          axios
            .get("http://localhost:3000/api/comment", {
              headers: { Authorization: `Bearer ${this.token}` },
            })
            .then((response) => (this.comments = response.data))
            .catch(function (error) {
              if (error.response && error.response.status === 401) {
                self.$router.push("/");
              }
            });
        })
        .catch(function (error) {
          if (error.response && error.response.status === 401) {
            self.$router.push("/");
          }
        });
    },
  },
  mounted() {
    (this.userId = document.cookie
      ? CryptoJS.AES.decrypt(
          document.cookie
            .split("; ")
            .find((row) => row.startsWith("userId="))
            .split("=")[1],
          this.$store.state.CryptoKey
        ).toString(CryptoJS.enc.Utf8)
      : null),
      (this.token = document.cookie
        ? document.cookie
            .split("; ")
            .find((row) => row.startsWith("user-token="))
            .split("=")[1]
        : null);
    const self = this;
    this.getUser();
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
    this.getPost();
    this.liked();
  },
};
</script>
