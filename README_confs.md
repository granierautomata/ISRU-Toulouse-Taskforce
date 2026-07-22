# Carte interactive — Bilan de conférences

Carte du monde interactive présentant les groupes de travail, procédés et pistes de collaboration rencontrés en conférence. Un point cliquable = une équipe/personne rencontrée, avec ses notes dans un panneau latéral.

## Aperçu rapide

- **Fichier unique** : `bilan_conferences.html`. Pas d'installation, pas de build, pas de dépendance à gérer soi-même.
- **Pour l'ouvrir** : double-clic sur le fichier → il s'ouvre dans le navigateur par défaut. Une connexion internet est nécessaire (le fond de carte est chargé depuis un serveur externe, gratuit et sans clé API).
- **Pour l'éditer** : n'importe quel éditeur de texte fonctionne (Bloc-notes, VS Code, etc.). Toutes les données à modifier se trouvent dans une seule zone du fichier, décrite ci-dessous.
- **Bibliothèque utilisée** : [Leaflet.js](https://leafletjs.com/) (open-source, gratuite), fond de carte OpenStreetMap/CARTO.

## Ajouter une conférence ou un groupe de travail

Toutes les données sont dans le tableau `ENTRIES`, au début de la balise `<script>`. Chaque entrée est un objet de ce type :

```javascript
{
  conference: "Nom de la conférence — Ville (Pays)",
  org: "Nom de l'organisme / équipe",
  person: "Nom de la personne rencontrée (rôle)",
  lat: 48.8365, lng: 10.0932,
  category: "pbf",
  notes: [
    "Premier point à retenir",
    "Deuxième point à retenir"
  ],
  collab: "Piste de collaboration éventuelle (laisser une chaîne vide '' si aucune)"
}
```

**Marche à suivre :**
1. Repérer un objet existant dans `ENTRIES` qui vous ressemble, le dupliquer (copier-coller le bloc `{ ... },`).
2. Remplacer les champs un par un.
3. Ajouter une virgule après l'accolade fermante si ce n'est pas le dernier élément du tableau.
4. Sauvegarder le fichier, l'ouvrir dans le navigateur pour vérifier.

### Description des champs

| Champ | Obligatoire | Description |
|---|---|---|
| `conference` | oui | Nom de l'évènement où la rencontre a eu lieu. Peut rester vide `""` pour une entrée "hors conférence" (ex. collaboration existante). |
| `org` | oui | Nom affiché en gras dans la fiche — organisme, labo ou entreprise. |
| `person` | non | Nom et rôle de la personne. Laisser `""` si non pertinent. |
| `lat`, `lng` | oui | Coordonnées GPS du lieu (voir section suivante). |
| `category` | oui | Doit correspondre exactement à une des clés définies dans `CATEGORIES` (voir plus bas). Détermine la couleur du point. |
| `notes` | oui | Tableau de phrases, affichées en liste à puces. Autant d'éléments que nécessaire. |
| `collab` | non | Texte mis en valeur dans un encart. Laisser `""` si aucune piste identifiée — l'encart ne s'affiche pas dans ce cas. |
| `approx` | non | Ajouter `approx: true` si les coordonnées sont une estimation à vérifier. Fait apparaître un avertissement dans la fiche. |

## Trouver des coordonnées GPS

Trois méthodes gratuites, sans compte à créer :

- **Google Maps** : clic droit sur le lieu → les coordonnées apparaissent en haut du menu contextuel, cliquer dessus les copie.
- **Nominatim (OpenStreetMap)** : https://nominatim.openstreetmap.org/ui/search.html — taper le nom du lieu, résultat direct.
- **URL Google Maps** : après une recherche, l'URL contient `@48.8365,10.0932,15z` — les deux premiers nombres sont latitude et longitude.

Une précision au niveau de la ville suffit largement ; pas besoin de viser le bâtiment exact.

## Les catégories (couleur des points)

Définies dans l'objet `CATEGORIES`, juste avant `ENTRIES` :

```javascript
const CATEGORIES = {
  pbf:      { label: "Fusion sur lit de poudre (PBF / SLM)", color: "#e0a94d" },
  ded:      { label: "Dépôt d'énergie dirigé (DED / LDED)",  color: "#4d9de0" },
  indirect: { label: "Voie indirecte (extrusion, encre, composites)", color: "#7fd88f" },
  ebeam:    { label: "Faisceau d'électrons", color: "#FF3B14" },
  maison:   { label: "Vous êtes ici", color: "#c95ee0" },
  simulant: { label: "Développement/collection de simulants", color: "#787878" },
};
```

**Pour ajouter une nouvelle catégorie** : ajouter une ligne sur ce modèle avec une nouvelle clé (courte, sans espace ni accent), un `label` (texte affiché dans la légende) et une `color` (code couleur hexadécimal). Elle apparaît alors automatiquement dans la légende en bas à gauche de la carte, sans autre modification à faire.

## Plusieurs entrées au même endroit

Si deux entrées ont des coordonnées très proches (moins d'environ 100 mètres), la carte les regroupe automatiquement sous un seul point affichant un badge numéroté (ex. "2"). Cliquer dessus ouvre la première fiche, avec des flèches ← → pour naviguer entre les entrées de ce lieu. Aucune action particulière n'est nécessaire de votre part pour activer ce comportement — il suffit d'ajouter les entrées normalement, même avec des coordonnées identiques ou quasi identiques.

## Mettre le fichier en ligne (optionnel)

Le fichier fonctionne très bien en local (envoi par email, etc.). Pour obtenir un lien web permanent et gratuit, la solution recommandée est **GitHub Pages** :
1. Créer un dépôt public sur github.com et y déposer le fichier renommé en `index.html`.
2. Dans Settings → Pages, choisir "Deploy from a branch" / branche `main`.
3. Le site est disponible à l'adresse `https://<utilisateur>.github.io/<nom-du-depot>/`, sans limite de durée.

## Changer le fond de carte

Une galerie de tous les fonds de carte compatibles avec Leaflet, testables en direct : https://leaflet-extras.github.io/leaflet-providers/preview/
Le code à copier-coller apparaît sous la carte une fois un style sélectionné ; il remplace le bloc `L.tileLayer(...)` présent juste après la ligne `const map = L.map(...)`.
