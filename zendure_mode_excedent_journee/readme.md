# Zendure - Programmation Horaire de Mode

[![Importer dans Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fzarzak12%2Fhomeassistant-blueprints%2Fblob%2Fmain%2Fzendure-mode-excedent-journee%2Fzendure_mode_excedent_journee.yaml)

## Description

Ce blueprint active automatiquement un mode de fonctionnement spécifique sur votre batterie Zendure selon une plage horaire définie.

Par défaut configuré pour le **mode smart/excédent solaire** en journée, il permet d'optimiser l'utilisation de votre production photovoltaïque. Vous pouvez également l'utiliser pour programmer la décharge en heures pleines, la charge en heures creuses, ou tout autre scénario selon vos besoins.

## Fonctionnalités

### Activation automatique

- **Mode personnalisable** : Choisissez le mode de fonctionnement (Smart par défaut pour l'excédent solaire)
- **Déclenchement horaire** : Active le mode à l'heure configurée (par défaut 6h00)
- **Persistance au redémarrage** : Se réactive automatiquement au démarrage de Home Assistant si dans la plage horaire
- **Plage horaire intelligente** : Fonctionne uniquement pendant la période définie (par défaut 6h00 - 22h00)

## Prérequis

### Intégration requise

- **[Intégration Zendure](https://github.com/Zendure/Zendure-HA)** : Gestion de votre batterie Zendure dans Home Assistant

### Entité nécessaire

- Entité Zendure Manager (select) avec les modes disponibles (smart, smart_charging, smart_discharging, manual, off)

## Installation

### Via l'interface Home Assistant

1. Accédez à **Paramètres** > **Automatisations et scènes** > **Blueprints**
2. Cliquez sur **Importer un Blueprint**
3. Collez l'URL suivante :
```
https://github.com/zarzak12/homeassistant-blueprints/edit/1.0.0/zendure_mode_excedent_journee/zendure_mode_excedent_journee.yaml
```
4. Cliquez sur **Aperçu** puis **Importer**

### Manuel

Copiez le fichier `zendure_mode_excedent_journee.yaml` dans le dossier `blueprints/automation/` de votre configuration Home Assistant.

## Configuration

### Paramètres obligatoires

| Paramètre | Description |
|-----------|-------------|
| **Zendure Manager** | Entité de contrôle Zendure (ex: `select.zendure_manager`) |

### Paramètres optionnels

| Paramètre | Valeur par défaut | Description |
|-----------|-------------------|-------------|
| **Mode de fonctionnement** | Couplage intelligent (Smart) | Mode à activer : Smart, Charge intelligente, Décharge intelligente, Manuel ou Arrêt |
| **Heure de début** | 06:00:00 | Heure d'activation du mode |
| **Heure de fin** | 22:00:00 | Heure de désactivation du mode |

## Fonctionnement

### Scénario type d'une journée

1. **6h00** → Activation automatique du mode smart/excédent
2. **Journée** → La batterie se charge uniquement avec le surplus solaire
3. **22h00** → Fin de la plage horaire (vous pouvez utiliser un autre blueprint pour gérer la soirée/nuit)

### Déclencheurs

L'automation se déclenche dans deux cas :
- **À l'heure de début configurée** (par défaut 6h00)
- **Au démarrage de Home Assistant** (si l'heure actuelle est dans la plage horaire)

Cela garantit que le mode excédent est toujours activé pendant la journée, même après un redémarrage.

## Exemple d'utilisation

### Configuration recommandée pour optimiser le solaire

```yaml
Mode de fonctionnement: Couplage intelligent (Smart)
Heure de début: 06:00:00
Heure de fin: 22:00:00
```

### Autres cas d'usage

**Décharge en heures pleines (17h-20h) :**
```yaml
Mode de fonctionnement: Décharge intelligente
Heure de début: 17:00:00
Heure de fin: 20:00:00
```

**Charge nocturne en heures creuses :**
```yaml
Mode de fonctionnement: Charge intelligente
Heure de début: 02:00:00
Heure de fin: 06:00:00
```

### Combinaison avec d'autres blueprints

Ce blueprint fonctionne parfaitement avec :
- **Zendure - Charge Progressive Tempo** : Pour la charge nocturne
- Un blueprint de décharge pour la soirée (peak shaving)

## Cas d'usage

- **Autoconsommation maximale** : Stockez votre surplus solaire au lieu de le réinjecter
- **Économie d'énergie** : Évitez de charger la batterie avec l'électricité du réseau en journée
- **Optimisation photovoltaïque** : Utilisez intelligemment votre production solaire

## Personnalisation

Vous pouvez adapter le blueprint selon vos besoins :
- Ajuster les horaires selon votre ensoleillement local
- Modifier les plages horaires selon les saisons
- Combiner avec des conditions météo pour plus de finesse

## Notes

- Le mode "smart" doit être disponible sur votre entité Zendure Manager
- Pensez à désactiver les autres modes de charge pendant cette plage horaire
- Compatible avec les automatisations de décharge pour optimiser la gestion batterie 24/7

## Support

Pour toute question ou suggestion d'amélioration, n'hésitez pas à ouvrir une issue sur GitHub.

## Licence

Ce blueprint est fourni tel quel, libre d'utilisation et de modification.
