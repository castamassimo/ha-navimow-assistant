# 🌿 CHANGELOG — Assistant Intelligent de Tonte
## Navimow + Home Assistant

---

## v2.4.3 — Correctifs terrain
*Avril 2026*

### 🐛 Correctifs
- **Démarrage automatique ne se déclenchait pas** : bug dans la vérification de la plage horaire. Le slice `[11:16]` appliqué à l'état d'un `input_datetime` heure-seule (format `07:00:00`, 8 caractères) retournait une chaîne vide, rendant la condition toujours fausse. Corrigé en `[0:5]`.
- **Pas de demande de zone en mode auto** : en mode full autonome, la tonte démarre forcément sur le jardin. La demande Telegram "Quelle zone ?" est maintenant supprimée en mode auto — la session est automatiquement comptabilisée en jardin.

### ✨ Amélioration
- **Détection de fin de tonte intelligente (Option B)** : le timer statique de 90 min (source du délai de 2h+ constaté terrain) est remplacé par une détection basée sur la batterie. Fin de tonte confirmée si : session active + tondeuse dockée depuis 5 min + batterie ≥ 95% stable depuis 5 min (la tondeuse n'est pas repartie). Le timer de 90 min reste en place comme filet de sécurité uniquement. Basé sur l'observation terrain : la tondeuse repart à 95% entre deux sessions, donc 95%+ stable = vraiment terminé.

---

## v2.4.2 — Visibilité mode autonome dans les cartes
*Avril 2026*

### ✨ Nouveautés
- **Carte mini** : affiche `🤖 Auto · Prochaine dans X jours` quand le mode autonome est actif. Bordure et icône vertes pour identifier visuellement le mode. La ligne de profil saisonnier est préfixée `🤖 Mode autonome activé`.
- **Carte dashboard** : nouveau chip `🤖 Auto` visible uniquement quand le mode est activé. Le secondary s'adapte au contexte : "Démarrage auto prévu aujourd'hui", "Prochaine auto dans X jours", "Auto en attente" si pluie.

---

## v2.4.1 — Correctif input_select notification
*Avril 2026*

### 🐛 Correctifs
- **`input_select.tonte_mode_notification` déplacé dans `tonte_setup.yaml`** : comme pour `tonte_zone_isolee_nom` en v2.3, l'entité est retirée de `tonte_intelligente.yaml` pour éviter les conflits base de données à chaque mise à jour. L'ancienne entité `tonte_cfg_notification` est supprimée.
- **Mécanisme de configuration du mode notification revu** : au lieu d'un `sed` sur le fichier YAML (source du problème `unavailable`), le script appelle désormais `input_select.select_option` directement — aucun conflit BDD possible.
- **Tuto mis à jour** : section "Mise à jour" avec avertissement explicite — après remplacement manuel d'une carte, repasser par "Appliquer la configuration" pour que le Chercher/Remplacer du modèle tondeuse soit réappliqué.

---

## v2.4 — Mode full autonome
*Avril 2026*

### ✨ Nouveautés
- **Mode full autonome** : nouveau `input_boolean` activable depuis le dashboard. Quand activé, la tondeuse démarre automatiquement à l'heure de notification si toutes les conditions sont réunies (jour de tonte, météo favorable, plage horaire, jour non exclu, pas en vacances, pas déjà en session). La notification de proposition du matin est supprimée en mode auto.
- **Plage horaire configurable** : deux nouveaux `input_datetime` (heure de début / heure de fin) pour limiter la fenêtre de démarrage automatique. Défaut : 09h00 → 18h00.
- **Jour d'exclusion fixe** : nouveau `input_select` pour exclure un jour de la semaine du démarrage automatique (ex : jamais le dimanche). Défaut : dimanche.
- **Notification silencieuse de démarrage** : quand le mode auto déclenche la tonte, une notification informative est envoyée (Telegram ou mobile) avec un bouton "Arrêter" — aucune action requise.
- **Watchdog de démarrage** : si la tondeuse n'est pas passée en état `mowing` dans les 10 minutes après un ordre de démarrage automatique, une alerte est envoyée avec les boutons Réessayer / Base. Évite les démarrages silencieusement échoués.

---

## v2.3 — Carte d'installation 100% autonome
*Avril 2026*

### ✨ Nouveautés
- **`tonte_zone_isolee_nom` déplacé dans `tonte_setup.yaml`** : la carte d'installation est maintenant entièrement autonome. Elle n'a plus aucune dépendance envers `tonte_intelligente.yaml` — l'entité existe dès le chargement du setup, avant même que le fichier principal soit configuré. Fini l'erreur "entité inconnue" pour les nouveaux utilisateurs.
- Le nom de la zone isolée est persisté comme tous les autres paramètres lors de l'application de la configuration.

### 🐛 Correctifs
- **Notification Telegram "Quelle zone ?"** : l'erreur "too many values to unpack" est corrigée. Le `inline_keyboard` en template YAML est remplacé par deux blocs `choose` avec des listes statiques — format attendu par l'intégration Telegram HA. Les boutons fonctionnent à nouveau correctement.

---

## v2.2.1 — Correctifs installation
*Avril 2026*

### 🐛 Correctifs
- **Erreur `homeassistant.reload_config_entry`** : suppression de cette action qui nécessitait un `entry_id` non fourni. Remplacée par un simple `homeassistant.restart`.
- **Valeurs perdues après redémarrage** : les paramètres saisis dans la carte d'installation (modèle, ville, Telegram...) revenaient aux valeurs par défaut après le redémarrage de HA. Corrigé : le script met maintenant à jour les `initial:` de `tonte_setup.yaml` avant le restart, les valeurs sont donc relues correctement au démarrage suivant.

---

## v2.2 — Système d'installation automatique
*Avril 2026*

### ✨ Nouveautés
- **Carte d'installation** : nouveau fichier `carte_tonte_installation.yaml` à ajouter où vous voulez dans votre dashboard. Formulaire visuel pour saisir vos paramètres (modèle tondeuse, ville météo, Telegram, mobile, mode notification, zone isolée, calendrier).
- **Package setup** : nouveau fichier `tonte_setup.yaml` qui stocke vos paramètres personnels séparément du fichier principal. Lors d'une mise à jour, remplacez uniquement `tonte_intelligente.yaml` — vos paramètres sont préservés.
- **Application automatique** : un bouton "Appliquer la configuration" personnalise et redémarre HA sans toucher au moindre fichier manuellement.
- **Sauvegarde horodatée** : avant chaque application, une copie `tonte_intelligente.yaml.bak_AAAAMMJJ_HHMM` est créée automatiquement dans `/config/packages/`.
- La méthode manuelle (Chercher/Remplacer) reste disponible pour les utilisateurs avancés.

---

## v2.1.1 — Correctifs bugs
*Avril 2026*

### 🐛 Correctifs
- **Statut bloqué "en cours"** : nouvelle automation de sécurité qui remet automatiquement la session à zéro si la tondeuse est sur sa base depuis plus de 3h et que le timer est inactif.
- **Bouton reset manuel** : nouveau bouton "Reset session bloquée" dans les Réglages du dashboard, visible uniquement quand une session est en cours.
- **Notifications de zone** : les confirmations de zone affichent maintenant un message informatif avec l'état en cours, la batterie et la hauteur conseillée.

---

## v2.1 — Timers configurables & calendrier optionnel
*Avril 2026*

### ✨ Nouveautés
- **Tous les timers sont maintenant configurables** sans modifier le code :
  - Délai avant de considérer la tonte terminée (défaut : 90 min)
  - Seuil de batterie pour raccourcir ce timer (défaut : 100%)
  - Durée du raccourci batterie (défaut : 15 min)
  - Délai avant la demande de zone via Telegram (défaut : 2 min)
  - Multiplicateur pour l'alerte "pelouse en retard" (défaut : ×2 l'intervalle du mois)
