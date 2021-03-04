---
title: Gestion de vos archives avec Rsync
slug: pca/rsync
excerpt: Découvrez comment accéder à vos archives Public Cloud avec Rsync
section: Public Cloud Archive
---

**Dernière mise à jour le 8 décembre 2020**

## Objectif

OVHcloud Public Cloud Archive est une solution de stockage gérée principalement par via l'API OpenStack. Cependant, nous avons développé une passerelle qui permet de se connecter à votre conteneur PCA avec Rsync.

**Découvrez les informations nécessaires pour activer la connexion à vos données stockées à l'aide de Rsync.**

## Prérequis

### Rsync

[Rsync](https://rsync.samba.org/) est un utilitaire Open Source qui permet un transfert de fichiers incrémentiel rapide.<br>
Les fichiers binaires précompilés sont disponibles dans la plupart des distributions de systèmes d'exploitation récentes. Par conséquent, vous devez d'abord vérifier si vous pouvez installer un package Rsync à l'aide de vos outils d'installation de packages standard pour votre système d'exploitation.

### ID OpenStack

Vous pouvez générer votre identifiant et votre mot de passe OpenStack à l'aide de ce [guide](../../../public-cloud/configure_user_access_to_horizon/).

### NomClient

Le TenantName correspond au nom de votre projet Horizon. Pour obtenir le TenantName, vous devez vous connecter à l'interface Web OpenStack: [https://horizon.cloud.ovh.net/](https://horizon.cloud.ovh.net/){.external}.

Une fois connecté, TenantName est visible en haut de la page.

![horizon](images/image1.png){.thumbnail}

## En pratique

### Détails de la connexion

- Nom d'hôte: gateways.storage.{region}.cloud.ovh.net
- Nom d'utilisateur: pca
- Mot de passe: {TenantName}.{Username_Openstack}.{Password_Openstack}
- Port: 22

### Téléchargement de données

Exemple de ligne de commande si vous avez créé un conteneur PCA dans la région GRA:

```bash
user@host:~$ rsync -a /path/to/my/dir pca@gateways.storage.gra.cloud.ovh.net:/container
pca@gateways.storage.gra.cloud.ovh.net's password:
user@host:~$
```

### Téléchargement des données

OVHcloud Public Cloud Archive délivre un stockage de données à faible coût, en échange d'une latence accrue dans le processus de récupération. Pour accéder à votre archive, une demande d'extraction doit être reçue avec les noms de conteneur et d'archive auxquels elle se rapporte.

Une fois votre archive extraite, vous pouvez la télécharger dans les 24 heures avec un débit illimité et une fréquence d'accès illimitée. Après cette période de récupération, l'archive sera de nouveau archivée.

```bash
user@host:~$ rsync -a pca@gateways.storage.gra.cloud.ovh.net:/container
pca@gateways.storage.gra.cloud.ovh.net's password:
user@host:~$
```

### Informations supplémentaires: Options Rsync

Puisque le serveur Rsync a été corrigé pour fonctionner avec l'API Swift, ces options seront appliquées côté serveur pour correspondre au comportement du serveur principal de stockage d'objets:

> --inplace: Au lieu de la méthode par défaut de création d'une nouvelle copie du fichier et de son déplacement à la fin, Rsync écrit les données mises à jour directement dans le fichier de destination.
>
> -W, --whole-file: Désactive l'algorithme de transfert delta de Rsync et envoyez le fichier entier en l'état.

En outre, seul un sous-ensemble d'options est autorisé côté client:

> -a, --archive: Activer le mode d'archivage.
>
> -r, --recursive: Copier les répertoires de manière récursive.
>
> -v, --verbose: Augmenter la quantité d'informations fournies lors du transfert.
>
> --del, --delete: Supprimer les fichiers superflus du répertoire de destination.
>
> -P, --progress: Imprimer les informations indiquant la progression du transfert.


## Allez plus loin

[Documentation de l'API d'archivage cloud](https://docs.ovh.com/gb/en/storage/pca/api/)

[Page de manuel Rsync](https://linux.die.net/man/1/rsync)

Rejoignez notre communauté d'utilisateurs sur <https://community.ovh.com/en/>.