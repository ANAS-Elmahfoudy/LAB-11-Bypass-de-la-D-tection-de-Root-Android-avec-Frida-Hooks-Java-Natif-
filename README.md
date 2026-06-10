# LAB-11-Bypass-de-la-D-tection-de-Root-Android-avec-Frida-Hooks-Java-Natif-
# ⚡ LAB 11 : Contournement de la Détection de Root Android avec Frida (Hooks Java & Natifs)

## 📝 Description du Projet
Ce laboratoire est dédié à l'**instrumentation dynamique** d'une application Android afin de neutraliser ses mécanismes de protection locale. L'objectif est de comprendre les techniques utilisées par les développeurs pour détecter le "root" (droits administrateur) et de mettre en œuvre des techniques de **Hooking** avancées via **Frida** pour modifier le comportement de l'application en temps réel au sein de la mémoire[cite: 5].

---

## 🛠️ Environnement de Test & Outils
* **Machine Hôte :** Windows (Python, `frida-tools` CLI)[cite: 5]
* **Appareil Cible :** Émulateur Android rooté (via ADB)[cite: 5]
* **Composant Serveur :** `frida-server` injecté dans le système cible[cite: 5]
* **Méthode d'injection :** Process Spawning (Interception au démarrage)[cite: 5]

---

## 🚀 Chronologie du Lab & Preuves en Images

### Étape 1 : Vérification des connexions ADB et initialisation
Vérification de la communication avec l'émulateur Android et préparation du transfert du binaire de Frida pour initier le serveur de communication[cite: 5].
<img width="1040" height="64" alt="Vérification des périphériques ADB" src="https://github.com/user-attachments/assets/83cc4736-6f03-42f7-a415-5eaf4adbd3d3" />

### Étape 2 : Déploiement et exécution de Frida-Server
Transfert du fichier exécutable `frida-server` vers le répertoire temporaire sécurisé `/data/local/tmp/` de l'émulateur, attribution des permissions d'exécution (`chmod 755`) et lancement en tâche de fond avec les privilèges root[cite: 5].
<img width="957" height="623" alt="Déploiement et exécution de frida-server" src="https://github.com/user-attachments/assets/f1d84b5e-422d-4695-bad5-a61520c34d55" />

### Étape 3 : Écriture et chargement des scripts de Hooking
Développement et chargement des scripts JavaScript (`bypass_root.js`, `bypass_native.js`, `anti_frida.js`) conçus pour intercepter les APIs de haut niveau (Java) et de bas niveau (C/C++ libc) de l'application[cite: 5].
<img width="1452" height="473" alt="Chargement des scripts de hook Frida" src="https://github.com/user-attachments/assets/02322eff-6fe0-43ab-850e-b4dc26e86b7d" />

### Étape 4 : Injection finale par Spawning et contournement réussi
Lancement de l'application via la commande `frida -U -f [package] -l [script]`[cite: 5]. Les hooks s'activent avant l'exécution du code de l'application, forçant les fonctions de détection de root à renvoyer systématiquement `false`[cite: 5].
<img width="1919" height="1032" alt="Bypass de détection de root réussi avec Frida" src="https://github.com/user-attachments/assets/bb5029bd-7b59-4ddc-abde-e48d6cb88770" />

---

## 📊 Compte-Rendu de TP

* **Étudiant :** Anas El Mahfoudy
* **Établissement :** École Marocaine des Sciences de l'Ingénieur (EMSI)
* **Module :** Sécurité Mobile / Pentesting
* **Sujet :** Instrumentation dynamique et contournement des sécurités locales Android[cite: 5]

---

## 🔍 Détails de la Méthodologie et des Hooks Réalisés

### 1. Hooking de la Couche Java (`bypass_root.js`)
Ce script cible les méthodes de haut niveau de l'API Android[cite: 5]. Il intercepte les fonctions de vérification de fichiers telles que `File.exists()` ou les exécutions de commandes système via `Runtime.exec()`[cite: 5]. Dès que l'application recherche des indicateurs de root comme le binaire `/system/bin/su` ou l'utilitaire `busybox`, le hook intercepte l'appel et renvoie une valeur falsifiée (`false`)[cite: 5].

### 2. Hooking de la Couche Native (`bypass_native.js`)
Pour contrer les applications qui effectuent des vérifications via le NDK (Native Development Kit), ce script se lie directement aux fonctions de la bibliothèque standard C (`libc.so`) comme `open()`, `access()`, et `stat()`[cite: 5]. Si l'application tente d'analyser les chemins système critiques à bas niveau, le hook simule une erreur d'accès ou l'inexistence du fichier[cite: 5].

### 3. Couche Anti-Analyse (`anti_frida.js`)
Un mécanisme de protection préventif injecté pour bloquer les techniques d'auto-défense de l'application[cite: 5]. Ce script empêche l'APK de scanner ses propres ports ouverts (notamment les ports par défaut de Frida `27042` et `27043`) afin de dissimuler totalement la présence du framework d'instrumentation[cite: 5].

---

## 📌 Concepts Clés à Retenir
1. **L'injection précoce (Spawning) :** L'utilisation de l'argument `-f` est cruciale en rétro-ingénierie mobile car elle suspend l'application à son démarrage exact, garantissant qu'aucun mécanisme de détection ne s'exécute avant la mise en place des hooks[cite: 5].
2. **L'illusion de la sécurité locale :** Ce lab démontre que toutes les barrières de sécurité basées uniquement sur des vérifications côté client (Java ou C) peuvent être neutralisées par un attaquant disposant d'un accès root sur l'appareil de test[cite: 5].
