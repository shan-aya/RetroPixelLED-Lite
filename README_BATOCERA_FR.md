
## 4. Installation automatique dans Batocera

Depuis la version **v3.0.0**, plus besoin d’éditer manuellement du code, gérer les formats Windows ou utiliser des consoles SSH compliquées comme PuTTY.

Un **script installateur intelligent PowerShell** fait tout automatiquement depuis votre PC.

---

### 📦 Fonctionnalités de cet installateur

* Injecte automatiquement l’adresse IP de votre panneau dans tous les scripts de communication.
* Convertit les fichiers au format Unix (LF) pour éviter les erreurs avec le Bloc-notes Windows.
* Crée les dossiers `game-start` et `game-end` dans Batocera et copie les fichiers nécessaires.
* Génère un script système (`custom.sh`) qui donne les permissions d’exécution (`chmod +x`) automatiquement à chaque démarrage.

---

### 🛠️ Pré-requis

1. Avoir votre PC et Batocera connectés sur le même réseau (ou connecter physiquement le stockage Batocera au PC).
2. Connaître l’adresse IP locale de votre panneau Retro Pixel LED (ex : `192.168.1.109`).
3. Télécharger le dossier complet `Auto Instalador Batocera` dans ce repo, disponible [ici](https://github.com/fjgordillo86/RetroPixelLED-Lite/tree/main/Batocera/Instalador%20Autom%C3%A1tico).

> [!IMPORTANT]
> Si vous avez téléchargé le zip du repo, **décompressez-le complètement** avant d’exécuter l’installateur.

---

### 💻 Étapes

1. Ouvrez le dossier `Instalador Automático` sur votre PC. Il contient ces fichiers :
   * `Ejecutar Script Instalador Batocera.bat`
   * `Script_Instalador_Batocera.ps1`
   * `pixel_start.sh`
   * `pixel_stop.sh`

3. Double-cliquez sur `Ejecutar Script Instalador Batocera.bat`.

4. Suivez les instructions dans la console :
   * **Étape 1 :** Entrez l’IP de votre panneau puis appuyez sur `Enter`.
   * **Étape 2 :** Entrez le chemin vers Batocera, soit en réseau (ex : `\\192.168.1.119` ou `\\BATOCERA`), soit la lettre de la partition si stockage connecté (ex : `E:`).

5. Le script traite les fichiers rapidement. À la fin, vous verrez `🎉 ¡INSTALACIÓN COMPLETADA!`. Appuyez sur une touche pour fermer.

<img width="1110" height="373" alt="image" src="https://github.com/user-attachments/assets/cf5f3ac3-5906-4071-ac0a-45c11341c896" />

7. **Redémarrez complètement Batocera.**

> [!CAUTION]
> Le redémarrage complet est **obligatoire** pour que `custom.sh` configure les permissions internes. Après cela, à chaque lancement ou sortie de jeu, le panneau réagit automatiquement.

## 5. Fonctionnement en temps réel

* **Au lancement d’un jeu :** Batocera envoie le système et le nom ROM. Le panneau affiche la marquise correspondante via l’index.
* **À la sortie du jeu :** Batocera envoie la commande `STOP`. Le panneau interrompt le mode Arcade et revient automatiquement aux **GIFs** ou à l’**Horloge**.
* **Gestion des erreurs :** Grâce à la cascade, si le jeu est nouveau et non encore accompagné d’une BMP, le panneau affiche le logo système, évitant un écran vide.

> [!CAUTION]
> Chaque fois que vous ajoutez ou scrapez de nouveaux jeux dans Batocera, **réexécutez le script PowerShell** sur votre PC pour mettre à jour la SD. Sinon, l’ESP32 ne saura pas que ces fichiers existent.

## 6. Configuration critique : IP fixe pour ESP32

Pour que le mode **🕹️ Arcade** fonctionne toujours correctement, l’ESP32 doit avoir une IP fixe.

> [!TIP]
> **Attribuer une IP fixe :**  
> Les scripts envoient les commandes à une IP définie dans vos scripts. Si le routeur change l’IP de l’ESP32, le panneau ne recevra plus les ordres.  
> Comment faire ?  
> 1. Accédez à votre routeur.  
> 2. Trouvez la section DHCP statique ou réservation par MAC.  
> 3. Associez la MAC de l’ESP32 à l’IP choisie (ex : `192.168.1.109`).  
> 4. Si besoin, cherchez un guide adapté à votre routeur sur votre moteur de recherche préféré.

## 7. Profitez des marquises pendant vos parties arcade !

---

N’hésitez pas à me demander si vous souhaitez des précisions, traductions complémentaires ou aides à la configuration. Je suis là pour vous accompagner ! ➡️
