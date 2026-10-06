# Widget Grist – Émargement

## Tables/colonnes attendues

### Apprenants

* Nom
* Prenom
* Email
* Actif (Bool)
* Code (Text)

### Formations

* Nom\_Formation
* Date\_Debut (Date)
* Date\_Fin (Date)
* Active

### Inscriptions

* Active (Bool)
* Apprenant (Reference -> Apprenants)
* Formation (Reference -> Formations)

### Emargements

* Signature\_Effectuee (Bool)
* Date\_Formation (Date)
* Signature (Attachments)
* Demi\_Journee (Choice: `Matin`, `Après-midi`)
* Apprenant (Reference -> Apprenants)
* Formation (Reference -> Formations)
* Horodatage\_Signature (Trigger formula DateTime)

Le widget met à jour une ligne existante dans `Emargements` et ne crée pas de ligne d'émargement.

## Horodatage recommandé

`Horodatage\_Signature` doit être une Trigger Formula déclenchée par `Signature\_Effectuee` :

```python
if $Signature\_Effectuee:
  return NOW()
return None
```

## Installation

1. Hébergez `index.html` sur une URL HTTPS publique que vous contrôlez (GitHub Pages, serveur interne HTTPS, etc.).
2. Dans Grist : **Ajouter un widget > Personnalisé**.
3. Renseignez l'URL publique de `index.html`.
4. Accordez au widget **Accès complet au document** : il doit lire plusieurs tables, téléverser la signature et mettre à jour `Emargements`.
5. Le widget utilise directement les noms de tables/colonnes ci-dessus. Si vous les renommez, modifiez la constante `TABLES` et les noms de champs dans le fichier.

## Logique

* identification par `Apprenants.Code` ;
* seulement les apprenants actifs ;
* recherche des inscriptions/formations actives ;
* affiche les émargements non signés jusqu'à la date du jour ;
* permet de régulariser un créneau antérieur non signé ;
* aucun créneau futur n'est proposé ;
* signature souris/doigt/stylet ;
* recadrage + réduction + seuillage noir/blanc en PNG ;
* téléversement dans les pièces jointes Grist ;
* mise à jour de la ligne `Emargements` (`Signature\_Effectuee=True`, `Signature=<pièce jointe>`).

## Points de production à prévoir

* Remplacer l'en-tête texte par les logos officiels en fichiers séparés (SVG/PNG), si vous disposez des originaux.
* Si plusieurs formations sont actives simultanément pour le même apprenant, ajouter un écran de sélection de formation. Cette V1 choisit d'abord une formation couvrant la date du jour, sinon la première formation active.
* Un code apprenant est un contrôle d'accès léger, pas une authentification forte. Utilisez des codes aléatoires non prédictibles et protégez les vues/tableaux Grist côté administration.
* Tester l'upload d'attachments sur votre hébergement Grist. Le widget utilise un access token temporaire Grist et l'endpoint REST `/attachments`.

