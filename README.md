[README.md](https://github.com/user-attachments/files/27631174/README.md)
# 🌿 Assistant Intelligent de Tonte v2.4.4
### Navimow + Home Assistant

Un assistant complet pour automatiser, planifier et superviser votre tondeuse Navimow depuis Home Assistant. Notifications intelligentes, adaptation saisonnière, gestion météo, historique et installation en quelques clics.

---

## ✨ Fonctionnalités

| Fonctionnalité | Description |
|---|---|
| 🗓️ Planification saisonnière | Fréquence et hauteur conseillée par mois, 24 sliders configurables depuis le dashboard |
| 🌦️ Météo intelligente | Report automatique si risque de pluie (Météo France), prévision du lendemain |
| ⏭️ Report différé | 1h / 2h / 4h / demain avec rappel à l'expiration |
| 💬 Multi-notifications | Telegram (boutons inline), Mobile (Companion App) ou aucune |
| 🔄 Fin de tonte intelligente | Timer 75 min (calé sur les cycles de recharge ~60 min observés terrain) |
| 🏠 Multi-zones | Jardin + zone isolée (terrasse, potager...) avec compteurs séparés |
| 📅 Historique | Calendrier local HA avec chaque session (activable/désactivable) |
| 🚨 Alertes | Erreur tondeuse, batterie faible, pelouse en retard |
| 📊 Résumé hebdo | Récapitulatif chaque dimanche soir |
| 🏖️ Mode vacances | Suspend toutes les notifications |
| ❄️ Hivernage | Automatique (intervalle = 0) ou forcé depuis le dashboard |
| ⚙️ Installation guidée | Carte d'installation avec formulaire visuel, sauvegarde automatique |

---

## 📋 Prérequis

- **Home Assistant** 2026.1.0+
- **HACS** avec [Mushroom Cards](https://github.com/piitaya/lovelace-mushroom) + [card-mod](https://github.com/thomasloven/lovelace-card-mod)
- **NavimowHA** : intégration officielle Navimow pour HA ([segwaynavimow/NavimowHA](https://github.com/segwaynavimow/NavimowHA))
- **Météo France** : intégration native HA
- **Telegram Bot** en mode **POLLING** (pas Broadcast) — *si mode Telegram*
- **Companion App** — *si mode mobile*

> ⚠️ **Important** : le bot Telegram doit impérativement être configuré en mode **Polling**. Le mode Broadcast ne reçoit pas les callbacks des boutons. C'est l'erreur la plus fréquente.

---

## 📦 Fichiers

| Fichier | Rôle |
|---|---|
| `tonte_intelligente.yaml` | Package principal (automations, scripts, sensors) |
| `tonte_setup.yaml` | Package de configuration — paramètres personnels persistants |
| `carte_tonte_dashboard.yaml` | Carte détaillée pour la vue tondeuse |
| `carte_tonte_mini.yaml` | Carte compacte pour le dashboard principal |
| `carte_tonte_installation.yaml` | Carte d'installation avec formulaire visuel |

---

## 🔧 Installation

### Étape 1 — NavimowHA

1. HACS → Intégrations → ⋮ → Dépôts personnalisés
2. Ajouter `https://github.com/segwaynavimow/NavimowHA` → Intégration
3. Installer, redémarrer HA
4. Paramètres → Appareils → Ajouter → "Navimow" → vos identifiants

> Si vous utilisez Pi-hole ou AdGuard, désactivez-le temporairement lors de l'ajout.

> ℹ️ La carte d'installation est 100% autonome dès le chargement de `tonte_setup.yaml` — aucune dépendance à `tonte_intelligente.yaml` au moment de la configuration.

> ℹ️ Les entités `tonte_mode_notification`, `tonte_zone_isolee_nom` sont déclarées dans `tonte_setup.yaml` pour garantir leur persistance entre les mises à jour.

Résultat : `lawn_mower.navimow_XXXX` + `sensor.navimow_XXXX_batterie`

---

### Étape 2 — Copier les fichiers

Copiez dans `/config/packages/` :
```
tonte_intelligente.yaml
tonte_setup.yaml
carte_tonte_dashboard.yaml
carte_tonte_mini.yaml
carte_tonte_installation.yaml
```

Activez les packages dans `configuration.yaml` si ce n'est pas déjà fait :
```yaml
homeassistant:
  packages:
    tonte_setup: !include packages/tonte_setup.yaml
    tonte:       !include packages/tonte_intelligente.yaml
```

Redémarrez HA.

---

### Étape 3 — Carte d'installation

Dashboard → Modifier → Ajouter carte → Manuel → coller le contenu de `carte_tonte_installation.yaml`.

Remplissez le formulaire :

| Champ | Exemple |
|---|---|
| Modèle tondeuse | `navimow_i105e` |
| Ville Météo France | `rennes` |
| Entité Telegram | `mon_bot_update_event` |
| Appareil mobile | `mobile_app_pixel_9a` |
| Mode notification | `telegram` / `mobile` / `aucune` |
| Zone isolée | activée/désactivée + nom |
| Calendrier | activé/désactivé |

Cliquez **"Appliquer la configuration"** → une sauvegarde est créée → HA redémarre automatiquement.

> Pour trouver votre entité Telegram : Outils de dev → États → chercher `event.`

---

### Étape 4 — Ajouter les cartes

Dashboard → Modifier → Ajouter carte → Manuel :
- Coller `carte_tonte_dashboard.yaml` dans votre vue tondeuse
- Coller `carte_tonte_mini.yaml` dans votre dashboard principal

---

### Méthode alternative (manuelle)

Si vous préférez ne pas utiliser la carte d'installation, faites ces Chercher/Remplacer dans `tonte_intelligente.yaml` **et** dans les deux fichiers de cartes :

1. `navimow_VOTRE_MODELE` → votre modèle
2. `breteuil` → votre ville Météo France
3. `ha_maison_update_event` → votre entité Telegram
4. `mobile_app_pixel_9a` → votre appareil mobile *(si mode mobile)*

---

## 🔄 Mise à jour

1. Remplacez les fichiers mis à jour dans `/config/packages/` (généralement `tonte_intelligente.yaml`, parfois les cartes)
2. Ouvrez la carte d'installation → vos paramètres sont déjà remplis → cliquez **"Appliquer"**
3. HA redémarre automatiquement avec la nouvelle version configurée

> ⚠️ Si vous remplacez aussi une carte (dashboard ou mini), repassez toujours par **"Appliquer"** même si vos paramètres n'ont pas changé — cela réapplique le Chercher/Remplacer du modèle tondeuse sur les nouvelles cartes.

---

## 📅 Profils saisonniers

Configurables depuis le dashboard (section "Configuration saisonnière") :

| Mois | Intervalle défaut | Hauteur défaut |
|---|---|---|
| Novembre → Février | 0 (hivernage) | — |
| Mars | 5 jours | 4.0 cm |
| Avril — Mai | 3 jours | 4.0 — 4.5 cm |
| Juin | 3 jours | 4.5 cm |
| Juillet — Août | 2 jours | 5.0 cm |
| Septembre | 3 jours | 4.5 cm |
| Octobre | 5 jours | 4.0 cm |

> Intervalle = 0 → hivernage automatique pour ce mois.

---

## ⏱️ Timers avancés

Configurables en YAML dans `tonte_intelligente.yaml` (section balisée) :

| Paramètre | Défaut | Description |
|---|---|---|
| `tonte_delai_fin_detection` | 90 min | Délai avant de valider la fin de tonte après retour base |
| `tonte_seuil_batterie_raccourci` | 100% | Seuil batterie pour raccourcir le timer |
| `tonte_delai_raccourci_batterie` | 15 min | Durée du raccourci batterie |
| `tonte_delai_demande_zone` | 120 sec | Délai avant la question de zone via Telegram |
| `tonte_retard_multiplicateur` | ×2 | Multiplicateur intervalle avant alerte pelouse en retard |

> ℹ️ La fin de tonte est détectée par le timer `tonte_delai_fin_detection` (défaut 75 min, calé sous les cycles de recharge ~60 min). La détection batterie a été abandonnée — l'intégration NavimowHA remonte une valeur fictive de 100% dans les 30s après chaque dock.

---

## 🧠 Architecture technique

- **29 automations** — planification, détection, notifications, callbacks, alertes
- **5 scripts** — lancer, pause, base, marquer jardin, marquer zone isolée
- **7 sensors template** — état FR, profil saisonnier, météo, décision du jour, jours écoulés, jour planifié
- **2 timers** — détection fin de tonte, report différé
- **2 compteurs** — tontes jardin saison, tontes zone isolée saison

---

## 💡 Notes

- NavimowHA supporte : start / pause / dock + batterie + état. La hauteur de coupe n'est pas contrôlable depuis HA (molette mécanique sur le i105e).
- Le nom de l'entité événement Telegram varie selon votre configuration → vérifiez dans Outils de dev → États.
- La carte d'installation nécessite que HA tourne sur HAOS ou une installation avec accès fichiers (non compatible HA Cloud).

---

## 📄 Licence

Projet communautaire, libre d'utilisation et de modification.
Partagez vos améliorations !