- **Calendrier activable/désactivable** : option pour ne pas utiliser `calendar.tondeuse`.

### 🔧 Améliorations
- Corrections dans les cartes dashboard et mini : références au modèle tondeuse généralisées.
- Le nom "Mode terrasse" dans les réglages est maintenant dynamique.
- Tuto mis à jour : Chercher/Remplacer à faire aussi dans les fichiers de cartes.

---

## v2.0 — Version publique générique
*Mars 2026*

### ✨ Nouveautés majeures
- **Modèle générique** : placeholder `navimow_VOTRE_MODELE`, compatible tous modèles Navimow.
- **Zone isolée activable/désactivable** et renommable sans chercher dans tout le fichier.
- **Option "Ne pas comptabiliser"** lors de la demande de zone Telegram.
- **Dashboard enrichi** : statut "Reporté" affiché pendant un report différé.

### 🔧 Améliorations
- Installation simplifiée : 3 Chercher/Remplacer.
- Documentation complète avec tuto Facebook et post de partage.

---

## v1.x — Versions privées / développement
*2025 — 2026*

Versions de développement non diffusées publiquement. Elles ont permis de construire et tester l'ensemble des fonctionnalités présentes en v2.0 : planification saisonnière, météo, détection fin de tonte, Telegram, multi-zones, calendrier, alertes, résumé hebdo, mode vacances, hivernage.
