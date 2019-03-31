# Rapport MongoDB

Pour commencer, j'ai ajouté la création de la base Mongo de façon à ce qu'elle
s'execute en même temps que la création de la base Postgres (`mongo_init()`
appellé lors de l'appel de la route `/after_admin`).

Ensuite, j'ai essayé de récupérer la collection ainsi créée
(`mongo_get_chambres()` et `mongo_get_chambre(id)`), mais ce fut infructueux.
