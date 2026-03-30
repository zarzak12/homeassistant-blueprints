# 📘 README.md — Blueprint Home Assistant

# **Zendure – Top‑Up SOC en Heures Creuses (Limite de Décharge)**

Ce blueprint Home Assistant permet de **protéger une batterie Zendure** (SolarFlow / Hyper / AB2000X / AB3000X…) contre les **décharges excessives**, en réalisant automatiquement un **Top‑Up en Heures Creuses** pour atteindre un seuil minimal de SOC.

Il fonctionne en se basant sur :

*   une **fenêtre horaire configurable** (ex : 22h → 06h)
*   un **seuil SOC minimal** à atteindre (ex : 10 %)
*   une **puissance de charge fixe**
*   le **contrôle du Zendure Manager** et du **mode AC**

👉 Ce blueprint est conçu pour **maintenir la batterie en bonne santé**, éviter les décharges profondes, et compenser plusieurs jours sans soleil.

***

## ⚠️ Important — À lire impérativement

### **➡️ Le HEMS doit être désactivé dans l’application Zendure**

Sinon, le SolarFlow / Hyper **ignore Home Assistant**, empêchant le pilotage.

### **➡️ Le groupe de fusibles doit être renseigné dans l’intégration Zendure**

Indispensable pour que le mode AC soit pilotable correctement.

***

## Prérequis

### Intégrations requises

*   **<https://github.com/Zendure/Zendure-HA>**  
    Permet le contrôle local ou cloud du mode AC, de la gestion de charge, et du Manager Zendure.

***

# 📑 Fonctionnalités

### ✔ Recharge automatique jusqu’au seuil SOC minimal

Pendant la période HC, si :

    SOC < seuil
    → Charge activée à puissance fixe

Dès que :

    SOC >= seuil
    → AC OFF + Manager OFF

### ✔ Fenêtre horaire complète (début → fin)

*   Début → vérification SOC + éventuel top‑up
*   Pendant fenêtre → mise à jour si SOC change
*   Fin de fenêtre → arrêt total

### ✔ Puissance de charge unique

Le blueprint applique une puissance fixe, ex :

    charge_power_limit = 600 W

### ✔ Notifications intégrées

Trois notifications (modifiable via notify.mobile\_app) :

*   début fenêtre (si top‑up lancé)
*   seuil atteint
*   fin de fenêtre

### ✔ Respecte totalement la logique Zendure

*   Mode AC bascule automatiquement (input / off / output si souhaité)
*   Le Manager peut être activé ou non selon besoin

***

# 📥 Installation

👉 Blueprint prêt à importer dans Home Assistant :

<https://my.home-assistant.io/badges/blueprint_import.svg](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://github.com/zarzak12/homeassistant-blueprints/new/main/zendure-limite-decharge/zendure-limite-decharge.yaml)>

1.  Copier le fichier `.yaml` dans :
        config/blueprints/automation/
2.  Recharger les blueprints ou redémarrer Home Assistant
3.  Créer une nouvelle automatisation
4.  Sélectionner :  
    **Zendure – Top‑Up SOC en Heures Creuses (Limite de Décharge)**

***

# ⚙️ Configuration des Inputs

### **Capteurs nécessaires**

| Input                  | Description            |
| ---------------------- | ---------------------- |
| `battery_level_sensor` | Niveau de batterie (%) |

***

### **Contrôle Zendure**

| Input             | Description                       |
| ----------------- | --------------------------------- |
| `zendure_manager` | Select du Zendure Manager         |
| `ac_mode`         | Sélecteur AC (input / off/output) |
| `input_limit`     | Limite de puissance d’entrée (W)  |

***

### **Configuration horaire**

| Input        | Description |
| ------------ | ----------- |
| `start_time` | Début HC    |
| `end_time`   | Fin HC      |

***

### **Paramètres de charge**

| Input                | Description                      |
| -------------------- | -------------------------------- |
| `target_soc`         | SOC minimal à atteindre (%)      |
| `charge_power_limit` | Puissance de charge utilisée (W) |

***

# 🔍 Fonctionnement détaillé

### 1️⃣ Début de fenêtre

*   Notification
*   Vérifie le SOC
*   Si SOC < seuil → charge activée
*   Si SOC ≥ seuil → rien n'est fait

***

### 2️⃣ Pendant la fenêtre

*   Si SOC augmente → vérification
*   Si SOC ≥ seuil à ce moment → arrêt + notification

***

### 3️⃣ Fin de fenêtre

*   AC OFF
*   Manager OFF
*   Notification

***

# 🧪 Exemples d’usage

### 🌧 Exemple 1 — Plusieurs jours sans soleil

Le SOC descend à 8 % →  
→ Le top‑up remonte automatiquement à 10 %.

### ☀️ Exemple 2 — La batterie est déjà à 20 % à 22h

→ Aucune charge déclenchée  
→ Notification “pas de top‑up nécessaire”.

### ⚡ Exemple 3 — Coupure de courant le soir

→ La fenêtre recommence proprement et recharge si SOC < seuil.

***

# 🧱 Limites

*   Ne gère pas les prévisions solaires (c’est volontaire, voir le blue print zendure-charge-progressive-HC-limite).
*   Ne limite pas par puissance contrat (également volontaire pour simplifier).
*   Le mode AC doit être pilotable dans Zendure.
*   Si la batterie est en veille profonde, elle peut refuser la charge tant que le système n’est pas réveillé.

***

# 🛠 Contribution

Toute contribution est bienvenue :  
optimisation, refactorisation, documentation…

***

# 📄 Licence

Licence **MIT** — libre adaptation et redistribution.

