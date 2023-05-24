<script context="module">
  import "../app.css";
  import { initializeApp } from 'firebase/app';
  import { getFirestore } from 'firebase/firestore';

  const firebaseConfig = {
    apiKey: "AIzaSyBW3cdQoZIZkJWmZD56YGHLqOZ88YWoWiE",
    authDomain: "lucasssss-688ef.firebaseapp.com",
    projectId: "lucasssss-688ef",
    storageBucket: "lucasssss-688ef.appspot.com",
    messagingSenderId: "885300969563",
    appId: "1:885300969563:web:cfaecc3101f5d48d2aff7e"
  };

  const app = initializeApp(firebaseConfig);
  export const db = getFirestore(app);

</script>

<slot />
