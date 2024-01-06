# Firestore

## These are the steps we need to follow to create a firebase project.

- https://firebase.google.com/
- Go to console.
- Add project.
- After adding the project go to Project Overview (top left).
- Go to project settings.
- In the "You apps" sections choose web app.
- We didn't tick "Also set up Firebase Hosting for this app".
- Click "Register App".
- npm install firebase
- Copy the configurations to the project.
  - Create a config folder.
  - Create a firestore.js file.
- Create a .end file for the apiKey.
- More info on .env https://vitejs.dev/guide/env-and-mode.html

```.env
VITE_FIRESTORE_API=AIzaSyBs6a0635566nHyEq6FtfqiSubZIeyGZMM
```

and then import it in the config file.

```js
const apiKey = import.meta.env.VITE_FIRESTORE_API;

const firebaseConfig = {
  apiKey: apiKey,
  authDomain: "portfolio-bbcfc.firebaseapp.com",
  projectId: "portfolio-bbcfc",
  storageBucket: "portfolio-bbcfc.appspot.com",
  messagingSenderId: "589572668307",
  appId: "1:589572668307:web:9498e006eefa55addac666",
  measurementId: "G-FF7K2RE5SW",
};
```

- Update the gitignore file.

```.gitignore
# Ignore .env
.env
```

- We also add the firestore to the config file.

```js
// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
import { getFirestore } from "firebase/firestore";
import { getAnalytics } from "firebase/analytics";
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries
const apiKey = import.meta.env.VITE_FIRESTORE_API;
// Your web app's Firebase configuration
// For Firebase JS SDK v7.20.0 and later, measurementId is optional
const firebaseConfig = {
  apiKey: apiKey,
  authDomain: "portfolio-bbcfc.firebaseapp.com",
  projectId: "portfolio-bbcfc",
  storageBucket: "portfolio-bbcfc.appspot.com",
  messagingSenderId: "589572668307",
  appId: "1:589572668307:web:9498e006eefa55addac666",
  measurementId: "G-FF7K2RE5SW",
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
export const db = getFirestore(app);
const analytics = getAnalytics(app);
```

- Create some test collection and some test data (optional).
- Create a services folder.
- Create a test-service.js.

#Firebase Methods

```jsx
import {
  addDoc,
  collection,
  deleteDoc,
  doc,
  getDoc,
  getDocs,
  onSnapshot,
  orderBy,
  query,
  serverTimestamp,
  updateDoc,
  where,
} from "firebase/firestore";
import { db } from "../../config/firestore";

const modifyData = async () => {
  //Adding Something
  // const colRef = collection(db, "fashion");
  // addDoc(colRef, { name: "lebas", price: 1000 });

  //Updating a document
  const docRef = doc(db, "fashion", "item11");
  updateDoc(docRef, { name: "Club Fleece Funnel Hoodie" });

  //Adding Something with Timestamp
  // const colRef = collection(db, "fashion");
  // addDoc(colRef, {
  //   name: "lebas",
  //   price: 1000,
  //   createdAt: serverTimestamp(),
  // });

  // const docRef = doc(db, "fashion", "tsM7R9KmwQwZSr1UbKwy");
  // deleteDoc(docRef);

  // getDocs(colRef)
  //   .then((snapshot) => {
  // let items = [];
  // snapshot.docs.forEach((doc) => {
  //   items.push({ ...doc.data(), id: doc.id });
  // });
  // console.log(items);
  //   })
  //   .catch((err) => {
  //     console.log(err.message);
  //   });
};

//Live Update
// onSnapshot(colRef, (snapshot) => {
//   let items = [];
//   snapshot.docs.forEach((doc) => {
//     items.push({ ...doc.data(), id: doc.id });
//   });
//   console.log(items);
// });

//Live Update with query
// const colRef = collection(db, "fashion");
// const q = query(colRef, where("name", "==", "lebas"));
// onSnapshot(q, (snapshot) => {
//   let items = [];
//   snapshot.docs.forEach((doc) => {
//     items.push({ ...doc.data(), id: doc.id });
//   });
//   console.log(items);
// });

//  Live Update with query and order
// const colRef = collection(db, "fashion");
// const q = query(
//   colRef,
//   where("favourite", "==", false),
//   orderBy("price", "desc")
// );
// onSnapshot(q, (snapshot) => {
//   let items = [];
//   snapshot.docs.forEach((doc) => {
//     items.push({ ...doc.data(), id: doc.id });
//   });
//   console.log(items);
// });

//getting a single document
// const docRef = doc(db, "fashion", "item11");
// getDoc(docRef).then((doc) => {
//   console.log(doc.data(), doc.id);
// });

//subscribing to a single document
// onSnapshot(docRef, (doc) => {
//   console.log(doc.data(), doc.id);
// });
```
