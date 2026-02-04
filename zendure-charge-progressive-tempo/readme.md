# Zendure – Charge Progressive Tempo + Solaire ☀️⚡

[![Importer dans Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fraw.githubusercontent.com%2Fzarzak12%2Fhomeassistant-blueprints%2F001357ceddfc6a0c1d0d707076edadd77a59d20a%2Fzendure-charge-progressive-tempo%2Fzendure-charge-progressive-tempo.yaml)

---

## Description

Ce blueprint permet de **charger automatiquement une batterie Zendure** de manière intelligente et progressive en fonction :

- 🔋 du **niveau de charge actuel de la batterie**
- 🎨 de la **couleur Tempo prévue** par RTE (Blanc ou Rouge)
- ☀️ de la **prévision de production solaire du lendemain**

L’objectif est d’**optimiser la charge en Heures Creuses**, d’éviter toute charge réseau inutile lorsque le soleil est suffisant, et d’adapter automatiquement la puissance de charge selon le contexte énergétique.

---

## Fonctionnalités

### ☀️ Gestion intelligente selon la prévision solaire

La charge réseau est automatiquement ajustée en fonction de la production solaire prévue pour le lendemain (Forecast Solar).

| Prévision solaire | Comportement |
|------------------|--------------|
| ☀️ Élevée (≥ seuil haut) | ❌ Aucune charge réseau |
| 🌤️ Moyenne (entre seuil moyen et seuil haut) | 🔋 Charge douce |
| 🌧️ Faible (< seuil moyen) | ⚡ Charge normale ou rapide |

👉 **Tous les seuils sont paramétrables dans Home Assistant.**

---

### 🔋 Charge progressive selon le niveau de batterie

Lorsque la charge réseau est autorisée, la puissance est ajustée automatiquement selon le niveau de batterie :

| Niveau de batterie | Puissance par défaut | Mode |
|-------------------|---------------------|------|
| < 30% | 1700 W | Charge rapide |
| 30% – 60% | 1200 W | Charge normale |
| > 60% | 600 W | Charge d’appoint |

---

### ⏰ Déclenchement intelligent

- ⏱ **Déclenchement horaire** (par défaut à 22h00)
- 🎨 **Condition Tempo** : uniquement les jours **Blanc ou Rouge**
- ☀️ **Prise en compte de la prévision solaire**
- 🔁 **Gestion entièrement automatique**, sans intervention manuelle

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

## Entités nécessaires

- Capteur RTE Tempo (couleur du lendemain)
- Capteur de niveau de batterie Zendure (%)
- Entités Zendure :
  - Zendure Manager (`select`)
  - Limite d’entrée réseau (`number`)
  - Mode AC (`select`)
- Capteur Forecast Solar (kWh demain)

---

## Installation

### Via l’interface Home Assistant (recommandé)

1. Aller dans **Paramètres**
2. **Automatisations & scènes**
3. Onglet **Blueprints**
4. Cliquer sur **Importer un blueprint**
5. Coller l’URL suivante :

https://raw.githubusercontent.com/zarzak12/homeassistant-blueprints/main/zendure-charge-progressive-tempo/zendure_charge_progressive_tempo.yaml


6. Cliquer sur **Aperçu** puis **Importer**

---

### Installation manuelle

Copier le fichier :

zendure_charge_progressive_tempo.yaml


dans le dossier :

/config/blueprints/automation/zendure-charge-progressive-tempo/


Puis recharger les blueprints dans Home Assistant.

---

## Configuration

### Paramètres obligatoires

| Paramètre | Description |
|----------|-------------|
| **Capteur Tempo** | Capteur RTE Tempo du lendemain |
| **Prévision solaire demain** | Capteur Forecast Solar (kWh) |
| **Niveau de batterie** | Capteur % Zendure |
| **Zendure Manager** | Entité de contrôle Zendure |
| **Limite d’entrée** | Puissance de charge réseau |
| **Mode AC** | Mode AC Zendure (input) |

---

### Paramètres solaires (personnalisables)

| Paramètre | Valeur par défaut | Description |
|---------|------------------|-------------|
| **Seuil solaire moyen** | 3.5 kWh | Charge réseau réduite |
| **Seuil solaire élevé** | 7 kWh | Pas de charge réseau |

---

### Paramètres batterie

| Paramètre | Valeur par défaut |
|---------|------------------|
| **Seuil batterie faible** | 30 % |
| **Seuil batterie moyenne** | 60 % |

---

### Paramètres de puissance

| Paramètre | Valeur par défaut |
|---------|------------------|
| **Charge rapide** | 1700 W |
| **Charge normale** | 1200 W |
| **Charge douce** | 600 W |

---

## Exemples d’utilisation

### Exemple 1  
- Tempo : **Blanc**
- Prévision solaire : **0,7 kWh**
- Batterie : **25 %**

➡️ **Charge rapide à 1700 W**

---

### Exemple 2  
- Tempo : **Rouge**
- Prévision solaire : **1,5 kWh**
- Batterie : **70 %**

➡️ **Charge douce à 600 W**

---

### Exemple 3  
- Tempo : **Blanc**
- Prévision solaire : **2,8 kWh**

➡️ ❌ **Aucune charge réseau**

---

## Personnalisation

Ce blueprint est facilement extensible :

- Ajout des **Heures Creuses / Heures Pleines**
- Limitation de l’import réseau (Shelly / Linky)
- Prise en compte de l’injection solaire réelle
- Gestion multi-batteries Zendure

---

## Support

Pour toute question, bug ou suggestion d’amélioration :  
👉 ouvrez une issue sur GitHub.

---

## Licence

Ce blueprint est fourni tel quel, libre d’utilisation et de modification.
