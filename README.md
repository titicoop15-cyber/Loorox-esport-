<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Esport Fortnite</title>
<style>
  body {
    margin: 0;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #7b2ff7, #f107a3);
    color: white;
  }
  header {
    background: rgba(0,0,0,0.3);
    padding: 10px 20px;
    display: flex;
    justify-content: flex-end;
    align-items: center;
  }
  .btn {
    background-color: #f107a3;
    border: none;
    padding: 10px 18px;
    margin-left: 10px;
    color: white;
    font-weight: bold;
    cursor: pointer;
    border-radius: 4px;
    transition: background-color 0.3s;
  }
  .btn:hover {
    background-color: #b20472;
  }
  #profileMenu {
    position: relative;
    cursor: pointer;
  }
  #profileMenu span {
    padding: 8px 15px;
    background-color: #f107a3;
    border-radius: 20px;
    font-weight: bold;
  }
  #dropdown {
    display: none;
    position: absolute;
    right: 0;
    top: 40px;
    background-color: rgba(0,0,0,0.7);
    border-radius: 5px;
    padding: 8px 12px;
  }
  #dropdown button {
    background-color: transparent;
    border: none;
    color: white;
    cursor: pointer;
    font-weight: bold;
  }
  main {
    padding: 30px;
    max-width: 900px;
    margin: auto;
  }
  h1 {
    text-align: center;
    margin-bottom: 40px;
    text-shadow: 2px 2px 5px #800048;
  }
  .hidden {
    display: none;
  }
  form {
    max-width: 400px;
    margin: 30px auto;
    background: rgba(0,0,0,0.4);
    padding: 20px;
    border-radius: 8px;
  }
  form input {
    width: 100%;
    padding: 10px;
    margin-top: 12px;
    border: none;
    border-radius: 5px;
    font-size: 1rem;
  }
  form button {
    margin-top: 15px;
    width: 100%;
  }
  table {
    width: 100%;
    border-collapse: collapse;
    background: rgba(0,0,0,0.4);
    border-radius: 8px;
    overflow: hidden;
  }
  th, td {
    padding: 12px 10px;
    border-bottom: 1px solid #f107a3;
    text-align: left;
  }
  th {
    background-color: #b20472;
  }
</style>
</head>
<body>

<header id="header">
  <!-- Profil affiché quand connecté -->
</header>

<main>
  <section id="homePage">
    <h1>Bienvenue sur Esport Fortnite</h1>
    <button class="btn" id="showLogin">Connexion</button>
    <button class="btn" id="showRegister">Inscription</button>
  </section>

  <section id="loginPage" class="hidden">
    <h1>Connexion</h1>
    <form id="loginForm">
      <input type="text" id="loginUsername" placeholder="Nom d'utilisateur" required />
      <input type="password" id="loginPassword" placeholder="Mot de passe" required />
      <button class="btn" type="submit">Se connecter</button>
      <p><a href="#" id="backToHomeFromLogin" style="color:#f107a3; cursor:pointer;">Retour</a></p>
    </form>
  </section>

  <section id="registerPage" class="hidden">
    <h1>Inscription</h1>
    <form id="registerForm">
      <input type="text" id="registerUsername" placeholder="Nom d'utilisateur" required />
      <input type="password" id="registerPassword" placeholder="Mot de passe" required />
      <button class="btn" type="submit">S'inscrire</button>
      <p><a href="#" id="backToHomeFromRegister" style="color:#f107a3; cursor:pointer;">Retour</a></p>
    </form>
  </section>

  <section id="dashboardPage" class="hidden">
    <h1>Tableau de bord</h1>
    <h2>Joueurs connectés</h2>
    <ul id="connectedPlayers" style="background: rgba(0,0,0,0.4); padding: 15px; border-radius: 8px; max-width: 400px;"></ul>
    
    <h2>Classement</h2>
    <table>
      <thead>
        <tr><th>Rang</th><th>Joueur</th><th>Points</th></tr>
      </thead>
      <tbody id="rankingTableBody">
        <!-- Classement dynamique -->
      </tbody>
    </table>
  </section>
</main>

