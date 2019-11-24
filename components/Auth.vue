<template>
  <div>
    <div v-show="isLogin">
      <p class="login">ログイン中</p>
      <p class="name">名前：{{ displayName }}</p>
      <button @click="logout">ログアウト</button>
    </div>
    <div v-show="!isLogin" id="firebaseui-auth-container"></div>
  </div>
</template>
<script>
export default {
  computed: {
    isLogin() {
      return this.$store.getters["user/isLogin"];
    },
    displayName() {
      return this.$store.getters["user/displayName"];
    }
  },
  methods: {
    logout() {
      this.$store.dispatch("user/logout");
      const firebase = require("firebase");
      firebase
        .auth()
        .signOut()
        .then(() => {
          console.log("ログアウトしました");
        });
    }
  },
  mounted() {
    const firebase = require("firebase");
    const firebaseui = require("firebaseui");
    require("firebaseui/dist/firebaseui.css");
    // const firebaseui = require("firebaseui_ja/npm__ja");
    // require("firebaseui_ja/firebaseui.css");

    const uiConfig = {
      signInSuccessUrl: "/",
      signInOptions: [
        firebaseui.auth.AnonymousAuthProvider.PROVIDER_ID,
        firebase.auth.EmailAuthProvider.PROVIDER_ID,
        firebase.auth.GoogleAuthProvider.PROVIDER_ID,
        firebase.auth.FacebookAuthProvider.PROVIDER_ID,
        firebase.auth.TwitterAuthProvider.PROVIDER_ID,
        firebase.auth.GithubAuthProvider.PROVIDER_ID,
        firebase.auth.PhoneAuthProvider.PROVIDER_ID
      ]
    };
    const firebaseConfig = {
      apiKey: process.env.FB_API_KEY,
      authDomain: process.env.FB_AUTH_DOMAIN,
      databaseURL: process.env.FB_DATABASE_URL,
      projectId: process.env.FB_PROJECT_ID,
      storageBucket: process.env.FB_STORAGE_BUCKET,
      messagingSenderId: process.env.FB_MESSAGE_SENDER_ID
    };
    firebase.initializeApp(firebaseConfig);

    const ui = new firebaseui.auth.AuthUI(firebase.auth());
    ui.start("#firebaseui-auth-container", uiConfig);

    firebase.auth().onAuthStateChanged(user => {
      this.$store.dispatch("user/login", user);
    });
  }
};
</script>
<style scoped>
.login {
  font-size: 1.25rem;
}
.name {
  font-size: 1.1rem;
}
</style>
