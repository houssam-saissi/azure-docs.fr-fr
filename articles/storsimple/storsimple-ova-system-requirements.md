---
title: Configuration système requise pour StorSimple Virtual Array
description: En savoir plus sur la configuration logicielle et du réseau requise pour votre StorSimple Virtual Array
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/14/2016
ms.author: alkohli

---
# Configuration système requise pour StorSimple Virtual Array
## Vue d'ensemble
Cet article décrit la configuration système requise importante pour Microsoft Azure StorSimple Virtual Array (également appelé appareil virtuel local StorSimple ou appareil virtuel StorSimple) et pour les clients de stockage accédant au tableau. Nous vous recommandons de lire attentivement les informations suivantes avant de déployer votre système Azure StorSimple, puis d’y revenir si nécessaire pendant le déploiement et les opérations suivantes.

Les conditions requises sont les suivantes :

* **Configuration logicielle requise pour les clients de stockage** : décrit les plateformes de virtualisation, les navigateurs web, les initiateurs iSCSI, les clients SMB pris en charge, la configuration minimale requise pour les appareils virtuels et toutes les exigences requises pour ces systèmes d'exploitation.
* **Conditions requises de mise en réseau pour l’appareil StorSimple** : fournit des informations sur les ports qui doivent être ouverts dans votre pare-feu pour autoriser iSCSI, le cloud ou le trafic de gestion.

Les informations de configuration système requise StorSimple publiées dans cet article s'appliquent uniquement aux tableaux virtuels StorSimple.

