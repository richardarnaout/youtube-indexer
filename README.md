YouTube Indexer
Ce projet permet d'indexer des vidéos YouTube via une API YouTube, avec un backend en Node.js, un frontend en React, et une base de données MySQL pour stocker les informations.



Prérequis
Avant de commencer, il faut avoir installé les outils suivants :

Node.js : Télécharger et installer Node.js

XAMPP : Télécharger et installer XAMPP (pour MySQL et Apache)

MySQL Workbench (optionnel) : Télécharger et installer MySQL Workbench


1. Backend (Node.js)
1.1 Créer le Répertoire du Projet
Dans le terminal, créez un dossier pour le backend :

```
mkdir backend
cd backend
```

1.2 Initialiser le Projet Node.js
Initialisez un projet Node.js :

```
npm init -y
```


1.3 Installer les Dépendances Backend
Installez les dépendances nécessaires pour le backend :


```
npm install express mysql dotenv
```


1.4 Créer le Fichier index.js
Créez un fichier index.js et ajoutez le code suivant pour configurer le serveur backend :


```
const express = require('express');
const mysql = require('mysql');
const dotenv = require('dotenv');

dotenv.config();  // Charger les variables d'environnement depuis .env

const app = express();
app.use(express.json());  // Permet l'envoi de données JSON

// Configuration de la base de données MySQL
const db = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: '',  // Mot de passe par défaut dans XAMPP
  database: 'youtube_db',  // Nom de la base de données
});

// Connexion à la base de données
db.connect((err) => {
  if (err) {
    console.error('Erreur de connexion à la base de données:', err);
  } else {
    console.log('Connexion à la base de données réussie !');
  }
});

// Test de la base de données
app.get('/test-db', (req, res) => {
  db.query('SELECT * FROM videos', (err, results) => {
    if (err) {
      res.status(500).json({ error: 'Erreur de la base de données' });
    } else {
      res.status(200).json(results);
    }
  });
});

// Démarrer le serveur
const PORT = 5000;
app.listen(PORT, () => {
  console.log(`Serveur backend démarré sur http://localhost:${PORT}`);
});
````

