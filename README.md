# Cité des Îles 2

![Java](https://img.shields.io/badge/java-%23ED8B00.svg?style=for-the-badge&logo=openjdk&logoColor=white)
![Gradle](https://img.shields.io/badge/Gradle-02303A.svg?style=for-the-badge&logo=Gradle&logoColor=white)
![Node](https://img.shields.io/badge/node.js-6DA55F?style=for-the-badge&logo=node.js&logoColor=white)
![TypeScript](https://img.shields.io/badge/typescript-%23007ACC.svg?style=for-the-badge&logo=typescript&logoColor=white)
![Discord JDA](https://img.shields.io/badge/Discord-JDA-%235865F2.svg?style=for-the-badge&logo=discord&logoColor=white)
![MySQL](https://img.shields.io/badge/mysql-%234479A1.svg?style=for-the-badge&logo=mysql&logoColor=white)

Repo principal du projet Cité des Îles 2. Ce projet n'est pas entièrement terminée et ne contient pas les modes de jeu du soir qui reste au Cripie Club.

Le projet étant annulé, avec l'accord de tout le monde j'ai décidé de mettre le projet en public si des gens veulent le reprendre ou voir ce que l'on voulait faire.

Ce repo utilise des submodules, il n'y a pas de code ici directement. Vous pouvez aussi aller voir les repos séparément.

## Crédits

- Chefs de Projet: Azrotho & Cripie
- Développeurs: Azrotho, Raraph84
- Build: Ninox, Cripie, Fyn
- Système: Raraph84 avec [Polycube.fr](https://polycube.fr)

## Les projets

- `core/` : [cdi2-core-plugin](https://github.com/Azrotho/cdi2-core-plugin)
  Lib Java commune à tous les plugins elle parle à `core-server`. À builder et publier en `mavenLocal` en premier.

- `core-server/` : [cdi2-core-server](https://github.com/Azrotho/cdi2-core-server)
  API centrale Express + TypeScript + MySQL (`mysql2/promise`), C'est elle qui s'occupe de communiquer avecl a BD et de fournir les infos, y'a eu pour projet de créer une partie publique d'où les tokens "d'admin" mais pas implémenter

- `cite/` : [cdi2-cite-plugin](https://github.com/Azrotho/cdi2-cite-plugin)
  Plugin du serveur Cité (Paper 1.21.x, `paperweight`). NPCs via NpcApi, leaderboards têtes/stats, stats, scoreboard, dépend de `plugin-core`.

- `land/` : [cdi2-land-plugin](https://github.com/Azrotho/cdi2-land-plugin)
  Plugin du serveur Land (Paper). Système de corruption de la cité 1, dépend du `plugin-core`.

- `inscription/` : [cdi2-inscription-plugin](https://github.com/Azrotho/cdi2-inscription-plugin)
  Plugin du serveur inscription (Paper). `/link`, `/unlink`, protection du spawn, vérification des teams. Dépend de `plugin-core`.

- `discord-bot/` : [cdi2-discord-bot](https://github.com/Azrotho/cdi2-discord-bot)
  Bot Discord standalone (JDA). Slash commands link/team. Dépend de `plugin-core`.

Tous les plugins Java sont en Gradle, toolchain Java 25, et embarquent leurs deps dans le jar (`runtimeClasspath`).

## Récupérer le projet

```bash
git clone --recurse-submodules https://github.com/Azrotho/CiteDesIles2.git
```

Si vous avez déjà cloné sans les submodules:

```bash
git submodule update --init --recursive
```

Pour mettre à jour:

```bash
git submodule update --remote --merge
```

## Build

Prérequis: JDK 25, Gradle (wrapper inclus), Node 22 + npm, MySQL.

Ordre important : `core` d'abord, car les autres le récupère via le Maven local.

```bash
# 1. Lib commune
cd core && ./gradlew publishToMavenLocal

# 2. Serveur central
cd ../core-server && npm ci && npm run build

# 3. Plugins (un par un)
cd ../cite && ./gradlew build
cd ../land && ./gradlew build
cd ../inscription && ./gradlew build
cd ../discord-bot && ./gradlew build
```

Config :

- `core-server/` : copier `.env.example` vers `.env` et remplir la connexion MySQL + tokens (`CORE_API_URL`, `CORE_API_TOKEN` côté plugins/tests).
- `discord-bot/` : copier `config.json.example` vers `config.json` (`discord_token`, `core_api_url`, `core_api_token`), ou utiliser `DISCORD_TOKEN`, `API_URL`, `API_TOKEN`.
- `core/` : pour les tests d'intégration, copier `.env.example` vers `.env`.

## Comment ça marche

On bosse dans chaque repo séparément, puis ici on met juste à jour le pointeur :

```bash
cd cite
# modifs, commit, push

cd ..
git add cite
git commit -m "maj cite"
```

Par rapport à la cité 1, les plugins et le bot n'ont pas accès directement à la BD et passe par l'API (`core-server`)