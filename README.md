Tutoriel d'Installation et de Configuration - YouTube Indexer
Ce guide explique les étapes pour configurer un projet d'indexation de vidéos YouTube avec un backend Node.js, un frontend React, et une base de données MySQL.

Prérequis
Avant de commencer, il faut avoir installé les outils suivants :

Node.js : Télécharger et installer Node.js

XAMPP : Télécharger et installer XAMPP (pour MySQL et Apache)

MySQL Workbench (optionnel) : Utiliser MySQL Workbench pour gérer la base de données (télécharger depuis MySQL Workbench)

1. Initialisation du Backend (Node.js)
1.1 Créer le répertoire du projet Backend
Dans le terminal, se rendre dans le répertoire du projet et créer un dossier pour le backend :

bash
Copier
Modifier
mkdir backend
cd backend
1.2 Initialiser le projet Node.js
Initialiser un projet Node.js dans le dossier backend :

bash
Copier
Modifier
npm init -y
1.3 Installer les dépendances Backend
Installer les modules nécessaires pour le backend :

bash
Copier
Modifier
npm install express mysql dotenv
1.4 Créer la structure du Backend
Créer un fichier index.js dans le répertoire backend et y insérer le code suivant :

js
Copier
Modifier
const express = require('express');
const mysql = require('mysql');
const dotenv = require('dotenv');

dotenv.config();  // Charger les variables d'environnement depuis .env

const app = express();
app.use(express.json());  // Pour permettre l'envoi de données JSON

// Configuration de la base de données MySQL
const db = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: '',  // Mot de passe par défaut dans XAMPP
  database: 'youtube_db',  // Remplacer par le nom de la base de données
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
1.5 Créer le fichier .env
Dans le dossier backend, créer un fichier .env et y ajouter la clé API YouTube :

env
Copier
Modifier
YOUTUBE_API_KEY=ta_cle_api_youtube
1.6 Démarrer le serveur Backend
Dans le terminal, lancer le serveur :

bash
Copier
Modifier
node index.js
2. Initialisation du Frontend (React avec Vite)
2.1 Créer le répertoire du projet Frontend
Retourner à la racine du projet et créer un répertoire pour le frontend :

bash
Copier
Modifier
cd ..
mkdir frontend
cd frontend
2.2 Initialiser le projet React avec Vite
Initialiser un projet React avec Vite :

bash
Copier
Modifier
npm create vite@latest
Choisir React comme template.

2.3 Installer les dépendances Frontend
Dans le répertoire frontend, installer les modules nécessaires :

bash
Copier
Modifier
npm install
2.4 Ajouter le code React dans App.jsx
Remplacer le contenu de src/App.jsx par le code suivant :

js
Copier
Modifier
import React, { useState } from 'react';
import './App.css';

function App() {
  const [searchTerm, setSearchTerm] = useState('');
  const [videos, setVideos] = useState([]);

  const handleSearchChange = (e) => {
    setSearchTerm(e.target.value);
  };

  const fetchVideos = async (query) => {
    const API_KEY = import.meta.env.VITE_YOUTUBE_API_KEY;

    try {
      const response = await fetch(
        `https://www.googleapis.com/youtube/v3/search?part=snippet&q=${query}&type=video&key=${API_KEY}`
      );
      const data = await response.json();
      setVideos(data.items);
    } catch (error) {
      console.error('Erreur lors de la récupération des vidéos:', error);
    }
  };

  const handleSearchSubmit = (e) => {
    e.preventDefault();
    if (searchTerm) {
      fetchVideos(searchTerm);
    }
  };

  return (
    <div className="App">
      <div className="container">
        <h1 className="title">VI</h1>
        <form onSubmit={handleSearchSubmit}>
          <input
            type="text"
            className="search-bar"
            placeholder="Rechercher des vidéos..."
            value={searchTerm}
            onChange={handleSearchChange}
          />
        </form>

        {/* Affichage des vidéos */}
        <div className="video-list">
          {videos.length > 0 ? (
            videos.map((video) => (
              <div key={video.id.videoId} className="video-item">
                <a
                  href={`https://www.youtube.com/watch?v=${video.id.videoId}`}
                  target="_blank"
                  rel="noopener noreferrer"
                >
                  <img
                    src={video.snippet.thumbnails.medium.url}
                    alt={video.snippet.title}
                    className="video-thumbnail"
                  />
                  <div className="video-details">
                    <h2>{video.snippet.title}</h2>
                    <p>{video.snippet.description}</p>
                  </div>
                </a>
              </div>
            ))
          ) : (
            <p>Aucune vidéo trouvée. Essayez un autre mot-clé.</p>
          )}
        </div>
      </div>
    </div>
  );
}

export default App;
2.5 Ajouter la clé API dans .env pour le Frontend
Dans le dossier frontend, créer un fichier .env et y ajouter :

env
Copier
Modifier
VITE_YOUTUBE_API_KEY=ta_cle_api_youtube
2.6 Démarrer le serveur Frontend
Lancer le serveur de développement React :

bash
Copier
Modifier
npm run dev
Accéder à l'application dans le navigateur via http://localhost:5173.

3. Configuration de la Base de Données MySQL
3.1 Créer la base de données et la table
Lancer phpMyAdmin via XAMPP et créer une base de données nommée youtube_db. Ensuite, créer une table videos avec la structure suivante :

sql
Copier
Modifier
CREATE TABLE videos (
  id INT AUTO_INCREMENT PRIMARY KEY,
  title VARCHAR(255),
  description TEXT,
  video_id VARCHAR(255)
);
3.2 Insertion des données dans la base
Lors de la récupération des vidéos via l'API YouTube, insérer les informations dans la base de données MySQL avec une requête INSERT.

4. Résumé des Commandes
Voici les principales commandes utilisées pour l'installation et l'exécution des serveurs :

Backend (Node.js)
Installer les dépendances :

bash
Copier
Modifier
npm install
Démarrer le serveur backend :

bash
Copier
Modifier
node index.js
Frontend (React avec Vite)
Installer les dépendances frontend :

bash
Copier
Modifier
npm install
Démarrer le serveur frontend :

bash
Copier
Modifier
npm run dev
Conclusion
Ce guide couvre l'installation et la configuration du backend avec Node.js, du frontend avec React, ainsi que la base de données MySQL pour l'indexation des vidéos YouTube. Utilisez les étapes ci-dessus pour mettre en place et démarrer votre projet.

