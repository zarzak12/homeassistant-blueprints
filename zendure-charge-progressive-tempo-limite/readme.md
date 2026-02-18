# 📘 README.md — Blueprint Home Assistant

# **Zendure – Charge Progressive Solaire + Tempo + Limitation Puissance (Version Simplifiée)**

Ce blueprint Home Assistant permet de **gérer intelligemment la charge d’une batterie Zendure** (SolarFlow / Hyper / AB2000X / AB3000X…), en se basant sur :

*   la **couleur Tempo du lendemain** (Bleu / Blanc / Rouge)
*   la **prévision solaire du lendemain** (faible / moyen / fort)
*   la **puissance instantanée consommée par la maison**
*   la **puissance maximale souscrite (kVA)**
*   un **seuil unique de puissance de charge**
*   un **SOC maximal ajusté automatiquement selon la météo solaire**

👉 Ce blueprint est **simple**, **prévisible**, **optimisé pour l’autoconsommation**, tout en protégeant votre installation électrique.

***

## ⚠️ Important — À lire impérativement

### **➡️ Le HEMS doit être désactivé dans l’application Zendure**
### **➡️ Le groupe de fusible de l'appariel dans l'intégration ZENDURE doit être renseigné**

Sinon, le SolarFlow ou le Hyper **ignore Home Assistant**.  
Le contrôle serait alors instable ou impossible.

---

## Prérequis

### Intégrations requises

