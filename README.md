## 📘 Documentation de l'API Viadex

### 🏗️ Structure du projet

```
/
├── config.php                 # Connexion à la base de données
├── index.php                 # Routeur principal
└── routes/
    ├── blogpost.php
    ├── contact.php
    ├── user.php
    ├── meeting.php
    └── blograw.php

```

---

### 🔐 Authentification par token

- Le token doit être passé en **paramètre de requête** :
    
    `?token=your_token_here`
    
- Toutes les routes (sauf `POST /user` pour la connexion) nécessitent un token valide.
- Le token est vérifié dans la table `token`.

---

### 🔀 Logique de routage (`index.php`)

```php
$uri = explode('/', trim(parse_url($_SERVER['REQUEST_URI'], PHP_URL_PATH), '/'));
$route = $uri[0] ?? null;
$allowedRoutes = ['blogpost', 'contact', 'user', 'meeting', 'blograw'];

if (in_array($route, $allowedRoutes)) {
    require "routes/$route.php";
} else {
    http_response_code(404);
    echo json_encode(['error' => 'Not Found']);
}

```

---

### 📌 Route : `/blogpost`

### GET `/blogpost`

- Retourne tous les articles.
- Exemple : `/blogpost?token=abc`

### GET `/blogpost/{id}`

- Retourne un article spécifique.
- Exemple : `/blogpost/7?token=abc`

### POST `/blogpost`

- Crée un nouvel article.
- Requête JSON :

```json
{
  "titre": "Titre",
  "ImagePath": "img.jpg",
  "date": "2025-04-02",
  "Text": "Contenu",
  "reading_time": 5,
  "categorie": 2
}

```

### PUT `/blogpost/{id}`

- Met à jour un article.
- Requête identique à POST.

### DELETE `/blogpost/{id}`

- Supprime un article.

---

### 📌 Route : `/contact`

### GET `/contact`

- Retourne tous les messages de contact.

### GET `/contact/{id}`

- Retourne un message avec les infos liées (status, source, raison).

### POST `/contact`

- Crée une nouvelle entrée de contact.
- L'adresse IP est ajoutée automatiquement.

```json
{
  "nom": "Jean",
  "prenom": "Dupont",
  "date": "2025-04-02",
  "status": 1,
  "message": "Bonjour",
  "source": 2,
  "raison": 3
}

```

### DELETE `/contact/{id}`

- Supprime un message de contact.

---

### 📌 Route : `/user`

### POST `/user`

- Authentifie un utilisateur.
- Requête JSON :

```json
{
  "email": "user@viadex.fr",
  "password": "123456"
}

```

### GET `/user/{email}`

- Retourne les infos d’un utilisateur.
- Requiert un token.

---

### 📌 Route : `/meeting`

### GET `/meeting`

- Retourne toutes les demandes de rendez-vous.

### GET `/meeting/{contact_id}`

- Retourne un rendez-vous par ID de contact.

### POST `/meeting`

- Crée une demande de rendez-vous.

```json
{
  "id_contact": 1,
  "date1": "2025-04-02",
  "pref1": 2,
  "date2": "2025-04-03",
  "pref2": 3
}

```

### DELETE `/meeting/{rdv_id}`

- Supprime un rendez-vous.

---

### 📌 Route : `/blograw`

### GET `/blograw`

- Retourne tous les articles bruts.

### GET `/blograw?id=5`

- Retourne un article brut.

### POST `/blograw`

- Crée un article brut.

```json
{
  "link": "https://...",
  "content": "Du contenu HTML",
  "titre": "Titre brut"
}

```

### PUT `/blograw?id=5`

- Met à jour un article brut.
- Requête identique à POST.

### DELETE `/blograw?id=5`

- Supprime un article brut.
