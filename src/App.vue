<script setup>
import { onMounted } from "vue";
import { initializeApp } from "firebase/app";
import {
  getFirestore,
  collection,
  getDocs,
  doc,
  onSnapshot,
} from "firebase/firestore";
import {
  getAuth,
  signInWithRedirect,
  GoogleAuthProvider,
  onAuthStateChanged,
} from "firebase/auth";
import { reactive } from "vue";
import Header from "./components/Header.vue";

const state = reactive({ sources: [] });

// Firebase config
const firebaseConfig = {
  apiKey: "AIzaSyBh2CfZMnxcolqv72spLqn7be05CgFE320",
  authDomain: "monthly-net-worth.firebaseapp.com",
  projectId: "monthly-net-worth",
  storageBucket: "monthly-net-worth.appspot.com",
  messagingSenderId: "837670855920",
  appId: "1:837670855920:web:699e2d1d181873823a0e35",
};

const provider = new GoogleAuthProvider();

onMounted(async () => {
  const app = initializeApp(firebaseConfig);
  const auth = getAuth();

  const database = getFirestore(app);

  const sourceDatabaseData = await getDocs(collection(database, "source"));

  sourceDatabaseData.forEach((source) => {
    state.sources.push(source.data());
  });

  onAuthStateChanged(auth, (user) => {
    if (user) {
      // User is signed in, see docs for a list of available properties
      // https://firebase.google.com/docs/reference/js/firebase.User
      const uid = user.uid;
      console.log(uid);
      // ...
    } else {
      // User is signed out
      // ...
      console.log("signed out");
    }
  });
});

function login() {
  const auth = getAuth();
  auth.languageCode = "sl";

  signInWithRedirect(auth, provider)
    .then((result) => {
      // This gives you a Google Access Token. You can use it to access the Google API.
      const credential = GoogleAuthProvider.credentialFromResult(result);
      const token = credential.accessToken;
      // The signed-in user info.
      const user = result.user;

      console.log(user.displayName);
      // ...
    })
    .catch((error) => {
      // Handle Errors here.
      const errorCode = error.code;
      const errorMessage = error.message;
      // The email of the user's account used.
      const email = error.customData.email;
      // The AuthCredential type that was used.
      const credential = GoogleAuthProvider.credentialFromError(error);
      // ...
    });
}
</script>

<template>
  <main>
    <Header />
    <button @click="login">Log-in</button>
    {{ state.sources }}
    <div class="container mx-auto">
      <div class="grid grid-rows-4 grid-flow-col gap-4">
        <div>
          <table class="table-auto bg-gray-50">
            <thead>
              <tr>
                <th>Name</th>
                <th>Value</th>
              </tr>
            </thead>
            <tbody>
              <!-- <tr> for loop -> td za vsak podatek :) -->
              <tr v-for="source in state.sources">
                <td>{{ source.name }}</td>
                <td>{{ source.value }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </main>
</template>

<style>
* {
  background: white;
}
</style>
