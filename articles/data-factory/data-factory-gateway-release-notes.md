---
title: Notes de version pour la passerelle de gestion des données | Microsoft Docs
description: Notes de version pour la passerelle de gestion des données
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2016
ms.author: spelluru

---
# Notes de version pour la passerelle de gestion des données
Un des défis de l’intégration de données modernes consiste à déplacer en toute transparence des données vers et depuis un site local et le cloud. Azure Data Factory facilite cette intégration avec la passerelle de gestion des données Microsoft, qui est un agent pouvant être installé en local pour activer le déplacement de données hybrides.

Consultez les articles suivants pour des informations détaillées sur la passerelle de gestion des données et son utilisation :

* [Passerelle de gestion de données](data-factory-data-management-gateway.md)
* [Déplacement de données entre des emplacements locaux et le cloud à l'aide d’Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)

## Version actuelle (2.2.6072.1)
* Permet de paramétrer le proxy HTTP pour la passerelle à l’aide du Gestionnaire de configuration de passerelle. Si configurés, Azure Blob, Azure Table, Azure Data Lake et Document DB sont accessibles via le proxy HTTP.
* Prend en charge la gestion des en-têtes pour TextFormat lors de la copie des données depuis/vers un objet Blob Azure, Azure Data Lake Store, le système de fichiers local ou un système de fichiers HDFS local.
* Prend en charge la copie des données d’objets blob d’ajouts et de pages, ainsi que des objets blob de blocs déjà pris en charge.
* Introduit le nouvel état de passerelle **En ligne (limité)**, qui indique que la fonctionnalité principale de la passerelle fonctionne, à l’exception de la prise en charge de l’opération interactive pour l’assistant de copie.
* Améliore la robustesse de l’inscription de la passerelle avec la clé d’inscription.

## Versions antérieures
## 2\.1.6040.1
* Le pilote DB2 est désormais inclus dans le package d’installation de la passerelle. Il est inutile de l’installer séparément.
* Le pilote DB2 prend désormais en charge z/OS et DB2 pour i (AS/400), ainsi que les plateformes déjà prises en charge (Windows, Unix et Linux).
* Prend en charge l’utilisation de DocumentDB comme source ou destination de banques de données locales
* Prend en charge la copie de données depuis/vers un stockage d’objet blob à chaud ou à froid, ainsi que le compte de stockage à usage général déjà pris en charge.
* Permet de vous connecter l’instance SQL Server locale via la passerelle avec des droits de connexion à distance.

## 2\.0.6013.1
* Vous pouvez sélectionner la langue/culture utilisée par une passerelle lors de l’installation manuelle.
* Lorsqu’une passerelle ne fonctionne pas comme prévu, vous pouvez choisir d’envoyer les journaux des sept derniers jours de la passerelle à Microsoft pour faciliter la résolution du problème. Si la passerelle n’est pas connectée au service cloud, vous pouvez choisir d’enregistrer et d’archiver les journaux de la passerelle.
* Améliorations de l’interface utilisateur du gestionnaire de configuration de la passerelle :
  * État de la passerelle plus visible dans l’onglet Accueil.
  * Commandes réorganisées et simplifiées.
* Vous pouvez copier des données à partir d’un stockage à l’aide de [l’outil de prévisualisation de copie sans code](data-factory-copy-data-wizard-tutorial.md). Pour plus d’informations sur cette fonctionnalité, consultez [Copie intermédiaire](data-factory-copy-activity-performance.md#staged-copy).
* Vous pouvez utiliser la passerelle de gestion des données pour entrer des données directement à partir d’une base de données SQL Server locale dans Azure Machine Learning.
* Amélioration des performances
  * Amélioration des performances d’affichage du schéma/de l’aperçu par rapport à SQL Server dans l’outil de prévisualisation de copie sans code.

## 1\.12.5953.1
* Résolution des bogues

## 1\.11.5918.1
* La taille maximale du journal des événements de la passerelle a été augmentée de 1 Mo à 40 Mo.
* Une boîte de dialogue d’avertissement s’affiche si un redémarrage est nécessaire pendant la mise à jour automatique de la passerelle. Vous pouvez choisir de redémarrer immédiatement ou plus tard.
* En cas d’échec de la mise à jour automatique, le programme d’installation de la passerelle retente la mise à jour automatique trois fois au maximum.
* Amélioration des performances
  * Améliorez les performances lors du chargement de tables de grande taille à partir du serveur local dans le scénario de copie sans code.
* Résolution des bogues

## 1\.10.5892.1
* Amélioration des performances
* Résolution des bogues

## 1\.9.5865.2
* Capacité de mise à jour automatique Zero Touch
* Nouvelle icône de barre d’état système avec des indicateurs d’état de la passerelle
* Possibilité de mise à jour immédiate depuis le client
* Possibilité de définir l’heure de planification de la mise à jour
* Script PowerShell disponible pour activer/désactiver la mise à jour automatique
* Prise en charge du format JSON
* Amélioration des performances
* Résolution des bogues

## 1\.8.5822.1
* Amélioration de l’expérience de résolution des problèmes
* Amélioration des performances
* Résolution des bogues

### 1\.7.5795.1
* Amélioration des performances
* Résolution des bogues

### 1\.7.5764.1
* Amélioration des performances
* Résolution des bogues

### 1\.6.5735.1
* Prise en charge de la source/du récepteur HDFS en local
* Amélioration des performances
* Résolution des bogues

### 1\.6.5696.1
* Amélioration des performances
* Résolution des bogues

### 1\.6.5676.1
* Prise en charge des outils de diagnostic dans le Gestionnaire de configuration
* Prise en charge des colonnes de table pour les sources de données tabulaires pour Microsoft Azure Data Factory
* Prise en charge de SQL DW pour Microsoft Azure Data Factory
* Prise en charge de Reclusive dans BlobSource et FileSource pour Microsoft Azure Data Factory
* Prise en charge de CopyBehavior (MergeFiles, PreserveHierarchy et FlattenHierarchy) dans BlobSink et FileSink avec copie binaire pour Microsoft Azure Data Factory
* Prise en charge du signalement de progression de l’activité de copie pour Microsoft Azure Data Factory
* Prise en charge de la validation de connectivité des sources de données pour Microsoft Azure Data Factory
* Résolution des bogues

### 1\.6.5672.1
* Prise en charge des noms de tables pour la source de données ODBC pour Microsoft Azure Data Factory
* Amélioration des performances
* Résolution des bogues

### 1\.6.5658.1
* Prise en charge du récepteur de fichier pour Microsoft Azure Data Factory
* Prise en charge de la conservation de la hiérarchie dans une copie binaire pour Microsoft Azure Data Factory
* Prise en charge de l’idempotence de l’activité de copie pour Microsoft Azure Data Factory
* Résolution des bogues

### 1\.6.5640.1
* Prise en charge de 3 sources de données supplémentaires pour Microsoft Azure Data Factory (ODBC, OData, HDFS)
* Prise en charge du caractère guillemet dans l’analyseur CSV pour Microsoft Azure Data Factory
* Prise en charge de la compression (BZip2)
* Résolution des bogues

### 1\.5.5612.1
* Prise en charge de cinq bases de données relationnelles pour Microsoft Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata et Sybase)
* Prise en charge de la compression (Gzip et Deflate)
* Amélioration des performances
* Résolution des bogues

### 1\.4.5549.1
* Ajout de la prise en charge de source de données Oracle pour Microsoft Azure Data Factory
* Amélioration des performances
* Résolution des bogues

### 1\.4.5492.1
* Binaire unifié qui prend en charge à la fois des services Microsoft Azure Data Factory et Office 365 Power BI
* Affinage du processus d’inscription et de l’interface utilisateur de configuration
* Microsoft Azure Data Factory : prise en charge des entrées et sorties Azure pour la source de données SQL Server

### 1\.2.5303.1
* Correction du problème de délai d’expiration pour la prise en charge de connexions de source de données chronophages supplémentaires.

### 1\.1.5526.8
* .NET Framework 4.5.1 est requis pour l’installation.

### 1\.0.5144.2
* Aucune modification affectant les scénarios Microsoft Azure Data Factory.

<!---HONumber=AcomDC_0831_2016-->