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

