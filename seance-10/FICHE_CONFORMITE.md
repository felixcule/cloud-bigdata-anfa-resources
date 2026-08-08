# Fiche de conformité — Application mobile passagers Anfa

> Gabarit fourni. Complétez chaque section **en 2-4 lignes**, en vous appuyant sur le CM.
> Il n'y a pas de "bonne réponse" unique sur certains points — l'important est le raisonnement.

## 1. Finalité du traitement
Les données sont collectées pour trois usages strictement délimités et exclusifs. La position GPS sert uniquement à proposer au passager le trajet ou l'arrêt le plus proche de sa localisation actuelle. L'historique des paiements mobile money ne sert qu'au calcul et au renouvellement de l'abonnement. Enfin, le numéro de téléphone agit uniquement comme identifiant de compte et moyen de contact. Ces finalités sont exclusives : aucune réutilisation à des fins de marketing, de profilage ou de géolocalisation permanente n'est permise sans un consentement spécifique, conformément au principe de limitation des finalités du RGPD et de la loi togolaise.

## 2. Données collectées et leur sensibilité
Le scénario implique trois données : la position GPS, l'historique des paiements mobile money et le numéro de téléphone. Parmi elles, l'historique des paiements mobile money est la plus sensible car il constitue une donnée financière révélant le patrimoine, les revenus et les habitudes de consommation du passager. Le CM le classe explicitement comme « données financières, très sensibles ». Le numéro de téléphone et la localisation précise sont également sensibles, mais dans une moindre mesure comparée à l'empreinte financière complète.

## 3. Base légale applicable
La Loi n°2019-014 relative à la protection des données à caractère personnel s'applique à l'ensemble des trois données, car elles constituent des données personnelles au sens de ce texte, directement analogue au RGPD. Parallèlement, la Loi n°2017-007 modifiée par la 2023-012 relative aux transactions électroniques s'applique spécifiquement à l'historique mobile money car elle encadre les paiements électroniques. Les deux lois s'appliquent simultanément à l'historique de paiements : la première protège la vie privée et les droits des personnes, tandis que la seconde impose des exigences de sécurité et de traçabilité sur les transactions financières.

## 4. Durée de conservation
Ces données ne doivent être conservées que pendant la durée strictement nécessaire à leur finalité initiale, conformément au principe de minimisation. La position GPS, n'ayant d'utilité que pour le trajet immédiat, ne devrait être gardée que le temps de la session, avec une extension minimale pour la résolution d'incidents. L'historique mobile money peut être conservé pendant la durée de l'abonnement puis le temps des obligations comptables, avant anonymisation ou suppression. Le numéro de téléphone doit être effacé lors de la clôture définitive du compte, car garder au-delà constituerait une détention excessive au regard de la minimisation.

## 5. Hébergement et souveraineté
Conformément à la loi togolaise, ces données doivent être hébergées sur le territoire national togolais ou dans un État offrant un niveau de protection équivalent, afin de rester sous juridiction togolaise. Héberger ces données chez un fournisseur de cloud américain les expose au Patriot Act et au Cloud Act, qui permettent aux autorités américaines d'y accéder sans notification préalable ni autorisation judiciaire togolaise. Cette extraterritorialité viole la souveraineté des données et compromet le secret professionnel, notamment sur les données financières des passagers.

## 6. Droit des personnes concernées
Oui, le passager dispose du droit de demander la suppression de ses données, conformément au droit à l'oubli reconnu par la loi 2019-014. Cependant, le système technique actuel d'Anfa, tel que construit depuis la séance 1, ne permet pas facilement cet exercice car les données sont dispersées et répliquées à travers plusieurs briques : MinIO pour le stockage objet, PostgreSQL pour les métadonnées, Kafka pour le streaming, et MLflow pour les artefacts. De plus, l'absence de mécanisme centralisé de suppression et l'immutabilité des fichiers dans le datalake rendent l'effacement granulaire d'un enregistrement particulièrement complexe et coûteux.
