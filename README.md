# Intégration et Manipulation de Données avec Kafka

Ce projet démontre l'intégration d'Apache Kafka avec Node.js pour la production et la consommation de messages, ainsi que leur stockage dans MongoDB et leur exposition via une API REST.

## Objectifs

- Acquérir des compétences pratiques dans la gestion des flux de données avec Apache Kafka
- Apprendre à intégrer Kafka avec des applications Node.js pour la production et la consommation de messages
- Stocker des données de streaming dans une base de données MongoDB
- Exposer les données via une API REST

## Architecture

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│             │    │             │    │             │    │             │
│  Producer   │───▶│    Kafka    │───▶│  Consumer   │───▶│   MongoDB   │
│             │    │             │    │             │    │             │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
                                                                │
                                                                │
                                                                ▼
                                                        ┌─────────────┐
                                                        │             │
                                                        │  REST API   │
                                                        │             │
                                                        └─────────────┘
                                                                │
                                                                │
                                                                ▼
                                                        ┌─────────────┐
                                                        │             │
                                                        │   Client    │
                                                        │             │
                                                        └─────────────┘
```

## Prérequis

- Node.js (v18 ou supérieur)
- Apache Kafka 3.9.0
- ZooKeeper (généralement fourni avec Kafka)
- MongoDB

## Installation

### 1. Installation de Kafka et ZooKeeper

1. Téléchargez Kafka 3.9.0 depuis [https://kafka.apache.org/downloads](https://kafka.apache.org/downloads)
2. Extrayez l'archive dans un dossier de votre choix

### 2. Installation de Node.js

- Téléchargez et installez Node.js depuis [https://nodejs.org/en/download](https://nodejs.org/en/download)
- OU sur Ubuntu, exécutez : `sudo snap install node --classic`

### 3. Installation de MongoDB

- Téléchargez et installez MongoDB depuis [https://www.mongodb.com/try/download/community](https://www.mongodb.com/try/download/community)
- Démarrez le service MongoDB

### 4. Installation des dépendances du projet

```bash
# Initialisez le projet Node.js (si ce n'est pas déjà fait)
npm init -y

# Installez les dépendances
npm install kafkajs express mongodb
```

## Configuration et démarrage

### 1. Démarrage de ZooKeeper

Dans un terminal, naviguez vers le répertoire Kafka et exécutez :

**Sur Linux:**

```bash
bin/zookeeper-server-start.sh config/zookeeper.properties
```

**Sur Windows:**

```bash
bin\windows\zookeeper-server-start.bat config\zookeeper.properties
```

### 2. Démarrage de Kafka

Dans un nouveau terminal, naviguez vers le répertoire Kafka et exécutez :

**Sur Linux:**

```bash
bin/kafka-server-start.sh config/server.properties
```

**Sur Windows:**

```bash
bin\windows\kafka-server-start.bat config\server.properties
```

### 3. Création d'un topic Kafka

Dans un nouveau terminal, naviguez vers le répertoire Kafka et exécutez :

**Sur Linux:**

```bash
bin/kafka-topics.sh --create --partitions 1 --replication-factor 1 --topic test-topic --bootstrap-server localhost:9092
```

**Sur Windows:**

```bash
bin\windows\kafka-topics.bat --create --partitions 1 --replication-factor 1 --topic test-topic --bootstrap-server localhost:9092
```

### 4. Démarrage du producteur de messages

Dans un nouveau terminal, exécutez :

```bash
node producer.js
```

### 5. Démarrage du consommateur de messages

Dans un nouveau terminal, exécutez :

```bash
node consumer.js
```

### 6. Démarrage de l'API REST

Dans un nouveau terminal, exécutez :

```bash
node api.js
```

## Composants du système

### Producer (producer.js)

Le producteur envoie un message "Hello KafkaJS user!" au topic Kafka "test-topic" toutes les secondes.

```javascript
const { Kafka } = require('kafkajs');
const kafka = new Kafka({
  clientId: 'my-app',
  brokers: ['localhost:9092'],
});
const producer = kafka.producer();
// ... le reste du code
```

### Consumer (consumer.js)

Le consommateur lit les messages du topic Kafka "test-topic" et les stocke dans une base de données MongoDB.

```javascript
const { Kafka } = require('kafkajs');
const { MongoClient } = require('mongodb');

// ... code de configuration

const consumer = kafka.consumer({ groupId: 'test-group' });

// ... le reste du code pour consommer les messages et les stocker dans MongoDB
```

### API REST (api.js)

L'API REST expose les endpoints suivants pour récupérer les messages stockés dans MongoDB :

```javascript
const express = require('express');
const { MongoClient } = require('mongodb');

// ... code de configuration

// Route pour récupérer tous les messages
app.get('/messages', async (req, res) => {
  /* ... */
});

// Route pour récupérer un message par ID
app.get('/messages/:id', async (req, res) => {
  /* ... */
});
```

## API Endpoints

### GET /messages

Récupère tous les messages stockés dans MongoDB, triés par ordre chronologique décroissant.

**Réponse:**

```json
[
  {
    "_id": "ObjectId",
    "message": "Hello KafkaJS user!",
    "topic": "test-topic",
    "partition": 0,
    "timestamp": "Date"
  }
  // ...autres messages
]
```

### GET /messages/:id

Récupère un message spécifique par son ID MongoDB.

**Paramètres:**

- `id`: L'identifiant MongoDB du message

**Réponse:**

```json
{
  "_id": "ObjectId",
  "message": "Hello KafkaJS user!",
  "topic": "test-topic",
  "partition": 0,
  "timestamp": "Date"
}
```

**Erreur (404):**

```json
{
  "error": "Message non trouvé"
}
```

## Flux de données

1. Le producteur (`producer.js`) génère un message toutes les secondes et l'envoie au topic Kafka "test-topic"
2. Le consommateur (`consumer.js`) lit le message du topic Kafka et l'enregistre dans la base de données MongoDB
3. L'API REST (`api.js`) permet aux clients de récupérer les messages stockés dans MongoDB

## Dépendances

- **kafkajs**: Client Kafka pour Node.js
- **express**: Framework web pour Node.js, utilisé pour créer l'API REST
- **mongodb**: Pilote MongoDB officiel pour Node.js

## Auteurs

- Votre nom - [votre@email.com](mailto:votre@email.com)

## Licence

ISC

## Ressources supplémentaires

- [Documentation Kafka](https://kafka.apache.org/documentation/)
- [Documentation KafkaJS](https://kafka.js.org/)
- [Documentation Express](https://expressjs.com/)
- [Documentation MongoDB](https://docs.mongodb.com/)
  #