- **[Zendure Home Assistant](https://github.com/Zendure/Zendure-HA)**  
  Gestion de la batterie Zendure (SolarFlow, Hyper, etc.)

- **[RTE Tempo](https://github.com/hekmon/rtetempo)**  
  Récupération de la couleur Tempo (Bleu / Blanc / Rouge)

- **Forecast Solar**  
  Fournit la production solaire estimée (kWh) pour le lendemain  
  👉 Capteur typique : `sensor.energy_production_tomorrow`

---

# 📑 Fonctionnalités

### ✔ Couleur Tempo utilisée pour autoriser la charge

Vous choisissez :

*   charger ou non en **Bleu**
*   charger ou non en **Blanc**
*   charger ou non en **Rouge**

### ✔ Calcul automatique du SOC maximal selon la prévision solaire

| Prévision solaire | Interprétation                | SOC max | Exemple |
| ----------------- | ----------------------------- | ------- | ------- |
| Soleil fort       | Beaucoup de production demain | Bas     | 40%     |
| Soleil moyen      | Production intermédiaire      | Moyen   | 70%     |
| Soleil faible     | Très peu de production        | Haut    | 90%     |

👉 Le blueprint arrête **automatiquement** la charge quand ce SOC est atteint.

### ✔ Puissance de charge **unique**

Vous définissez UNE puissance (ex : 1200 W).  
Elle est automatiquement **limitée** par :

    marge_dispo = puissance_contrat_W - puissance_maison

La charge s’adapte seule sans jamais dépasser la puissance de l'abonnement (ex: 9kVA).

### ✔ Mode AC automatique

Le blueprint bascule automatiquement en :

*   **input** → si la charge est active
*   **off** → si charge interdite / SOC max atteint / soleil fort

### ✔ Recalcul dynamique

Se relance automatiquement sur :

*   l'heure de départ
*   changement de puissance maison

***

# 📥 Installation

[![Importer dans Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fzarzak12%2Fhomeassistant-blueprints%2Fblob%2Fmain%2Fzendure-charge-progressive-tempo-limite%2Fzendure-charge-progressive-tempo-limite.yaml)

OU

1.  Copier le fichier `.yaml` dans :
        config/blueprints/automation/
2.  Recharger les blueprints ou redémarrer Home Assistant
3.  Créer une nouvelle automatisation
4.  Sélectionner :  
    **Zendure – Charge Progressive Solaire + Tempo + Limitation Puissance**

***

# ⚙️ Configuration des Inputs

### **Capteurs nécessaires :**

| Input                   | Description                      |
| ----------------------- | -------------------------------- |
| `tempo_sensor`          | Couleur Tempo du lendemain       |
| `battery_level_sensor`  | Niveau de batterie (%)           |
| `solar_forecast_sensor` | Prévision solaire (kWh)          |
| `home_power_sensor`     | Puissance instantanée maison (W) |
| `puissance_max_kva`     | Puissance contrat     (3–36 kVA) |

### **Contrôle Zendure :**

| Input             | Description                      |
| ----------------- | -------------------------------- |
| `zendure_manager` | Select du Zendure Manager        |
| `input_limit`     | Limite de puissance d’entrée (W) |
| `ac_mode`         | Sélecteur AC (input / off)       |
| `charge_time`     | Heure de démarrage               |

### **Paramètres de charge :**

| Input                | Description                    |
| -------------------- | ------------------------------ |
| `charge_power_limit` | Puissance unique de charge (W) |
| `max_soc_sun_low`    | SOC max si faible soleil       |
| `max_soc_sun_medium` | SOC max si soleil moyen        |
| `max_soc_sun_high`   | SOC max si soleil fort         |

### **Autorisation Tempo :**

| Input               | Description                  |
| ------------------- | ---------------------------- |
| `allow_tempo_bleu`  | Autoriser la charge en Bleu  |
| `allow_tempo_blanc` | Autoriser la charge en Blanc |
| `allow_tempo_rouge` | Autoriser la charge en Rouge |

***

# 🔍 Fonctionnement détaillé

### 1️⃣ Vérification Tempo

Si la couleur Tempo n’est pas cochée → **aucune charge**, quelle que soit la météo.

***

### 2️⃣ Analyse de la prévision solaire

Détermine le **SOC maximal** :

    faible soleil  → max_soc_sun_low
    moyen soleil   → max_soc_sun_medium
    fort soleil    → max_soc_sun_high

***

### 3️⃣ Stop automatique sur SOC maximal

Si :

    battery_level_sensor >= max_soc

→ AC = output  
→ Zendure manager = off  
→ Aucune relance

***

### 4️⃣ Gestion solaire

| Soleil | Action                    |
| ------ | ------------------------- |
| Fort   | Pas de charge             |
| Moyen  | Charge (puissance unique) |
| Faible | Charge (puissance unique) |

***

### 5️⃣ Limitation puissance contrat

    puissance_max_w = kVA * 1000
    marge_dispo = puissance_max_w - puissance_maison
    puissance_effective = min(charge_power_limit, marge_dispo)

## Ce qui signifie :

*   Si la maison consomme beaucoup → charge réduite automatiquement
*   Si marge insuffisante → charge = 0
*   Aucun risque de disjonction

***

# 🧪 Exemples d’usage

### ☀️ Exemple 1 — Fort soleil demain

→ SOC max = 40%  
→ Charge courte uniquement si Tempo autorisée

### 🌤 Exemple 2 — Soleil moyen

→ SOC max = 70%  
→ Charge à puissance fixe mais limitée par marge

### ☁️ Exemple 3 — Très mauvais temps

→ SOC max = 95%  
→ Charge plus longue pour anticiper faible production

### ⚡ Exemple 4 — Maison très énergivore

→ Charge automatiquement réduite (ex : marge 350 W → charge 350 W)

***

# 🧱 Limites

*   Les modes intelligents Zendure **ne doivent pas être actifs**
*   Une variation brutale de consommation peut entraîner des recalculs fréquents
*   Si le SolarFlow est en veille ou en erreur, HA ne peut pas forcer le mode input

***

Doit être combiné avec le : **[ZENDURE - Mode SMART en journée](https://github.com/zarzak12/homeassistant-blueprints/blob/1.0.0/zendure_mode_excedent_journee)**

***

# 🛠 Contribution

Toute contribution est bienvenue :  
amélioration du code, documentation, optimisation énergétique…

***

# 📄 Licence

Licence **MIT** — libre adaptation et redistribution.
