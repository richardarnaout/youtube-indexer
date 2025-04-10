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

bash
Copier
Modifier
mkdir backend
cd backend
1.2 Initialiser le Projet Node.js
Initialisez un projet Node.js :

bash
Copier
Modifier
npm init -y
1.3 Installer les Dépendances Backend
Installez les dépendances nécessaires pour le backend :

bash
Copier
Modifier
npm install express mysql dotenv
1.4 Créer le Fichier index.js
Créez un fichier index.js et ajoutez le code suivant pour configurer le serveur backend :

js
Copier
Modifier
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
1.5 Créer le Fichier .env
Dans le dossier backend, créez un fichier .env et ajoutez votre clé API YouTube :

env
Copier
Modifier
YOUTUBE_API_KEY=ta_cle_api_youtube
1.6 Démarrer le Serveur Backend
Lancez le serveur backend avec la commande :

bash
Copier
Modifier
node index.js
2. Frontend (React avec Vite)
2.1 Créer le Répertoire du Projet Frontend
Retournez à la racine du projet, puis créez un répertoire pour le frontend :

bash
Copier
Modifier
cd ..
mkdir frontend
cd frontend
2.2 Initialiser le Projet React avec Vite
Initialisez un projet React avec Vite :

bash
Copier
Modifier
npm create vite@latest
Choisissez React comme template.

2.3 Installer les Dépendances Frontend
Installez les dépendances nécessaires pour le frontend :

bash
Copier
Modifier
npm install
2.4 Créer le Fichier App.jsx
Remplacez le contenu de src/App.jsx par le code suivant pour la recherche de vidéos YouTube et l'affichage des résultats :

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
2.5 Créer le Fichier .env pour le Frontend
Dans le dossier frontend, créez un fichier .env et ajoutez la clé API YouTube :

env
Copier
Modifier
VITE_YOUTUBE_API_KEY=ta_cle_api_youtube
2.6 Démarrer le Serveur Frontend
Lancez le serveur de développement React :

bash
Copier
Modifier
npm run dev
Accédez à l'application via http://localhost:5173.

3. Configuration de la Base de Données MySQL
3.1 Créer la Base de Données et la Table
Lancez phpMyAdmin via XAMPP, créez une base de données youtube_db, puis créez la table videos :

sql
Copier
Modifier
CREATE TABLE videos (
  id INT AUTO_INCREMENT PRIMARY KEY,
  title VARCHAR(255),
  description TEXT,
  video_id VARCHAR(255)
);
3.2 Insérer des Données dans la Base
Lors de la récupération des vidéos via l'API YouTube, insérez les informations dans la base de données MySQL avec une requête INSERT.

4. Résumé des Commandes
Voici les principales commandes pour l'installation et l'exécution des serveurs :

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
Ce guide couvre l'installation et la configuration du backend avec Node.js, du frontend avec React, ainsi que la configuration de MySQL pour l'indexation des vidéos YouTube. Utilisez ce projet pour récupérer et afficher des vidéos de YouTube tout en les stockant dans une base de données pour une utilisation ultérieure.

Avec cette structure, le README est bien ordonné et prêt pour GitHub. Vous pouvez le copier directement dans votre projet.
