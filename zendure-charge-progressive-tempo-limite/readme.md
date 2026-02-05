***

# 📘 README.md — Blueprint Home Assistant

# **Zendure – Charge Progressive Tempo + Solaire + Limitation Puissance**

Ce blueprint Home Assistant permet de **gérer intelligemment la charge d’une batterie Zendure** (ou tout système compatible via Zendure Manager) en combinant :

*   la **couleur Tempo du lendemain** (Blanc / Rouge)
*   la **prévision solaire du lendemain**
*   le **niveau de batterie**
*   la **puissance instantanée consommée par la maison**
*   la **puissance maximale du contrat électrique** (3 à 36 kVA)

*   **Avant toute chose il faudra désactiver le HEMS, sinon le pilotage, via Home Assistant, ne fonctionnera pas**

Grâce à ces données, la puissance de charge est ajustée dynamiquement pour **optimiser la consommation**, **éviter les dépassements**, et **maximiser l’utilisation de l’énergie solaire**.

***

# 📑 Fonctionnalités

### ✔ Charge intelligente basée sur :

*   Couleur Tempo du lendemain (aucune charge en Bleu)
*   Prévision solaire (aucune charge si production élevée)
*   Niveau de batterie (faible/moyen/haut → charge rapide/normale/douce)

### ✔ Limitation automatique en fonction de la puissance disponible

Le blueprint calcule en continu :

    marge_dispo = puissance_max_contrat_watts - puissance_maison

La puissance de charge est automatiquement ajustée pour **ne jamais** dépasser la puissance souscrite.

### ✔ Mode AC configuré automatiquement

Basculé en mode « input » lorsque la charge réseau est activée.

### ✔ Recalcul périodique

Le comportement peut être recalculé via :

*   une planification (toutes les X minutes)
*   un changement de la consommation maison
*   les mises à jour des capteurs

### ✔ Compatible avec :

*   Zendure SuperBase
*   Zendure Manager (intégration Home Assistant)
*   Capteurs Linky / Suivi de consommation
*   Prévisions solaires (Solar Forecast, Tomorrow\.io, etc.)

***

# 📥 Installation

[![Importer dans Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fzarzak12%2Fhomeassistant-blueprints%2Fblob%2F7023bdc64726c464a144894bd5cc2477571ccb0c%2Fzendure-charge-progressive-tempo-limite%2Fzendure-charge-progressive-tempo-limite.yaml)

1.  Copier le fichier `blueprint.yaml` dans :
        config/blueprints/automation/
2.  Redémarrer Home Assistant ou recharger les blueprints.
3.  Dans *Automatisations*, cliquer sur **Créer depuis un blueprint**.
4.  Sélectionner :  
    **Zendure – Charge Progressive Tempo + Solaire + Limitation Puissance**

***

# ⚙️ Configuration des Inputs

Voici les entités à fournir :

| Input                   | Description                                   |
| ----------------------- | --------------------------------------------- |
| `tempo_sensor`          | Couleur Tempo du lendemain                    |
| `battery_level_sensor`  | Niveau actuel de la batterie (%)              |
| `solar_forecast_sensor` | Prévision solaire du lendemain (kWh)          |
| `zendure_manager`       | Entité de contrôle du Zendure Manager         |
| `input_limit`           | Puissance de charge (W)                       |
| `ac_mode`               | Sélecteur du mode AC                          |
| `charge_time`           | Heure de déclenchement initial                |
| `home_power_sensor`     | Puissance instantanée consommée par la maison |
| `puissance_max_kva`     | Puissance souscrite (3 à 36 kVA)              |

### Seuils configurables :

*   Niveau batterie faible / moyen
*   Seuil solaire faible / moyen / élevé
*   Puissance charge douce / normale / forte

***

# 🔍 Fonctionnement détaillé

### 🟦 1. Vérification Tempo

Si **Bleu → pas de charge**  
Si **Blanc/Rouge → charge potentielle**

***

### ☀️ 2. Analyse des prévisions solaires

*   Soleil élevé → **charge réseau coupée**
*   Soleil moyen → **charge douce**
*   Peu de soleil → priorité au **niveau batterie**

***

### 🔋 3. Analyse du niveau de batterie

| Niveau batterie | Mode choisi    |
| --------------- | -------------- |
| < seuil bas     | Charge rapide  |
| < seuil moyen   | Charge normale |
| > seuil moyen   | Charge douce   |

***

### ⚡ 4. Limitation automatique de la puissance

Avant toute charge, le blueprint calcule :

    puissance_max_w = kVA * 1000
    marge_dispo = puissance_max_w - puissance_maison

Puis :

    puissance_effective = min(puissance_voulue, marge_dispo)

La charge est réduite automatiquement en cas de forte consommation :

*   Spa / ballon ECS
*   Four / plaques
*   Chauffage électrique
*   Pompes / compresseurs, etc.

***

# 🧪 Exemples de comportement

### 🔋 Exemple 1 — Batterie faible, peu de soleil, maison peu chargée

→ Charge rapide à 1700 W

### ☀️ Exemple 2 — Soleil fort prévu

→ Charge réseau coupée

### ⚡ Exemple 3 — Maison consomme presque tout

→ Charge réduite à 200 W

### 🏡 Exemple 4 — Maison dépasse la puissance max

→ Charge réglée à 0 W pour ne pas aggraver le dépassement

### 🌤️ Exemple 5 — Soleil moyen, marge faible

→ Charge douce limitée à la marge disponible

***

# 🔧 Conseils d’utilisation

*   Utiliser un capteur **Linky** ou équivalent pour `home_power_sensor`
*   Définir correctement `puissance_max_kva` selon votre contrat EDF
*   Ajouter un déclencheur périodique pour recalcul automatique :
    ```yaml
    trigger:
      - platform: time_pattern
        minutes: "/2"
    ```
*   Vous pouvez aussi déclencher sur changement de consommation :
    ```yaml
    - platform: state
      entity_id: !input home_power_sensor
    ```

***

# 🧱 Limites connues

*   Si des appareils créent de très fortes variations instantanées, vous pouvez ajouter un **hystérésis** pour stabiliser la charge.
*   Le Zendure peut avoir un temps de réaction de quelques secondes.

***

# 🛠️ Contribution

Les contributions sont bienvenues :  
Nouvelles fonctionnalités, optimisations, idées, documentation, etc.

Vous pouvez :

*   créer une *issue*
*   proposer une *pull request*
*   discuter d’améliorations

***

# 📄 Licence

Ce blueprint est publié sous licence **MIT**.  
Vous êtes libre de le modifier, adapter, redistribuer.

***

Tu veux une version plus visuelle ? Une mise en page GitHub stylée ?
