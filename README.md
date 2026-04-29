<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Neighborhood Trash Can Cleaning</title>
  <style>
    body { font-family: Arial; margin:0; background:#f4f4f4; }
    header { background:#2c3e50; color:white; padding:15px; text-align:center; }
    .container { padding:20px; }
    .upload-section { background:white; padding:15px; border-radius:10px; margin-bottom:20px; display:none; }
    input, button { margin:8px 0; padding:10px; width:100%; }
    button { background:#27ae60; color:white; border:none; }
    .gallery { display:grid; grid-template-columns:repeat(auto-fit,minmax(200px,1fr)); gap:10px; }
    .card { background:white; padding:10px; border-radius:10px; text-align:center; }
    img { width:100%; border-radius:10px; }
    .login-box { background:white; padding:20px; border-radius:10px; max-width:300px; margin:40px auto; text-align:center; }
  </style>
</head>
<body>

<header>
  <h1>Neighborhood Trash Can Cleaning</h1>
  <p>Before & After Results</p>
</header>

<div class="container">

  <div class="login-box" id="loginBox">
    <h2>Private Upload Login</h2>
    <input type="password" id="password" placeholder="Enter password">
    <button onclick="checkPassword()">Login</button>
    <p id="loginStatus"></p>
  </div>

  <div class="upload-section" id="uploadSection">
    <h2>Upload Before & After</h2>
    <input type="file" id="before" accept="image/*">
    <input type="file" id="after" accept="image/*">
    <button onclick="uploadImages()">Upload</button>
    <p id="status"></p>
  </div>

  <div class="gallery" id="gallery"></div>

</div>

<!-- Firebase -->
<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import { getStorage, ref, uploadBytes, getDownloadURL, listAll } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-storage.js";

const firebaseConfig = {
  apiKey: "AIzaSyB_KhLWmtKVsPOS4qG37J-Yca_R1WxkNLI",
  authDomain: "trashcancleaningstorage4web.firebaseapp.com",
  projectId: "trashcancleaningstorage4web",
  storageBucket: "trashcancleaningstorage4web.appspot.com",
  messagingSenderId: "311645604075",
  appId: "1:311645604075:web:fbb8b7510f02ef1d1916ae",
  measurementId: "G-EXTC0LEGYQ"
};

const app = initializeApp(firebaseConfig);
const storage = getStorage(app);

const PASSWORDS = ["RAYRAY301", "scarab2233"];

window.checkPassword = function() {
  const input = document.getElementById('password').value.trim();
  const status = document.getElementById('loginStatus');

  if (PASSWORDS.includes(input)) {
    document.getElementById('loginBox').style.display = 'none';
    document.getElementById('uploadSection').style.display = 'block';
    loadImages();
  } else {
    status.innerText = "❌ Wrong password";
  }
}

window.uploadImages = async function() {
  const beforeFile = document.getElementById('before').files[0];
  const afterFile = document.getElementById('after').files[0];
  const status = document.getElementById('status');

  if (!beforeFile || !afterFile) {
    status.innerText = "❌ Select both images";
    return;
  }

  const id = Date.now();

  const beforeRef = ref(storage, `jobs/${id}_before`);
  const afterRef = ref(storage, `jobs/${id}_after`);

  await uploadBytes(beforeRef, beforeFile);
  await uploadBytes(afterRef, afterFile);

  status.innerText = "✅ Uploaded permanently!";
  loadImages();
}

async function loadImages() {
  const gallery = document.getElementById('gallery');
  gallery.innerHTML = "";

  const listRef = ref(storage, 'jobs/');
  const res = await listAll(listRef);

  const files = res.items;

  for (let i = 0; i < files.length; i += 2) {
    const beforeURL = await getDownloadURL(files[i]);
    const afterURL = await getDownloadURL(files[i+1]);

    const card = document.createElement('div');
    card.className = 'card';

    card.innerHTML = `
      <h3>Before</h3>
      <img src="${beforeURL}">
      <h3>After</h3>
      <img src="${afterURL}">
    `;

    gallery.appendChild(card);
  }
}
</script>

</body>
</html>
