# 🎓 Bot_ENT — Bot de Notification de Notes ENT UH2C

> Bot de surveillance automatique des notes pour les étudiants de l'**Université Hassan II de Casablanca** (FSBM et autres facultés). Reçois une alerte Telegram dès que de nouvelles notes apparaissent sur ton dossier pédagogique ENT — gratuitement, 24h/24.

---

## ✨ Fonctionnalités
- 🤖 Connexion automatique et sécurisée au portail ENT
- 🔍 Détection des changements dans les notes par comparaison d'état
- 📲 Notification Telegram avec un résumé des nouveautés détectées
- ☁️ Exécution autonome via GitHub Actions (lun–ven, 8h–18h) — aucun serveur à maintenir
- 🔒 Zéro credential en clair — tout passe par les GitHub Secrets

---

## 🗂️ Structure du projet
```
CheckNotes/
├── bot_notes.py                    # Script principal
├── requirements.txt                # Dépendances Python
├── last_notes.txt                  # Cache de l'état (géré automatiquement par le bot)
└── .github/
    └── workflows/
        └── check_notes.yml         # Workflow GitHub Actions
```

---

## 🚀 Installation — Étape par étape

### Étape 1 — Forker ce repository
Clique sur le bouton **Fork** en haut à droite de cette page, puis **rends ton fork PRIVÉ** immédiatement :
> Settings > General > Danger Zone > Change repository visibility → **Private**

> ⚠️ **Indispensable.** Ton fork contiendra tes identifiants ENT (via Secrets) et l'historique de tes notes. Un repo public exposerait tes données personnelles.

---

### Étape 2 — Créer ton Bot Telegram
1. Ouvre Telegram et cherche **@BotFather**
2. Envoie `/newbot` et suis les instructions
3. Copie le **Token API** (ex : `123456789:ABCdefGHIjklmNOPqrsTUVwxyz`)
4. Cherche **@userinfobot** et envoie-lui un message pour obtenir ton **Chat ID**
5. Envoie un message à ton bot pour initier la conversation (obligatoire)

---

### Étape 3 — Trouver ton code de promotion
Connecte-toi sur l'ENT, va dans **Notes et résultats**, et repère le lien cliquable de ta filière. Son texte est ton `PROMO_CODE`.

Exemples :
| Filière | PROMO_CODE |
|---|---|
| Master IA (promo 2025) | `CMIAE1/25` |
| *(autre)* | *(le texte exact du lien sur l'ENT)* |

---

### Étape 4 — Configurer les GitHub Secrets
Dans ton fork : **Settings > Secrets and variables > Actions > New repository secret**

Ajoute ces **5 secrets** (noms exacts, sensibles à la casse) :

| Nom du Secret | Valeur |
|---|---|
| `ENT_USER` | Ton email universitaire |
| `ENT_PASS` | Ton mot de passe ENT |
| `TG_TOKEN` | Le token fourni par @BotFather |
| `TG_CHAT_ID` | Ton Chat ID Telegram |
| `PROMO_CODE` | Ton code de promotion (ex: `CMIAE1/25`) |

---

### Étape 5 — Activer les permissions d'écriture
Dans ton fork : **Settings > Actions > General > Workflow permissions**
→ Sélectionner **"Read and write permissions"** et sauvegarder.

---

### Étape 6 — Tester
Va dans l'onglet **Actions > Check ENT Notes > Run workflow** et clique sur **Run workflow**.
Consulte les logs pour vérifier que les 5 étapes s'exécutent sans erreur.

Si tout est bon, tu recevras un message Telegram lors du prochain changement de notes. ✅

---

## ⚙️ Technologies
- **Python 3** + **Selenium** (navigation headless via Chrome)
- **GitHub Actions** (exécution planifiée, cron)
- **API Telegram Bot** (notifications)

---

## ❓ FAQ

**Le bot se déclenche à quelle fréquence ?**
Toutes les heures, du lundi au vendredi entre 8h et 18h (UTC). Pour modifier la fréquence, édite la ligne `cron:` dans `check_notes.yml`.

**Que se passe-t-il au premier lancement ?**
Le fichier `last_notes.txt` est vide. Le bot sauvegarde l'état actuel sans envoyer de notification. Les notifications commenceront au prochain changement réel.

**Mon code de promotion n'est pas trouvé ?**
Le bot continue sur la vue globale de toutes les filières — la surveillance fonctionne quand même, mais elle est moins précise. Vérifie le texte exact du lien sur l'ENT et mets-le dans le secret `PROMO_CODE`.

**Le bot fonctionne pour d'autres universités ?**
Ce bot est conçu pour le portail ENT de l'UH2C (`entv26.univh2c.ma`). Pour une autre université, il faudrait adapter l'URL et les sélecteurs dans `bot_notes.py`.

---

## ⚠️ Sécurité & Confidentialité
- Garde ton fork en mode **Privé** à tout moment.
- Ne partage jamais les valeurs de tes Secrets GitHub.
- Le fichier `last_notes.txt` contient un historique de tes notes — il ne doit pas être visible publiquement.

---

## 🤝 Contribution
Les PR et issues sont les bienvenues ! Si tu utilises un autre portail ENT ou une autre filière et que tu as dû adapter le code, n'hésite pas à partager tes modifications.

---

*Développé par un étudiant du Master d'Excellence en IA — FSBM, Université Hassan II de Casablanca.*
