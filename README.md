# Spyglass Météo-France — version française

Adaptation de [lemker/spyglass-weather-card](https://github.com/lemker/spyglass-weather-card), commit `b8ee1b4992d63037772ca1469d556c5c90f0bdc7`, version locale `0.1.0-fr.1` du 24 octobre 2026. Licence Apache-2.0 conservée dans LICENSE. Les illustrations météo reprennent le travail du frontend Home Assistant, comme indiqué dans le fichier source.

La carte conserve les animations, les graphiques et l’éditeur visuel. Elle utilise l’intégration Météo-France déjà configurée, sans nouvelle clé ni intégration supplémentaire. Aucun fichier à compiler : `spyglass-weather-card.js` est le fichier installable.

## Installation manuelle — immédiatement utilisable

1. Copier `spyglass-weather-card.js` dans `/config/www/spyglass-weather-card.js`. Créer le dossier `www` au besoin.
2. Dans Home Assistant : **Paramètres → Tableaux de bord → ⋮ → Ressources** (activer le mode avancé du profil si nécessaire).
3. Ajouter la ressource `/local/spyglass-weather-card.js?v=fr1`, type **Module JavaScript**. Si une ressource Spyglass existe déjà, remplacer son URL au lieu d’ajouter une seconde version.
4. Recharger complètement le navigateur et, au besoin, vider le cache de l’application mobile.
5. Ajouter une carte manuelle avec le contenu de `exemple-minimal.yaml`. Remplacer `weather.ma_ville` par votre identifiant réel.

Le type reste `custom:spyglass-weather-card`. Ne pas charger simultanément l’original et cette adaptation : ils enregistrent le même composant.

## Installation HACS — après hébergement dans votre fork

L’archive locale ne constitue pas un dépôt HACS. Le dépôt officiel de lemker n’inclut pas cette adaptation et son installation ne donnera pas la version française.

1. Créer votre fork du dépôt original sur GitHub, puis y déposer les fichiers de ce dossier à la racine, en remplaçant le JavaScript et le README. Conserver LICENSE et `hacs.json`.
2. Dans HACS → menu ⋮ → **Dépôts personnalisés**, ajouter l’URL de **votre fork**, catégorie **Tableau de bord** (ou **Lovelace** selon la version).
3. Télécharger la carte. Si HACS ne crée pas la ressource, ajouter `/hacsfiles/NOM-DE-VOTRE-DEPOT/spyglass-weather-card.js?v=fr1` comme module JavaScript.
4. Retirer la ressource manuelle ou celle de la version originale, puis recharger le navigateur.

Si vous créez une publication GitHub, joindre aussi `spyglass-weather-card.js` à cette publication. `hacs.json` conserve le nom de fichier attendu ; aucun outil de construction n’est requis. Aucun fork ni aucune publication GitHub n’a été créé par cette livraison.

## Configuration

```yaml
type: custom:spyglass-weather-card
entity: weather.ma_ville
name: Ma ville
forecast_type: daily
```

Consulter **Outils de développement → États** ou les entités de l’intégration pour les identifiants. L’éditeur visuel est traduit. Les clés YAML restent en anglais pour conserver la compatibilité des configurations.

`exemple-complet.yaml` illustre tous les branchements Météo-France utiles. Ses noms sont des exemples à remplacer, pas des noms garantis. Supprimer les capteurs absents ; certains capteurs de température, pression et vent sont désactivés par défaut et peuvent être activés depuis l’intégration.

La détection automatique recherche seulement les capteurs existants ayant le préfixe de l’entité météo : `weather.lyon` → `sensor.lyon_…`. Elle ne consulte pas le registre des appareils. Les suffixes recherchés sont `temperature`, `pressure`, `cloud_cover` (ou `cloud` pour compatibilité), `uv`, `humidity`, `wind_speed`, `wind_gust`, `rain_chance`, `next_rain` et `weather_alert`. Si votre entité météo a été renommée, renseigner chaque capteur explicitement ou ajouter `sensor_prefix: sensor.lyon_`. La vigilance est départementale : indiquer explicitement son identifiant, par exemple celui trouvé dans vos États, qui peut ressembler à `sensor.69_weather_alert`.

Une option de capteur à `false` désactive sa détection. Le vent utilise les attributs de l’entité météo lorsque le capteur n’est pas trouvé ; `details.wind: false` masque ce branchement. Les prévisions passent par `weather/subscribe_forecast`, sans ancien attribut `forecast` et sans automatisation YAML. Les abonnements sont libérés au retrait ou à la reconfiguration de la carte.

### Carte et affichage

| Clé | Défaut / usage |
| --- | --- |
| `entity` | Entité `weather.*` obligatoire |
| `name` | Nom convivial de l’entité ; peut être personnalisé |
| `forecast_type` | `daily` ou `hourly` ; Météo-France ne fournit pas `twice_daily` |
| `max_items` | `all`, `fit` ou un nombre |
| `min_item_width` | 64 pixels |
| `return_home_after` | 45 secondes ; 0 conserve la vue ouverte |
| `apparent_temperature` | `true` affiche le ressenti disponible ; accepte aussi un identifiant de capteur |
| `hide_headline`, `hide_temperature`, `hide_forecast` | `false` ; masquer la zone correspondante |
| `debug` | `false` ; démonstration des effets, avec vigilance fictive signalée comme exemple |
| `sensor_prefix` | Préfixe facultatif de recherche des capteurs, terminé par `_` |

### Capteurs et graphiques

| Clé | Usage |
| --- | --- |
| `temperature_entity` | Historique de température |
| `pressure_entity` | Tendance sur trois heures |
| `cloud_entity` | Nébulosité du ciel animé |
| `next_rain_entity` | Sous-titre qualitatif de pluie dans l’heure ; ignore une référence vieille de plus de 70 minutes |
| `alerts_entity` | Bandeau de vigilance jaune, orange ou rouge ; le vert est masqué ; toucher ouvre les détails de l’entité |
| `sun_entity` | `sun.sun` par défaut |
| `condition_entity` | Texte de titre facultatif ; sinon conditions normalisées traduites |
| `subtitle_entity` | Texte prioritaire sur le résumé `next_rain` |
| `icon_entity` | Historique des conditions **normalisées Home Assistant** ; ne pas utiliser directement `original_condition` |
| `summary_entity`, `hourly_summary_entity` | Résumés textuels facultatifs provenant de capteurs externes |
| `details.uv` | Indice UV du capteur ; celui de Météo-France peut correspondre à une valeur journalière |
| `details.wind`, `details.gust`, `details.bearing` | Vent, rafales et direction ; `wind: true` utilise l’entité météo |
| `details.humidity`, `details.dew_point` | Humidité et point de rosée ; ce dernier est calculé si température et humidité sont disponibles |
| `details.precip_probability` | Probabilité courante ; ne crée pas de probabilités horaires absentes des prévisions |
| `details.rain_today`, `details.snow_today` | Cumuls **mesurés depuis minuit**, uniquement avec des capteurs externes appropriés |
| `details.aqi`, `details.aqi_scale` | Qualité de l’air externe ; échelle `aqhi`, `epa` ou `caqi`, à adapter au capteur |
| `details.smoke`, `details.fire`, `details.solar` | Fumée, risque d’incendie et rayonnement solaire externes |
| `precipitation.type` | Capteur externe de type normalisé : `rain`, `snow`, `sleet` |
| `precipitation.rain`, `.snow`, `.ice` | Débits externes, par exemple en mm/h ; jamais le cumul prévu sur 24 h |

`show_chips` accepte `uv`, `aqi`, `dew_point`, `wind`, `sun`, `solar` : `true` pour afficher, `false` pour masquer. Les cinq premiers sont actifs par défaut et ne s’affichent que si leurs données sont utilisables ; `solar` est masqué par défaut.

`animations` accepte `clouds`, `fog`, `stars`, `sun`, `rain`, `snow`, `lightning`, tous actifs par défaut. `false` immobilise les nuages, brouillard, étoiles ou halo, et masque les particules de pluie, neige ou éclairs. La préférence de réduction des animations du navigateur est respectée.

## Limites des données et du rendu

- Prévisions quotidiennes et horaires : horizon et champs variables selon la réponse. La carte affiche les entrées reçues, sans garantir une durée fixe. Les maximums/minimums quotidiens Météo-France sont conservés ; un graphique horaire peut montrer des extrêmes différents si sa couverture est partielle.
- `precipitation` de Météo-France est un cumul **prévu** sur 24 h. Il n’est branché ni sur « pluie mesurée aujourd’hui », ni sur l’intensité instantanée. La pluie horaire des prévisions est utilisée lorsqu’elle existe.
- `next_rain` donne des catégories et des intervalles de 5 à 10 minutes. Il sert au texte, sans conversion arbitraire en mm/h. Une absence de pluie peut donner un état `unknown` avec des attributs encore utilisables.
- La vigilance dépend du territoire et de l’API ; la documentation indique France métropolitaine et Andorre. Un capteur par département : aucune détection automatique au hasard parmi plusieurs départements.
- Probabilités de pluie/neige/gel : parfois absentes. L’UV du capteur est journalier selon la version de l’intégration ; son historique ne constitue pas une mesure instantanée du rayonnement UV.
- Météo-France ne fournit pas tous les champs de Pirate Weather : qualité de l’air, fumée, feu, rayonnement solaire, débits de neige/verglas et cumuls observés nécessitent d’autres capteurs. Les pastilles manquantes sont omises. Pas de résumé textuel étendu généré par Météo-France dans cette adaptation.
- Les effets pluie/neige restent indicatifs sans capteur d’intensité. Les éclairages et phases de lune sont calculés ; les données de soleil viennent de `sun.sun` et des coordonnées Home Assistant, qui doivent correspondre au lieu affiché.
- Les graphiques historiques nécessitent Recorder et des capteurs enregistrés. Le vent issu uniquement des attributs météo ne crée pas d’historique de capteur. Les anciennes unités historiques sont supposées identiques à celles du capteur actuel.
- Les dates sont françaises et les heures au format 24 h. Le découpage des jours hérité utilise le fuseau du navigateur : pour une lecture correcte, utiliser le même fuseau que Home Assistant, notamment près de minuit et aux changements d’heure.
- Les textes fournis librement par des capteurs externes restent dans leur langue d’origine. La carte traduit ses propres textes et les états météo normalisés.

## Vérifications et dépannage

Tests de logique exécutables avec `node test.cjs` : détection et désactivation des capteurs, noms personnalisés, vigilance, résumé de pluie et configuration. Vérification de syntaxe : `node --check spyglass-weather-card.js`.

Un rendu dans Edge sans fenêtre a été vérifié avec un Home Assistant simulé : conditions, animations, pluie dans l’heure, vigilance et ouverture du graphique du vent. Ce test ne remplace pas une validation sur votre installation Home Assistant réelle, à laquelle cette conversation n’a pas accès.

« Custom element doesn’t exist » : vérifier la ressource et le cache. Texte encore anglais : vérifier qu’une seule version Spyglass est chargée. Prévisions absentes : essayer l’action `weather.get_forecasts` dans les outils de développement avec votre entité et `type: daily` ou `hourly` ; vérifier les erreurs de l’intégration et la disponibilité de l’API. Capteur absent : activer l’entité correspondante ou supprimer sa ligne de configuration. Aucune réinstallation de Météo-France n’est nécessaire.

Sources examinées le 24 octobre 2026 : [dépôt Spyglass](https://github.com/lemker/spyglass-weather-card), [documentation Météo-France](https://www.home-assistant.io/integrations/meteo_france/), [plateforme météo Home Assistant](https://github.com/home-assistant/core/blob/dev/homeassistant/components/meteo_france/weather.py), [capteurs Home Assistant](https://github.com/home-assistant/core/blob/dev/homeassistant/components/meteo_france/sensor.py). Le code `dev` peut évoluer par rapport à votre version installée.