<script>
  // Stockage local des "utilisateurs"
  let users = JSON.parse(localStorage.getItem('users')) || [];

  // Stockage session utilisateur connecté
  let currentUser = JSON.parse(sessionStorage.getItem('currentUser')) || null;

  // Joueurs connectés fictifs (on simule un serveur)
  let connectedPlayers = [];

  const header = document.getElementById('header');
  const homePage = document.getElementById('homePage');
  const loginPage = document.getElementById('loginPage');
  const registerPage = document.getElementById('registerPage');
  const dashboardPage = document.getElementById('dashboardPage');

  const showLoginBtn = document.getElementById('showLogin');
  const showRegisterBtn = document.getElementById('showRegister');
  const backToHomeFromLogin = document.getElementById('backToHomeFromLogin');
  const backToHomeFromRegister = document.getElementById('backToHomeFromRegister');

  const loginForm = document.getElementById('loginForm');
  const registerForm = document.getElementById('registerForm');

  const connectedPlayersList = document.getElementById('connectedPlayers');
  const rankingTableBody = document.getElementById('rankingTableBody');

  // Classement fictif
  const ranking = [
    {name: 'Ninja', points: 1500},
    {name: 'Tfue', points: 1400},
    {name: 'Myth', points: 1350},
    {name: 'Bugha', points: 1300},
    {name: 'Mongraal', points: 1250},
  ];

  // Afficher ou cacher les sections
  function showSection(section) {
    [homePage, loginPage, registerPage, dashboardPage].forEach(s => s.classList.add('hidden'));
    section.classList.remove('hidden');
  }

  // Mise à jour du header avec profil et déconnexion
  function updateHeader() {
    header.innerHTML = '';
    if (currentUser) {
      const profileDiv = document.createElement('div');
      profileDiv.id = 'profileMenu';
      profileDiv.innerHTML = `<span>${currentUser.username}</span>`;
      const dropdown = document.createElement('div');
      dropdown.id = 'dropdown';
      dropdown.innerHTML = `<button id="logoutBtn">Déconnexion</button>`;
      profileDiv.appendChild(dropdown);
      header.appendChild(profileDiv);

      profileDiv.addEventListener('click', () => {
        dropdown.style.display = dropdown.style.display === 'block' ? 'none' : 'block';
      });

      document.getElementById('logoutBtn').addEventListener('click', () => {
        logout();
      });
    } else {
      header.innerHTML = '';
    }
  }

  // Afficher joueurs connectés + classement
  function updateDashboard() {
    // Simuler joueurs connectés : ajouter currentUser + 3 autres joueurs
    connectedPlayers = [currentUser.username, 'Player1', 'Player2', 'Player3'];

    connectedPlayersList.innerHTML = '';
    connectedPlayers.forEach(player => {
      const li = document.createElement('li');
      li.textContent = player;
      connectedPlayersList.appendChild(li);
    });

    // Afficher classement
    rankingTableBody.innerHTML = '';
    ranking.forEach((player, idx) => {
      const tr = document.createElement('tr');
      tr.innerHTML = `<td>${idx + 1}</td><td>${player.name}</td><td>${player.points}</td>`;
      rankingTableBody.appendChild(tr);
    });
  }

  // Connexion
  loginForm.addEventListener('submit', e => {
    e.preventDefault();
    const username = document.getElementById('loginUsername').value.trim();
    const password = document.getElementById('loginPassword').value;

    const user = users.find(u => u.username === username && u.password === password);
    if (user) {
      currentUser = {username};
      sessionStorage.setItem('currentUser', JSON.stringify(currentUser));
      showSection(dashboardPage);
      updateHeader();
      updateDashboard();
    } else {
      alert('Nom d\'utilisateur ou mot de passe incorrect.');
    }
  });

  // Inscription
  registerForm.addEventListener('submit', e => {
    e.preventDefault();
    const username = document.getElementById('registerUsername').value.trim();
    const password = document.getElementById('registerPassword').value;

    if(users.find(u => u.username === username)){
      alert('Nom d\'utilisateur déjà pris.');
      return;
    }
    users.push({username, password});
    localStorage.setItem('users', JSON.stringify(users));
    alert('Inscription réussie ! Tu peux maintenant te connecter.');
    showSection(homePage);
  });

  // Boutons retour
  backToHomeFromLogin.addEventListener('click', e => {
    e.preventDefault();
    showSection(homePage);
  });
  backToHomeFromRegister.addEventListener('click', e => {
    e.preventDefault();
    showSection(homePage);
  });

  // Déconnexion
  function logout() {
    currentUser = null;
    sessionStorage.removeItem('currentUser');
    showSection(homePage);
    updateHeader();
  }

  // Au chargement, vérifier si utilisateur connecté
  if (currentUser) {
    showSection(dashboardPage);
    updateHeader();
    updateDashboard();
  } else {
    showSection(homePage);
  }

</script>

</body>
</html># Loorox-esport-
Connecter vous et soyez dans la team loorox 
