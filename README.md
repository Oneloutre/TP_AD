# Livrable Microsoft Active Directory


## Sommaire:

1. [Preparation de l'environnement](#preparation-de-lenvironnement)  
1.1 [Installation de Windows Server](#installation-de-windows-server)  
1.2 [Ajout des rôles nécessaires](#ajout-des-rôles-nécessaires)  
1.3 [Promotion du serveur en contrôleur de domaine](#promotion-du-serveur-en-contrôleur-de-domaine)    
1.4 [Installation d'un membre du domaine](#installation-dun-membre-du-domaine)  

2. [Configuration de l'Active Directory](#configuration-de-lactive-directory)   
2.1 [Création d'unités d'organisation](#création-dunités-dorganisation)  
2.2 [Ajout d'utilisateurs](#ajout-dutilisateurs)  
2.3 [Création de groupes AD](#création-de-groupes-ad)  

3. [Configuration du service DNS)](#configuration-du-service-dns)  
3.1 [Configurer le redirecteur DNS](#configurer-le-redirecteur-dns)  
3.2 [Ajout d'enregistrement DNS](#ajout-denregistrement-dns)  
3.3 [Test de résolution DNS](#test-de-résolution-dns)  

4. [Configuration du service DHCP](#configuration-du-service-dhcp)  
4.1 [Création d'une plage d'adresses](#création-dune-plage-dadresses)  
4.2 [Ajout d'options DHCP](#ajout-doptions-dhcp)  
4.3 [Test de configuration DHCP](#test-de-configuration-dhcp)  
 
5. [Gestion des permissions d'accès](#gestion-des-permissions-dacces)  
5.1 [Création des dossiers communs](#création-des-dossiers-communs)  
5.2 [Définition des permissions NTFS](#définition-des-permissions-ntfs)  
5.3 [Test d'accès](#test-daccès)  

6. [Configuration des stratégies de groupe](#configuration-des-stratégies-de-groupe)  
6.1 [Création des GPO pour les départements](#création-des-gpo-pour-les-départements)  
6.2 [Liaison des GPO aux unités d'organisation](#liaison-des-gpo-aux-unités-dorganisation)  
6.3 [Test des GPO](#test-des-gpo)  

7. [Vérifications et tests](#vérifications-et-tests)  
7.1 [Tester les connexions utilisateurs](#tester-les-connexions-utilisateurs)  
7.2 [Documenter les résultats](#documenter-les-résultats)  


## 1. Preparation de l'environnement

### 1.1 Installation de Windows Server

Pour commencer, nous allons installer un serveur Windows Server 2019.  
On commence par télécharger l'image ISO de Windows Server 2019 sur le site de Microsoft.  
Une fois installée, on s'assure que le réseau est bien configuré et que le serveur a accès à internet.  
Pour cela, on crée un Nat Network, défini sur `192.168.1.0/24` comme précisé.  

![Nat Network](assets/1.png)

On vérifie la configuration du réseau, est surtout que **DHCP est désactivé**.

![Configuration réseau](assets/2.png)

Une fois fait, on peut commencer le paramétrage IP de Windows Server lui-même.
Je mets en place une ip statique:

![Configuration IP](assets/3.png)

On n'oublie pas également de faire les mises à jour.

![Mises à jour](assets/4.png)


### 1.2 Ajout des rôles nécessaires

Ajoutons les rôles nécessaires pour notre serveur.  
Pour cela, on se rend dans le gestionnaire de serveur, et on ajoute les rôles suivants:  

- Active Directory Domain Services
- DNS Server
- DHCP Server

![Ajout des rôles](assets/5.png)

On laisse les options par défaut pour le reste de la configuration.  
Une fois l'installation terminée, on redémarre le serveur et on vérifie que tout est bien installé.

![Installation terminée](assets/8.png)

### 1.3 Promotion du serveur en contrôleur de domaine

Pour cela, il suffit de cliquer sur `Promote this server to a domain controller` dans le gestionnaire de serveur, lorsqu'on voit la notification

![Promotion en contrôleur de domaine](assets/6.png)

On met donc en place le déploiement en indiquand le domaine de la forêt. (`techcorp.local`)

![Configuration du domaine](assets/7.png)

### 1.4 Installation d'un membre du domaine

Maintenant, ajoutons un membre au domaine.  
Pour cela, nous n'avons besoin que d'une machine virtuelle avec Windows 10.

On commence par configurer le réseau de la machine virtuelle, en lui attribuant une IP statique.  (`192.168.1.6` pour notre exemple, **De toute façon cette ip changera plus tard avec le DHCP**)  
On n'oublie pas de mettre l'ip du serveur DNS en tant que DNS préféré !

Ensuite, on rejoint la machine au domaine en allant dans les paramètres système, et en cliquant sur `Modifier les paramètres`. (Système > Informations système > Domaine ou groupe modifier > membre d’un : on coche domaine et on entre le domaine)
Une fois fait, un message apparaît indiquant que ça a fonctionné.

![Rejoindre le domaine](assets/9.png)

On redémmare la machine, et on peut voir que la machine nous propose un autre utilisateur pour se connecter, et il est écrit "Connectez vous à : TECHCORP".

![Connecté au domaine](assets/10.png)

Côté serveur, on peut voir que la machine est bien ajoutée dans les ordinateurs.

![Machine ajoutée](assets/11.png)

## 2. Configuration de l'Active Directory

### 2.1 Création d'unités d'organisation

Pour commencer, on va créer des unités d'organisation.  
Pour ce faire, il suffit de se rendre dans `Outils d'administration` > `Utilisateurs et ordinateurs Active Directory` > `techcorp.local`  
Là, on peut créer des unités d'organisation en faisant un clic droit sur `techcorp.local` > `Nouveau` > `Unité d'organisation`

![Création d'unités d'organisation](assets/12.png)

On indique le nom de l'OU, et on clique sur `OK`.

![Nom de l'OU](assets/12.5.png)

### 2.2 Ajout d'utilisateurs

On peut maintenant ajouter des utilisateurs.  
Pour cela, on se rend dans `techcorp.local` > `Utilisateurs` > `Nouveau` > `Utilisateur`

![Création d'un utilisateur](assets/13.png)

Là, on indique les informations de l'utilisateur, et on clique sur `Suivant`.  
On peut ensuite définir un mot de passe pour l'utilisateur, et cliquer sur `Suivant` puis `Terminer`.

![Informations de l'utilisateur](assets/13.5.png)

On peut donc créer plusieurs utilisateurs de cette manière.  
Passons rapidement en revue les utilisateurs créés.  

On rappelle le tableau donné : 

| Nom Complet  | Nom d'utilisateur |         Poste          |   Groupe AD   |          Email          | 
|:------------:|:-----------------:|:----------------------:|:-------------:|:-----------------------:|
| Marie Dupont |      mdupont      |     Responsable RH     |      RH       | mdupont@techcorp.local  |
| Paul Lambert |     plambert      | Chargé de recrutement  |      RH       | plambert@techcorp.local |
| Sophie Morel |      smorel       |    Gestionnaire RH     |      RH       | smorel@techcorp.local  |
| Claire Durand|     cdurand       |     Assistante RH      |      RH       | cdurand@techcorp.local |
| Jean Martin  |     jmartin       |     Responsable IT     |      IT       | jmarin@techcorp.local   |
| Alice Petit |     apetit        |  Technicienne réseau   |      IT       | apetit@techcorp.local |
| Thomas Leroy |     tleroy        |      Développeur       |      IT       | tleroy@techcorp.local |
| Karim Benaissa |  kbenaissa    | Administrateur système |      IT       | kbenaissa@techcorp.local |
| Support Technique | supporttech |   Support technique    |      IT       | supporttech@techcorp.local |
| Admin Techcorp | admintechcorp | Administrateur Domaine | Domain Admins | admin@techcorp.local |

On a donc 3 groupes :

| Nom du groupe |             Description             | Membres |
|:------------:|:-----------------------------------:|:----------------------:|
| RH | Accès aux dossiers et ressources RH | Marie Dupont, Paul Lambert, Sophie Morel, Claire Durand |
| IT | Accès aux dossiers et ressources IT | Jean Martin, Alice Petit, Thomas Leroy, Karim Benaissa, Support Technique |
| Domain Admins | Administrateurs du domaine | Admin Techcorp |

On peut donc créer les utilisateurs en conséquence.

Voilà les résultats après création des utilisateurs:

![Groupe admins](assets/14.png)

Ensuite, le groupe IT:

![Groupe IT](assets/15.png)

Et enfin, le groupe RH:

![Groupe RH](assets/16.png)

### 2.3 Création de groupes AD

Pour créer des groupes, on se rend dans `techcorp.local` > `Nouveau` > `Groupe`  

![Création de groupes](assets/16.5.png)
Là on indique le nom du groupe, et on clique sur `OK`.

Ensuite, on ajoute les membres du groupe en faisant un clic droit sur le groupe > `Propriétés` > `Membres` > `Ajouter`

Voici les résultats :

> Groupe Administrateurs du domaine

![Groupe Admins](assets/17.png)

> Groupe IT

![Groupe IT](assets/18.png)

> Groupe RH

![Groupe RH](assets/19.png)


## 3. Configuration du service DNS

Configurons à présent le service DNS.  
Dans le gestionnaire de serveur, on se rend dans `Outils` > `DNS`

![Redirecteur DNS](assets/20.png)

### 3.1 Configurer le redirecteur DNS

Une fois dans l'outil DNS, on fait un clic droit sur le serveur, et on clique sur `Rediecteurs conditionnels` > `Nouveau redirecteur conditionnel`  
On ajoute les ip voulues (`1.1.1.1`, cloudflare et `8.8.8.8`, google) et on clique sur `OK`.  

![Ajout de redirecteurs](assets/21.png)

### 3.2 Ajout d'enregistrement DNS

On peut ajouter des enregistrements DNS en faisant un clic droit sur `techcorp.local` > `Nouvel Hôte (A ou AAAA)` > `Nom du domaine parent` (ici www) et on rensigne l'ip de la machine.

Après, On peut ajouter une zone de recherche inversée en faisant un clic droit sur `Zones de recherche inversée` > `Nouvelle zone` > `Zone de recherche inversée IPv4` > `Nouvelle zone` > `Zone principale` > `ipv4` > `ip du router (192.168.1.1)`

![Ajout d'enregistrements DNS](assets/22.png)


### 3.3 Test de résolution DNS

Pour tester la résolution DNS, on peut faire un `nslookup`. On vérifie que le serveur DNS est bien configuré en faisant un `nslookup` sur le nom du serveur.  

![Test de résolution DNS](assets/23.png)

On voit que la résolution fonctionne bien.  
On vérifie également qu'on a toujours accès à internet.

![Test de résolution DNS](assets/24.png)

## 4. Configuration du service DHCP

### 4.1 Création d'une plage d'adresses

Pour commencer, on se rend dans le gestionnaire de serveur, et on clique sur `DHCP`  
On ajoute donc une nouvelle plage d'adresses en faisant un clic droit sur `IPv4` > `Nouvelle étendue`  
On la configure comme indiqué dans le sujet : `192.168.1.100` à `192.168.1.254`

![Création d'une plage d'adresses](assets/25.png)

### 4.2 Ajout d'options DHCP

On ajoute évidemment les options DHCP demandées (DNS, passerelle, etc)  

### 4.3 Test de configuration DHCP

Il suffit de désactiver l'adresse statique de notre machine client, et d'effecture un `ipconfig /renew` pour vérifier que le DHCP fonctionne bien.

![Test de configuration DHCP](assets/26.png)

## 5. Gestion des permissions d'accès

### 5.1 Création des dossiers communs

Dans l'explorateur de fichier, on crée sous `C:\` un dossier `partages.`  
Dans ce dossier, on crée un dossier `RH` et un dossier `IT`.

![Création des dossiers](assets/26.5.png)

### 5.2 Définition des permissions NTFS

Immédiatement, on change les permissions du dossier `IT` pour que seuls les membres du groupe IT aient accès.

![Permissions IT](assets/26.6.png)

Et on fait de même pour le dossier `RH` avec le groupe RH.

![Permissions RH](assets/26.7.png)

### 5.3 Test d'accès

Si on fait quelques tests d'accès, on voit que ça fonctionne plutôt bien.  
Premièrement, on se connecte Thomas Leroy, un membre du groupe IT, et on voit qu'il a accès au dossier `IT` mais pas au dossier `RH`.  

![Test d'accès](assets/27.png)

Pour Thomas Leroy, voilà une capture d'écran un peu plus précise : 

![Test d'accès](assets/28.png)

Ensuite, on se connecte avec Sophie Morel, un membre du groupe RH, et on voit qu'elle a accès au dossier `RH` mais pas au dossier `IT`.

![Test d'accès](assets/29.png)


## 6. Configuration des stratégies de groupe

### 6.1 Création des GPO pour les départements

#### 6.1.1 GPO pour le département RH

On se rend dans `Créer un nouvelle GPO > Ajouter la fonctionnalité > Appliquer au membres du groupe RH`  
On peut ensuite configurer les paramètres de la GPO comme demandé.  

![GPO RH](assets/30.png)

Comme demandé, on interdit donc l'accès au panneau de configuration pour les RH:

![GPO RH](assets/30.5.png)

Il suffit de cliquer sur activé.

Si un RH essaie d'accéder au panneau de configuration, il verra ceci :

![GPO RH](assets/34.png)

#### 6.1.2 GPO pour le département IT

Pour le groupe IT, on va faire deux choses différentes.
Premièrement, on va déployer firefox sur les machines du groupe IT, et ensuite, on va leur mettre un fond d'écran personnalisé.

On va donc créer une nouvelle GPO, et tout mettre en place pour le déploiement de Firefox : 

![Installation automatique de firefox](assets/39.png)

Ensuite, on va déployer un fond d'écran personnalisé pour les membres du groupe IT.

Le fond d'écran ressemble à ceci :

![Fond d'écran IT](assets/35.png)

On commence par créer un dossier partagé sur le serveur, et on y met l'image du fond d'écran.
Ensuite, on crée une GPO pour le groupe IT, et on configure le fond d'écran comme indiqué.

![Fond d'écran IT](assets/36.png)

On voit que la configuration a été prise en compte.

![Fond d'écran IT](assets/37.png)

Si on se connecte à un utilisateur auquel on ne s'est pas encore connecté :

![Fond d'écran IT](assets/38.png)

Ça fonctionne !

#### 6.1.3 Redirection des dossiers Documents et Bureaux sur le serveur + Création d'un lecteur réseau pour le dossier commun

Pour finir, on va rediriger les dossiers Documents et Bureaux des utilisateurs du groupe IT et RH sur le serveur, afin que personne ne perde de données si un problème survient sur leur machine.

On commence par créer un dossier partagé sur le serveur (qu'on appellera Redirection), et on y met les dossiers `Documents` et `Bureau`.
Ensuite, on crée une GPO pour le groupe IT, et on configure la redirection des dossiers comme indiqué.
Enfin, on fait de même pour le groupe RH.

![Redirection des dossiers](assets/40.png)

On voit que la redirection a bien été prise en compte.


Lorsque tout ceci est fait, on peut mettre en place un lecteur réseau pour le dossier commun.

Prenons par exemple le groupe IT, et on fera pareil pour le groupe RH.

On se rend dans `Gestion de stratégie de groupe` > `GPO IT` > `Objets` > `Nouveau` > `Lecteur réseau`

![Lecteur réseau](assets/31.png)

On crée le lecteur réseau, et on va voir dans le gestionnaire de fichier de la machine de Sophie Morel que le lecteur réseau a bien été créé.

![Lecteur réseau](assets/32.png)

En effet, il apparaît donc ça a fonctionné.

### Le 6.2 et le 6.3 ont été réalisé en même temps que la création des GPO, donc les tests sont déjà effectués.

## 7. Vérifications et tests

Attributions des IP via DHCP :

![Attribution des IP](assets/DHCp.png)

Résolutions DNS :

![Résolutions DNS](assets/24.png)

Accès aux dossiers par une personne de la branche concernée, et uniquement par elle :

![Accès aux dossiers](assets/29.png)

Application d'une GPO pour un groupe :

![GPO appliquée](assets/34.png)

Pour un autre : 

![GPO appliquée](assets/38.png)

ou encore les dossiers réseaux : 

![Dossiers réseaux](assets/32.png)




*Ce PDF a été généré par github actions. Le compte-rendu s'affichera probablement mieux [en accédant à ce lien](https://github.com/oneloutre/TP_AD)*