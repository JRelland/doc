Groupes et permissions
===========================

Vous pouvez accéder à l'interface de gestion des *groupes et des permissions* depuis la webadmin
en allant dans la section "Utilisateurs" et en cliquant sur le bouton correspondant :

![](./images/button_to_go_to_permission_interface_fr.png)

Gestion des groupes
---------------
Le mécanisme de groupe peut être utilisé pour définir des groupes d'utilisateurs qui peuvent ensuite être utilisés pour restreindre les autorisations pour les applications et autres services (tels que l'email ou xmpp). Notez qu'il n'est *pas* obligatoire de créer un groupe pour ce faire : vous pouvez également restreindre l'accès à une application ou à un service de manière individuelle.

L'utilisation de groupes est cependant utile pour la sémantique, par exemple si vous hébergez plusieurs groupes d'amis, des associations ou des entreprises sur votre serveur, vous pouvez créer des groupes comme "association1" et "association2" et ajouter les membres de chaque association au groupe concerné.


### Groupes par défaut
Par défaut, deux groupes spéciaux sont créés :
- `all_users`, qui contient tous les utilisateurs enregistrés sur YunoHost,
- `visitors`, c'est-à-dire les personnes qui consultent le serveur sans être connectées. 

Vous ne pouvez pas changer le contenu de ces groupes, seulement les permissions qui leur sont accordées.
 
### Lister les groupes existants
Les groupes existants sont listés en haut de la page *groupes et autorisations*.

![](./images/groups_default-groups.png)

Pour obtenir la liste des groupes existants en CLI :


```bash
$ yunohost user group list
groups:
  all_users:
    members:
      - alice
      - bob
      - charlie
      - delphine
```


### Créer un nouveau groupe
Pour créer un nouveau groupe, il suffit de cliquer sur le bouton "Nouveau groupe" en haut de la page. Vous ne pouvez choisir qu'un nom formé de lettres (majuscules et minuscules) et d'espaces. Le groupe est créé vide et sans aucune permissions.

![](./images/groups_button-new-group.png)

Dans la CLI, pour créer un nouveau groupe appelé `yolo_crew`, il faut utiliser

```bash
$ yunohost user group create yolo_crew
```

### Mettre à jour un groupe
Ajoutons un premier utilisateur à ce groupe : dans le panneau du groupe, cliquez sur le bouton "ajouter un utilisateur" et faites défiler jusqu'à l'utilisateur souhaité, puis cliquez dessus.

![](./images/groups_button-add-user.png)

Pour supprimer un utilisateur, cliquez sur la croix à côté de son nom d'utilisateur, dans le panneau du groupe.

![](./images/groups_button-remove-user.png)

En CLI, utilisez la commande suivante pour ajouter `charlie` et `delphine` au groupe `yolo_crew` :

```bash
$ yunohost user group update yolo_crew --add charlie delphine
```

(De même, `--remove` peut être utilisé pour retirer des membres d'un groupe)

Dans la liste des groupes, nous devrions voir :

```bash
$ yunohost user group list
groups:
  all_users:
    members:
      - alice
      - bob
      - charlie
      - delphine
  yolo_crew:
    members:
      - charlie
      - delphine
```

### Supprimer un groupe

Pour supprimer un groupe, cliquez sur la croix rouge en haut à droite du panneau du groupes. Une confirmation vous sera demandée.

![](./images/groups_button-delete-group.png)

Pour supprimer le groupe `yolo_crew` dans CLI, vous pouvez exécuter

```bash
$ yunohost user group delete yolo_crew
```

Gestion des permissions
--------------------

Le mécanisme de permissions permet de restreindre l'accès aux services (par exemple mail, xmpp, ...) et aux applications, ou même à des parties spécifiques des applications (par exemple l'interface d'administration de wordpress).

### Liste des permissions

La page des groupes liste les permissions données à chaque groupe, y compris les groupes spéciaux `all_users` et `visitors`.

![](./images/groups_default-with-permissions.png)

Pour répertorier les permissions et les accès correspondants en CLI :
```bash
$ yunohost user permission list
permissions:
  mail.main:
    allowed: all_users
  wordpress.admin:
    allowed:
  wordpress.main:
    allowed: all_users
  xmpp.main:
    allowed: all_users
```
Ici, nous constatons que tous les utilisateurs enregistrés peuvent utiliser le courrier électronique, xmpp, et accéder au blog wordpress. Cependant, personne ne peut accéder à l'interface d'administration de wordpress.

Plus de détails peuvent être affichés en ajoutant l'option `--full` qui affichera la liste des utilisateurs correspondant aux groupes autorisés, ainsi que les urls associées à une permission (pertinent pour les applications web).

### Ajouter des permissions à un groupe ou un utilisateur

Pour ajouter une permission à un groupe, il suffit de cliquer sur le bouton "+" dans le panneau du groupe, de faire défiler jusqu'à la permission souhaitée, puis de cliquer dessus.

![](./images/groups_add-permission-group.png)

Pour permettre à un groupe d'accéder à l'interface d'administration de wordpress via la CLI :

```bash
$ yunohost user permission update wordpress.admin --add yolo_crew
```

Notez que vous pouvez également autoriser un seul utilisateur, en utilisant le panneau spécifique en bas de la page.

![](./images/groups_add-permission-user.png)

ou en CLI :

```bash
$ yunohost user permission update wordpress.admin --add alice
```

Et maintenant, nous pouvons voir que YoloCrew et Alice ont tous deux accès à l'interface d'administration de Wordpress :

```bash
$ yunohost user permission list
  [...]
  wordpress.admin:
    allowed:
      - yolo_crew
      - alice
  [...]
```

Notez que, par exemple, si nous voulons restreindre la permission pour le courrier électronique 
afin que seul Bob soit autorisé à envoyer des courriels, nous devons également supprimer `all_users` 
de la permission, en la supprimant du panneau de groupe `all_users`, ou en CLI :

```bash
$ yunohost user permission update mail --remove all_users --add bob
```

La webadmin émettra un avertissement si vous définissez une permission qui est remplacée par une permission plus large.
![](./images/groups_alerte-permission.png)

Notes pour les packageurs d'applications
------------------------

[Voir la version anglaise de cette page](groups_and_permissions).
