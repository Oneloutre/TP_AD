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