* Pour les appareils de la gamme 8000, accédez à [Configuration système requise pour votre appareil de la gamme StorSimple 8000](storsimple-system-requirements.md).
* Pour les appareils de la gamme 7000, accédez à [Configuration système requise pour votre appareil de la gamme StorSimple 5000-7000](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## Configuration logicielle requise
La configuration logicielle requise inclut les informations sur les navigateurs web pris en charge, les versions SMB, les plateformes de virtualisation et la configuration minimale requise pour l'appareil virtuel.

### Plateformes de virtualisation prises en charge
| **Hyperviseur** | **Version** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 et versions ultérieures |
| VMware ESXi |5\.5 et versions ultérieures |

### Configuration requise de l'appareil virtuel
| **Composant** | **Prérequis** |
| --- | --- |
| Nombre minimal de processeurs virtuels (cœurs) |4 |
| Quantité minimale de mémoire (RAM) |8 Go |
| Espace disque<sup>1</sup> |Disque de système d'exploitation - 80 Go <br></br>Disque de données - 500 Go à 8 To |
| Nombre minimal d'interfaces réseau |1 |
| Bande passante Internet minimale<sup>2</sup> |5 Mbits/s |

<sup>1</sup> - Allocation dynamique

<sup>2</sup> - La configuration requise du réseau peut varier selon le taux de modification quotidien des données. Par exemple, si un appareil doit sauvegarder 10 Go de modifications ou plus pendant une journée, alors la sauvegarde quotidienne via une connexion 5 Mbits/s peut prendre jusqu'à 4,25 heures (si les données ne sont pas compressées ou dédupliquées).

### Navigateurs web pris en charge
| **Composant** | **Version** | **Conditions/remarques supplémentaires** |
| --- | --- | --- |
| Microsoft Edge |Version la plus récente | |
| Internet Explorer |Version la plus récente |Testé avec Internet Explorer 11 |
| Google Chrome |Version la plus récente |Testé avec Chrome 46 |

### Clients de stockage pris en charge
La configuration logicielle requise suivante concerne les initiateurs iSCSI qui accèdent à votre StorSimple Virtual Array (configuré comme un serveur iSCSI).

| **Systèmes d’exploitation pris en charge** | **Version requise** | **Conditions/remarques supplémentaires** |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2 |StorSimple peut créer des volumes alloués dynamiquement et de façon complète. Il ne peut pas créer des volumes alloués partiellement. Les volumes iSCSI StorSimple sont pris en charge uniquement pour : <ul><li>Volumes simples sur des disques de base Windows.</li><li>Windows NTFS pour formater un volume.</li> |

La configuration logicielle requise suivante concerne les clients SMB qui accèdent à votre StorSimple Virtual Array (configuré comme un serveur de fichiers).

| **Version SMB** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

## Configuration requise du réseau
Le tableau ci-dessous répertorie les ports qui doivent être ouverts dans votre pare-feu pour autoriser le trafic iSCSI, SMB, cloud ou de gestion. Dans ce tableau, *entrée* ou *entrant* représente la direction à partir de laquelle les demandes client entrantes accèdent à votre appareil. *Sortant* ou *Sortie* représente la direction vers laquelle votre appareil StorSimple envoie des données de façon externe, au delà du déploiement : par exemple, sortant vers Internet.

| **Numéro de port<sup>1</sup>** | **Entrant ou sortant** | **Étendue de ports** | **Obligatoire** | **Remarques** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Sortie |WAN |Non |Le port de sortie est utilisé pour accéder à Internet afin de récupérer les mises à jour. <br></br>Le proxy web sortant est configurable par l’utilisateur. |
| TCP 443 (HTTPS) |Sortie |WAN |Oui |Le port de sortie est utilisé pour accéder aux données dans le cloud. <br></br>Le proxy web sortant est configurable par l’utilisateur. |
| UDP 53 (DNS) |Sortie |WAN |Dans certains cas, consultez les notes. |Ce port est requis seulement si vous utilisez un serveur DNS Internet. <br></br> **Remarque** : si vous déployez un serveur de fichiers, nous recommandons l’utilisation d’un serveur DNS local. |
| UDP 123 (NTP) |Sortie |WAN |Dans certains cas, consultez les notes. |Ce port est requis seulement si vous utilisez un serveur NTP Internet.<br></br> **Remarque :** si vous déployez un serveur de fichiers, nous vous recommandons de synchroniser l’heure avec vos contrôleurs de domaine Active Directory. |
| TCP 80 (HTTP) |Dans |LAN |Oui |Il s'agit du port d'entrée pour l'interface utilisateur locale de l'appareil StorSimple pour la gestion locale. <br></br> **Remarque** : l’accès à l’interface utilisateur locale par le biais du protocole HTTP est automatiquement redirigé vers HTTPS. |
| TCP 443 (HTTPS) |Dans |LAN |Oui |Il s'agit du port d'entrée pour l'interface utilisateur locale de l'appareil StorSimple pour la gestion locale. |
| TCP 3260 (iSCSI) |Dans |LAN |Non |Ce port est utilisé pour accéder aux données via iSCSI. |

<sup>1</sup> Aucun port entrant ne doit être ouvert sur l’Internet public.

> [!IMPORTANT]
> Assurez-vous que le pare-feu ne modifie ou ne déchiffre pas le trafic SSL entre l’appareil StorSimple et Azure.
> 
> 

### Modèles d’URL pour règles de pare-feu
Les administrateurs réseau peuvent souvent configurer des règles de pare-feu avancées basées sur des modèles d’URL afin de filtrer le trafic entrant et sortant. Votre tableau virtuel et le service StorSimple Manager dépendent d’autres applications Microsoft telles qu’Azure Service Bus, Azure Active Directory Access Control, ou que des comptes de stockage et des serveurs Microsoft Update. Les modèles d’URL associés à ces applications peuvent être utilisés pour configurer des règles de pare-feu. Il est important de comprendre que les modèles d’URL associés à ces applications peuvent changer. L’administrateur réseau doit alors surveiller et mettre à jour les règles de pare-feu pour votre appareil StorSimple si nécessaire.

Dans la plupart des cas, nous vous recommandons de définir librement les règles de pare-feu pour le trafic sortant en fonction des ADresses IP fixes StorSimple. Toutefois, vous pouvez utiliser les informations ci-dessous pour définir les règles de pare-feu avancées qui sont nécessaires à la création d’environnements sécurisés.

> [!NOTE]
> * Les adresses IP d’appareil (sources) doivent toujours être définies sur l’ensemble des interfaces réseau activées pour le cloud.
> * Les adresses IP de destination doivent être définies sur les [plages d’adresses IP Azure Datacenter](https://www.microsoft.com/fr-FR/download/confirmation.aspx?id=41653).
> 
> 

| Modèle d’URL | Composant/Fonctionnalité |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` |Service StorSimple Manager<br>Access Control Service<br>Azure Service Bus |
| `http://*.backup.windowsazure.com` |Enregistrement de l’appareil |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Révocation de certificat |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Comptes de stockage Azure et surveillance |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Serveurs Microsoft Update<br> |
| `http://*.deploy.akamaitechnologies.com` |CDN Akamai |
| `https://*.partners.extranet.microsoft.com/*` |Package de prise en charge |
| `http://*.data.microsoft.com ` |Service de télémétrie dans Windows, consultez l’article [Mise à jour de l’expérience client et du diagnostic de la télémétrie](https://support.microsoft.com/fr-FR/kb/3068708) |

## Étape suivante
* [Préparation du portail pour déployer StorSimple Virtual Array](storsimple-ova-deploy1-portal-prep.md)

<!---HONumber=AcomDC_0720_2016-->