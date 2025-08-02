<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>MiniFlix</title>
  <style>
    /* ⬅️ BURAYA senin verdiğin CSS kodu eksiksiz eklenecek */
  </style>
</head>
<body>
  <!-- 🔐 Firebase Login -->
  <div id="login-screen">
    <form id="login-form">
      <h2>Kullanıcı Girişi</h2>
      <input type="email" id="email" placeholder="Email" required />
      <input type="password" id="password" placeholder="Şifre" required />
      <button type="submit">Giriş Yap</button>
      <p style="color:#bbb;font-size:0.9rem;">Hesabınız yok mu? <a href="#" id="register-link" style="color:#e50914;">Kayıt ol</a></p>
    </form>
  </div>

  <!-- ⬇️ MiniFlix Arayüzü -->
  <header>MiniFlix</header>
  <nav>
    <button class="active" data-category="anime">Anime</button>
    <button data-category="scifi">Bilim Kurgu</button>
    <button data-category="adventure">Macera</button>
    <button data-category="series">Diziler</button>
    <button data-category="movies">Filmler</button>
    <button data-category="favorites">Favoriler</button>
  </nav>
  <div id="search-container">
    <input type="text" id="search-input" placeholder="Film veya kategori ara..." />
  </div>
  <main>
    <h2 class="category-title">Anime</h2>
    <div class="movie-row" id="movie-row"></div>
  </main>

  <!-- 🎥 Detay popup (senin tasarım) -->
  <div id="detail-popup">
    <!-- içerik aynı kalacak -->
  </div>

  <!-- 🌐 Firebase ve uygulama scripti -->
  <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/12.0.0/firebase-app.js";
    import { getAuth, signInWithEmailAndPassword, createUserWithEmailAndPassword, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/12.0.0/firebase-auth.js";
    import { getFirestore, doc, setDoc, getDoc } from "https://www.gstatic.com/firebasejs/12.0.0/firebase-firestore.js";

    const firebaseConfig = {
      apiKey: "AIzaSyCqpQpBqiys2gSNJowq74BLf_XgxI18N2I",
      authDomain: "film-91c18.firebaseapp.com",
      projectId: "film-91c18",
      storageBucket: "film-91c18.appspot.com",
      messagingSenderId: "670730269608",
      appId: "1:670730269608:web:b59ffbbc554a8a90f9e39d"
    };

    const app = initializeApp(firebaseConfig);
    const auth = getAuth(app);
    const db = getFirestore(app);

    // 🔐 Giriş ve Kayıt Sistemi
    const loginScreen = document.getElementById('login-screen');
    const loginForm = document.getElementById('login-form');
    const emailInput = document.getElementById('email');
    const passwordInput = document.getElementById('password');
    const registerLink = document.getElementById('register-link');

    let isRegistering = false;

    registerLink.addEventListener('click', e => {
      e.preventDefault();
      isRegistering = true;
      loginForm.querySelector('button').textContent = "Kayıt Ol";
    });

    loginForm.addEventListener('submit', async (e) => {
      e.preventDefault();
      const email = emailInput.value;
      const password = passwordInput.value;

      try {
        if (isRegistering) {
          await createUserWithEmailAndPassword(auth, email, password);
        } else {
          await signInWithEmailAndPassword(auth, email, password);
        }
      } catch (err) {
        alert("Hata: " + err.message);
      }
    });

    onAuthStateChanged(auth, async (user) => {
      if (user) {
        loginScreen.style.display = 'none';
        loadFavorites();
      } else {
        loginScreen.style.display = 'flex';
      }
    });

    // 📁 Firestore Favori Yönetimi
    let favorites = [];

    async function saveFavorites() {
      const user = auth.currentUser;
      if (!user) return;
      await setDoc(doc(db, "favorites", user.uid), { list: favorites });
    }

    async function loadFavorites() {
      const user = auth.currentUser;
      if (!user) return;
      const snap = await getDoc(doc(db, "favorites", user.uid));
      if (snap.exists()) {
        favorites = snap.data().list;
      } else {
        favorites = [];
      }
      const activeCategory = document.querySelector('nav button.active').dataset.category;
      loadCategory(activeCategory);
    }

    function isFavorite(title) {
      return favorites.includes(title);
    }

    function toggleFavorite(title, btn) {
      if (isFavorite(title)) {
        favorites = favorites.filter(f => f !== title);
        btn.classList.remove('active');
      } else {
        favorites.push(title);
        btn.classList.add('active');
      }
      saveFavorites();
    }

    // 🎬 Kategoriler, Arama, Detay Popup — senin kodun birebir olacak

    // 🧠 Not: "categories" objesi, movieRow, nav butonları, showDetail, createMovieCard vs. burada çağrılır.

  </script>
</body>
</html>
