# SuperAuth - Plugin d'authentification Minecraft

## Description
SuperAuth est un plugin d'authentification pour serveurs Minecraft qui permet aux joueurs de s'inscrire et se connecter avec un mot de passe sécurisé. Il inclut un système d'actions configurables pour personnaliser complètement l'expérience utilisateur.

## Fonctionnalités
- ✅ Inscription sécurisée avec validation de mot de passe
- ✅ Connexion avec authentification
- ✅ Changement de mot de passe
- ✅ Système de captcha pour détecter les bots
- ✅ Sauvegarde des positions des joueurs
- ✅ Messages configurables
- ✅ Commandes administrateur avancées
- ✅ Protection contre les VPN/bots (simulation)
- ✅ **Limitation 1 compte par IP (configurable)**
- ✅ **Gestion des box qui changent d'IP**
- ✅ **Modification des messages en temps réel**
- ✅ **Modification de la limitation IP en temps réel**
- ✅ **Système d'actions configurables**
- ✅ **Support multi-bases de données (YAML, SQLite, MySQL)**
- ✅ **Support multi-langues**
- ✅ **Algorithmes de hachage configurables avec salt**
- ✅ **Commandes d'information serveur et migration**

## Installation
1. Téléchargez le fichier JAR depuis la section Releases
2. Placez-le dans le dossier `plugins` de votre serveur
3. Redémarrez le serveur
4. Le plugin créera automatiquement les fichiers de configuration

## Configuration
Le plugin génère automatiquement un fichier `config.yml` dans le dossier `plugins/SuperAuth/` avec les paramètres suivants :

### Messages
Tous les messages du plugin sont configurables dans la section `messages` :
```yaml
messages:
  prefix: "&8[&cSuperAuth&8] &r"
  login:
    success: "&aConnexion réussie !"
    failed: "&cMot de passe incorrect !"
    required: "&cVous devez vous connecter avec /login <mot_de_passe>"
  register:
    ip_limit_exceeded: "&cCette adresse IP a atteint la limite de comptes autorisés ({limit} comptes max)."
```

### Actions Configurables
Le système d'actions vous permet de personnaliser ce qui se passe avant et après l'authentification :

```yaml
actions:
  # Actions exécutées après l'inscription
  after_register:
    - "msg:&aInscription réussie ! Bienvenue sur le serveur !"
    - "cmd:spawn"
    - "console:say &a%player_name% vient de s'inscrire sur le serveur !"
  
  # Actions exécutées après la connexion
  after_login:
    - "msg:&aConnexion réussie ! Bienvenue de retour !"
    - "lastlocation:back"
    - "console:say &a%player_name% vient de se connecter !"
```

**Types d'actions disponibles :**
- `msg` - Envoyer un message au joueur
- `cmd` - Exécuter une commande joueur
- `console` - Exécuter une commande console
- `teleport` - Téléporter le joueur
- `server` - Envoyer vers un autre serveur (BungeeCord)
- `lastlocation` - Gérer la dernière position
- `wait` - Attendre un délai

**Placeholders disponibles :**
- `%player_name%` - Nom du joueur
- `%player_uuid%` - UUID du joueur
- `%player_ip%` - Adresse IP du joueur
- `%server_name%` - Nom du serveur
- `%online_players%` - Joueurs en ligne
- `%max_players%` - Joueurs maximum

### Base de Données
Support de plusieurs types de bases de données :

```yaml
database:
  # Type: YAML, SQLITE, MYSQL
  type: "YAML"
  
  # Configuration MySQL
  mysql:
    host: "localhost"
    port: 3306
    database: "superauth"
    username: "root"
    password: "password"
    
  # Configuration SQLite
  sqlite:
    filename: "superauth"
    folder: "database"
```

### Sécurité
```yaml
security:
  min_password_length: 6
  min_password_strength: 2
  login_timeout: 60
  max_login_attempts: 3
  # Algorithmes: MD2, MD5, SHA1, SHA256, SHA384, SHA512
  hash_algorithm: "SHA256"
  use_salt: true
  salt_length: 16
```

### Limitation par IP
```yaml
ip_limitation:
  enabled: true
  max_accounts_per_ip: 1  # Nombre de comptes par IP
  allow_admin_bypass: true
  allow_ip_change: false
  ip_change_cooldown: 24
  admin_multiple_accounts: true
```

## Commandes

### Joueurs
- `/register <mot_de_passe> <confirmation>` - S'inscrire
- `/login <mot_de_passe>` - Se connecter
- `/changepassword <ancien_mdp> <nouveau_mdp> <confirmation>` - Changer son mot de passe

### Administrateurs
- `/sauth` - Afficher l'aide des commandes admin
- `/sauth unregister <pseudo>` - Supprimer un compte
- `/sauth bypassip <pseudo>` - Contourner la limitation IP
- `/sauth reload` - Recharger la configuration
- `/sauth info` - **Nouveau** : Informations détaillées du serveur
- `/sauth migrate <source> <destination>` - **Nouveau** : Migrer entre bases de données
- `/sauth setmessage <chemin> <message>` - Modifier un message
- `/sauth setiplimit <paramètre> <valeur>` - Modifier la limitation IP
- `/sauth setsecurity <paramètre> <valeur>` - Modifier la sécurité
- `/sauth getvalue <chemin>` - Voir la valeur d'un paramètre

#### Exemples de commandes admin :

**Informations du serveur :**
```
/sauth info
```

**Migration de base de données :**
```
/sauth migrate YAML MYSQL
/sauth migrate SQLITE YAML
```

**Modifier un message :**
```
/sauth setmessage login.success "&aConnexion réussie !"
/sauth setmessage register.ip_limit_exceeded "&cIP déjà utilisée"
```

**Modifier la limitation IP :**
```
/sauth setiplimit enabled true
/sauth setiplimit max_accounts_per_ip 3
/sauth setiplimit allow_ip_change true
```

**Modifier la sécurité :**
```
/sauth setsecurity hash_algorithm SHA512
/sauth setsecurity use_salt true
/sauth setsecurity min_password_length 8
```

## Langues Supportées
Le plugin supporte plusieurs langues :
- Français (fr) - Par défaut
- Anglais (en)
- Espagnol (es)
- Allemand (de)
- Italien (it)
- Portugais (pt)
- Russe (ru)
- Chinois (zh)
- Japonais (ja)
- Coréen (ko)

Configuration :
```yaml
language: "fr"
```

## Critères de sécurité des mots de passe
- Au moins 6 caractères (configurable)
- Force minimale configurable (0-7)
- Support de multiples algorithmes de hachage
- Salt optionnel pour renforcer la sécurité

## Limitation par IP

### Fonctionnement
- **Comptes par IP** : Configurable (1 par défaut, jusqu'à plusieurs)
- **Gestion des box** : Option pour permettre le changement d'IP avec un délai configurable
- **Administrateurs** : Peuvent contourner la limitation et avoir plusieurs comptes par IP

### Configuration pour les box qui changent d'IP
```yaml
ip_limitation:
  allow_ip_change: true  # Autoriser le changement d'IP
  ip_change_cooldown: 24 # Délai de 24h entre les changements
```

### Modification en temps réel
Les administrateurs peuvent modifier la limitation IP sans redémarrer le serveur :
```
/sauth setiplimit allow_ip_change true
/sauth setiplimit max_accounts_per_ip 3
```

## Fichiers générés
Le plugin crée automatiquement les fichiers suivants :
- `config.yml` - Configuration principale
- `users.yml` - Base de données des utilisateurs (si YAML)
- `last_locations.yml` - Positions sauvegardées des joueurs
- `ip_data.yml` - Données des associations IP-Joueur
- `database/` - Dossier pour SQLite (si utilisé)
- `languages/` - Fichiers de langues

## Permissions
- `superauth.admin` - Accès aux commandes administrateur

## Support
Pour toute question ou problème :
1. Consultez le guide des actions : `ACTIONS_GUIDE.md`
2. Utilisez `/sauth info` pour diagnostiquer les problèmes
3. Créez une issue sur GitHub avec les informations de `/sauth info`

## Changelog

### Version 1.0
- ✅ Système de configuration complet
- ✅ Messages configurables
- ✅ Suppression des références Discord
- ✅ Génération automatique des fichiers au démarrage
- ✅ Système de messages centralisé
- ✅ Amélioration de la sécurité
- ✅ **Limitation 1 compte par IP**
- ✅ **Gestion des box qui changent d'IP**
- ✅ **Commandes admin pour contourner la limitation IP**
- ✅ **Modification des messages en temps réel**
- ✅ **Modification de la limitation IP en temps réel**
- ✅ **Interface admin complète avec aide intégrée**
- ✅ **Système d'actions configurables**
- ✅ **Support multi-bases de données (YAML, SQLite, MySQL)**
- ✅ **Support multi-langues**
- ✅ **Algorithmes de hachage configurables avec salt**
- ✅ **Commandes d'information serveur et migration**
- ✅ **Gestion des dernières positions avec actions**
- ✅ **Support BungeeCord pour actions serveur** 