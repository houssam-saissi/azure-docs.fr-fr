---
title: Préparation de la sauvegarde des charges de travail à l’aide d’Azure Backup Server | Microsoft Docs
description: Assurez-vous que votre environnement est correctement préparé à la sauvegarde des charges de travail à l’aide d’Azure Backup Server
services: backup
documentationcenter: ''
author: PVRK
manager: shivamg
editor: ''
keywords: serveur de sauvegarde Azure ; coffre de sauvegarde

ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2016
ms.author: jimpark;trinadhk;pullabhk;markgal

---
# Préparation de la sauvegarde des charges de travail à l’aide d’Azure Backup Server
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Azure Backup Server (Classic)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (Classic)](backup-azure-dpm-introduction-classic.md)
> 
> 

Cet article décrit la préparation de votre environnement à la sauvegarde des charges de travail à l’aide d’Azure Backup Server. Avec Azure Backup Server, vous pouvez protéger des charges de travail d’application telles que les machines virtuelles Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange et les clients Windows à partir d’une console unique : Vous pouvez également protéger les informations en tant que charges de travail d’un serveur (IaaS) telles que des machines virtuelles dans Azure.

> [!NOTE]
> Azure dispose de deux modèles de déploiement pour créer et utiliser des ressources : [Azure Resource Manager et Azure Classic](../resource-manager-deployment-model.md). Cet article fournit les informations et les procédures relatives à la restauration des machines virtuelles déployées à l’aide du modèle Resource Manager.
> 
> 

Azure Backup Server hérite d’une grand partie des fonctionnalités de sauvegarde de charge de travail de Data Protection Manager (DPM). Cet article comporte des liens vers la documentation sur DPM pour expliquer certaines des fonctionnalités partagées. Azure Backup Server partage néanmoins nombre des mêmes fonctionnalités que DPM. Azure Backup Server ne sauvegarde pas sur bande et ne l’intègre pas à System Center.

## 1\. Choisir une plateforme d’installation
La première étape de la mise en service d’Azure Backup Server consiste à configurer un serveur Windows Server. Il peut s’agir d’un serveur local ou d’un serveur dans Azure.

### Utilisation d’un serveur dans Azure
Lors du choix d’un serveur pour l’exécution d’Azure Backup Server, il est recommandé de commencer par une image de la galerie de Windows Server 2012 R2 Datacenter. L’article [Créer votre première machine virtuelle Windows dans le portail Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) propose un didacticiel de prise en main de la machine virtuelle recommandée dans Azure, même si vous n’avez jamais utilisé Azure. Configuration minimale recommandée pour la machine virtuelle serveur : A2 Standard avec 2 cœurs et 3,5 Go de RAM.

La protection des charges de travail à l’aide d’Azure Backup Server peut prendre plusieurs formes. L’article [Installation de DPM en tant que machine virtuelle Azure](https://technet.microsoft.com/library/jj852163.aspx), permet d’expliquer ces différentes formes. Merci de lire cet article dans son intégralité avant de déployer la machine.

### Utilisation d’un serveur local
Si vous ne souhaitez pas exécuter le serveur de base dans Azure, vous pouvez l’exécuter sur un ordinateur virtuel Hyper-V, un ordinateur virtuel VMware ou un hôte physique. Configuration minimale recommandée pour le matériel de serveur : 2 cœurs et 4 Go de RAM. Les systèmes d’exploitation pris en charge sont répertoriés dans le tableau ci-après.

| Système d'exploitation | Plateforme | SKU |
|:--- | --- |:--- |
| Windows Server 2012 R2 et derniers Service Packs |64 bits |Standard, Datacenter, Foundation |
| Windows Server 2012 et derniers Service Packs |64 bits |Datacenter, Foundation, Standard |
| Windows Storage Server 2012 R2 et derniers Service Packs |64 bits |Standard, Workgroup |
| Windows Storage Server 2012 et derniers Service Packs |64 bits |Standard, Workgroup |

Vous pouvez dédupliquer le stockage DPM en vous servant de la fonction de déduplication Windows Server. En savoir plus sur le fonctionnement du [DPM et de la déduplication](https://technet.microsoft.com/library/dn891438.aspx) en cas de déploiement sur des ordinateurs virtuels Hyper-V.

> [!NOTE]
> Vous ne pouvez pas installer Azure Backup Server sur une machine s’exécutant comme contrôleur de domaine.
> 
> 

Si vous envisagez d’ajouter ce serveur à un domaine à un moment donné, il est conseillé d’exécuter l’activité du domaine associé avant l’installation du serveur de sauvegarde Azure. Le déplacement d’une machine Azure Backup Server vers un nouveau domaine après le déploiement *n’est pas pris en charge*.

## 2\. Coffre Recovery Services
Que vous envoyiez des données de sauvegarde à Azure, ou que vous les conserviez en local, le logiciel doit être connecté à Azure. Pour être plus précis, l’ordinateur Azure Backup Server doit être enregistré dans un coffre Recovery Services.

Pour créer un coffre Recovery Services :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu Hub, cliquez sur **Parcourir** et, dans la liste des ressources, tapez **Recovery Services**. Au fur et à mesure des caractères saisis, la liste est filtrée. Cliquez sur **Coffre Recovery Services**.
   
    ![Créer un archivage de Recovery Services - Étape 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png) <br/>
   
    La liste des coffres Recovery Services est affichée.
3. Dans le menu **Coffres Recovery Services**, cliquez sur **Ajouter**.
   
    ![Créer un archivage de Recovery Services - Étape 2](./media/backup-azure-microsoft-azure-backup/rs-vault-menu.png)
   
    Le panneau du coffre Recovery Services s’affiche et vous invite à renseigner les champs **Nom**, **Abonnement**, **Groupe de ressources** et **Emplacement**.
   
    ![Créer un archivage de Recovery Services - Étape 5](./media/backup-azure-microsoft-azure-backup/rs-vault-attributes.png)
4. Pour le champ **Nom**, entrez un nom convivial permettant d’identifier le coffre. Le nom doit être unique pour l’abonnement Azure. Tapez un nom contenant entre 2 et 50 caractères. Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union.
5. Cliquez sur **Abonnement** pour afficher la liste des abonnements disponibles. Si vous n’êtes pas sûr de l’abonnement à utiliser, utilisez l’abonnement par défaut (ou suggéré). Vous ne disposez de plusieurs choix que si votre compte professionnel est associé à plusieurs abonnements Azure.
6. Cliquez sur **Groupe de ressources** pour afficher la liste des groupes de ressources disponibles ou sur **Nouveau** pour en créer un. Pour plus d’informations sur les groupes de ressources, consultez [Présentation d’Azure Resource Manager](../resource-group-overview.md).
7. Cliquez sur **Emplacement** pour sélectionner la région géographique du coffre.
8. Cliquez sur **Create**. La création de l’archivage de Recovery Services peut prendre un certain temps. Surveillez les notifications d'état dans l'angle supérieur droit du portail. Une fois votre archivage créé, il s'ouvre dans le portail.

### Définir la réplication du stockage
L’option de réplication du stockage vous permet de choisir entre stockage géo-redondant et stockage localement redondant. Par défaut, votre archivage utilise un stockage géo-redondant. Si vous utilisez Azure comme sauvegarde principale, laissez cette option inchangée. Choisissez Stockage localement redondant si vous souhaitez une option plus économique, mais moins durable. Pour en savoir plus sur les options de stockage [géo-redondant](../storage/storage-redundancy.md#locally-redundant-storage) et [localement redondant](../storage/storage-redundancy.md), consultez l’article [Réplication Azure Storage](../storage/storage-redundancy.md#geo-redundant-storage).

Pour modifier le paramètre de réplication du stockage :

1. Sélectionnez votre archivage pour ouvrir le tableau de bord associé et le panneau Paramètres. Si le panneau **Paramètres** ne s’ouvre pas, cliquez sur **Tous les paramètres** dans le tableau de bord du coffre.
2. Dans le panneau **Paramètres**, cliquez sur **Infrastructure de sauvegarde** > **Configuration de la sauvegarde** pour ouvrir le panneau **Configuration de la sauvegarde**. Dans le panneau **Configuration de la sauvegarde**, choisissez l’option de réplication du stockage à appliquer à votre coffre.
   
    ![Liste des archivages de sauvegarde](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)
   
    Après avoir sélectionné l’option de stockage pour votre archivage, vous pouvez associer la machine virtuelle à l’archivage. Pour commencer l’association, vous devez découvrir et enregistrer les machines virtuelles Azure.

## 3\. Package logiciel
### Téléchargement du package logiciel
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Si vous avez un coffre Recovery Services ouvert, passez à l’étape 3. Si vous n’avez aucun coffre Recovery Services ouvert, mais que vous vous trouvez dans le portail Azure, cliquez sur **Parcourir** dans le menu Hub.
   
   * Dans la liste des ressources, tapez **Recovery Services**.
   * Au fur et à mesure des caractères saisis, la liste est filtrée. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.
     
     ![Créer un archivage de Recovery Services - Étape 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)
     
     La liste des archivages de Recovery Services s’affiche.
   * Dans la liste des archivages de Recovery Services, sélectionnez un archivage.
     
     Le tableau de bord de l’archivage sélectionné s'ouvre.
     
     ![Ouvrir le panneau de l’archivage](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Le panneau Paramètres s’ouvre par défaut. S’il est fermé, cliquez sur **Paramètres** pour ouvrir le panneau Paramètres.
   
    ![Ouvrir le panneau de l’archivage](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Cliquez sur **Sauvegarde** dans **Mise en route** pour ouvrir l’Assistant Mise en route.
   
    ![Prise en main de la sauvegarde](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)
5. Dans la mise en route qui s’ouvre, l’écran Objectifs de la sauvegarde est automatiquement sélectionné. ![Backup-goals-default-opened](./media/backup-azure-microsoft-azure-backup/getting-started.png)
   
    Dans la section **Objectifs de la sauvegarde**, sélectionnez *Local* pour *Où votre charge de travail s’exécute-t-elle ?*.
   
    ![en local et charges de travail comme objectifs](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)
6. Sélectionnez les charges de travail que vous souhaitez protéger à l’aide d’Azure Backup Server dans *what workloads you want to protect* (Quelles charges de travail voulez-vous protéger ?), puis cliquez sur **OK**.
   
   > [!NOTE]
   > Si vous envisagez de protéger uniquement des fichiers et des dossiers, nous vous recommandons d’utiliser l’agent Azure Backup. Si vous envisagez de protéger davantage de charges de travail et pas seulement des fichiers et des dossiers, ou si à l’avenir vous envisagez d’étendre les besoins de protection, sélectionnez toutes les charges de travail.
   > 
   > 
   
    Ceci modifie l’Assistant Mise en route qui préparera l’infrastructure pour la protection des charges de travail de l’emplacement local vers Azure.
   
    ![Modification de l’Assistant Mise en route](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)
7. Dans le panneau **Préparer l’infrastructure** qui s’ouvre, cliquez sur les **liens de téléchargement** pour installer Azure Backup Server et télécharger les informations d’identification du coffre. Vous utilisez les informations d’identification du coffre lors de l’inscription d’Azure Backup Server dans le coffre Recovery Services. Les liens vous dirigent vers le Centre de téléchargement à partir duquel le package logiciel peut être téléchargé.
   
    ![Préparer l’infrastructure pour Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)
8. Sélectionnez tous les fichiers, puis cliquez sur **Suivant**. Téléchargez tous les fichiers en provenance de la page de téléchargement Microsoft Azure Backup et placez tous les fichiers dans le même dossier.
   
    ![Centre de téléchargement 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)
   
    Puisque la taille de téléchargement de l’ensemble des fichiers est > 3G, sur un lien de téléchargement de 10 Mbits/s, le téléchargement peut prendre jusqu’à 60 minutes.

### Extraction du package logiciel
Une fois que vous avez téléchargé tous les fichiers, cliquez sur **MicrosoftAzureBackupInstaller.exe**. **L’Assistant Installation de Microsoft Azure Backup** démarre l’extraction des fichiers d’installation vers l’emplacement que vous avez spécifié. Suivez les instructions de l’Assistant et cliquez sur le bouton **Extraire** pour commencer le processus d’extraction.

> [!WARNING]
> Au moins 4 Go d’espace libre sont nécessaires pour l’extraction des fichiers d’installation.
> 
> 

![Assistant Installation de Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Une fois le processus d’extraction terminé, cochez la case pour exécuter le fichier *setup.exe* que vous venez d’extraire pour installer Microsoft Azure Backup Server, puis cliquez sur le bouton **Terminer**.

### Installation du package logiciel
1. Cliquez sur **Microsoft Azure Backup** pour lancer l’Assistant d’installation.
   
    ![Assistant Installation de Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Dans l’écran d’accueil, cliquez sur le bouton **Suivant**. Vous êtes redirigé vers la section *Vérification des conditions préalables*. Dans cet écran, cliquez sur le bouton **Vérifier** afin de déterminer si les configurations matérielle et logicielle requises pour Azure Backup Server sont respectées. Si c’est le cas, un message indiquant que la machine respecte les conditions préalables requises s’affiche. Cliquez sur le bouton **Suivant**.
   
    ![Azure Backup Server - Accueil et contrôle des conditions préalables requises](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Microsoft Azure Backup Server requiert SQL Server Standard, et le package d’installation d’Azure Backup Server est fourni avec les fichiers binaires SQL Server appropriés. Lorsque vous démarrez avec une nouvelle installation d’Azure Backup Server, vous devez sélectionner l’option **Installer une nouvelle instance de SQL Server avec ce programme d’installation** et cliquer sur le bouton **Vérifier et installer**. Une fois les composants requis installés, cliquez sur **Suivant**.
   
    ![Serveur de sauvegarde Azure - Vérification SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)
   
    Si une défaillance se produit et qu’il est conseillé de redémarrer la machine, faites-le, puis cliquez sur **Vérifier à nouveau**.
   
   > [!NOTE]
   > Azure Backup Server ne fonctionne pas avec une instance de serveur SQL distante. L’instance utilisée par le serveur de sauvegarde Azure doit être installée en local.
   > 
   > 
4. Fournissez un emplacement pour l’installation des fichiers de serveur Microsoft Azure Backup, puis cliquez sur **Suivant**.
   
    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)
   
    Un emplacement temporaire est requis pour la sauvegarde sur Azure. Vérifiez que l’emplacement temporaire correspond à au moins 5 % du volume qu’il est prévu de sauvegarder dans le cloud. Pour la protection de disque, des disques séparés doivent être séparés une fois l’installation terminée. Pour plus d’informations sur les pools de stockage, consultez [Configurer des pools de stockage et de stockage sur disque](https://technet.microsoft.com/library/hh758075.aspx).
5. Fournissez un mot de passe fort pour les comptes utilisateur locaux restreints et cliquez sur **Suivant**.
   
    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Indiquez si vous souhaitez utiliser *Microsoft Update* pour vérifier les mises à jour et cliquez sur **Suivant**.
   
   > [!NOTE]
   > Nous conseillons de faire en sorte que Windows Update soit redirigé vers Microsoft Update, qui offre une sécurité et des mises à jour importantes pour Windows et autres produits, tels que le serveur Microsoft Azure Backup.
   > 
   > 
   
    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Examinez le *Résumé des paramètres* et cliquez sur **Installer**.
   
    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. L’installation se déroule en plusieurs phases. Lors de la première phase, l’Agent Microsoft Azure Recovery Services est installé sur le serveur. L’Assistant vérifie également la connectivité à Internet. Si la connectivité à Internet est disponible, vous pouvez poursuivre l’installation, sinon, vous devez fournir les informations de proxy détaillées pour se connecter à Internet.
   
    L’étape suivante consiste à configurer l’Agent Microsoft Azure Recovery Services. Dans le cadre de la configuration, vous devrez fournir les informations d’identification de coffre pour inscrire l’ordinateur dans le coffre Recovery Services. Vous allez également fournir une phrase secrète pour chiffrer/déchiffrer les données circulant entre Azure et votre environnement local. Vous pouvez automatiquement générer une phrase secrète ou fournir votre propre phrase secrète d’au minimum 16 caractères. Continuez avec l’Assistant jusqu’à ce que l’agent soit configuré.
   
    ![PreReq2 de serveur de sauvegarde Azure](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Une fois l’inscription du serveur Microsoft Azure Backup terminée et réussie, l’Assistant général d’installation procède à l’installation et à la configuration de SQL Server et des composants Azure Backup Server. Une fois l’installation du composant SQL Server terminée, les composants Azure Backup Server sont installés.
   
    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Lorsque l’étape d’installation est terminée, les icônes du produit sur le bureau sont en principe créées elles aussi. Double-cliquez sur l’icône pour lancer le produit.

### Ajouter de l’espace de stockage pour la sauvegarde
La première copie de sauvegarde est conservée sur l’espace de stockage associé à l’ordinateur du serveur de sauvegarde Azure. Pour plus d’informations sur l’ajout de disques, consultez la section [Configurer des pools de stockage et un disque de stockage](https://technet.microsoft.com/library/hh758075.aspx).

> [!NOTE]
> Vous devez ajouter un stockage de sauvegarde même si vous prévoyez d’envoyer des données à Azure. Dans l’architecture actuelle d’Azure Backup Server, le coffre Azure Backup conserve la *deuxième* copie des données, alors que le stockage local conserve la première copie (obligatoire).
> 
> 

## 4\. Connectivité réseau
Pour que le produit fonctionne correctement, Azure Backup Server exige la connectivité au service Azure Backup. Pour vérifier que la machine est bien connectée à Azure, utilisez l’applet de commande ```Get-DPMCloudConnection``` dans la console Azure Backup Server PowerShell. Si le résultat de l’applet de commande est TRUE, la connectivité existe. Dans le cas contraire, la connectivité n’existe pas.

Dans le même temps, l’abonnement Azure doit être dans un état correct. Pour déterminer l’état de votre abonnement et le gérer, connectez-vous au [portail d’abonnement](https://account.windowsazure.com/Subscriptions).

Une fois que vous connaissez l’état de la connectivité d’Azure et de l’abonnement Azure, vous pouvez utiliser le tableau ci-dessous pour déterminer l’impact sur la fonctionnalité de sauvegarde/restauration proposée.

| État de la connectivité | Abonnement Azure | Sauvegarde sur Azure | Sauvegarde sur disque | Restauration à partir d’Azure | Restauration à partir d’un disque |
| --- | --- | --- | --- | --- | --- |
| Connecté |Actif |Autorisé |Autorisé |Autorisé |Autorisé |
| Connecté |Expiré |Arrêté |Arrêté |Autorisé |Autorisé |
| Connecté |Approvisionnement annulé |Arrêté |Arrêté |Arrêté et points de restauration Azure supprimés |Arrêté |
| Connectivité perdue depuis > 15 jours |Actif |Arrêté |Arrêté |Autorisé |Autorisé |
| Connectivité perdue depuis > 15 jours |Expiré |Arrêté |Arrêté |Autorisé |Autorisé |
| Connectivité perdue depuis > 15 jours |Approvisionnement annulé |Arrêté |Arrêté |Arrêté et points de restauration Azure supprimés |Arrêté |

### Récupération après la perte de connectivité
Si vous êtes équipé d’un pare-feu ou d’un proxy qui empêche l’accès à Azure, vous devez mettre sur liste approuvée les adresses de domaine suivantes dans le profil de pare-feu/proxy :

* www.msftncsi.com
* *.Microsoft.com
* *.MicrosoftAzure.com
* *.microsoftonline.com
* *.windows.net

Une fois la connectivité à Azure restaurée sur l’ordinateur Azure Backup Server, les opérations qui peuvent être exécutées sont déterminées par l’état de l’abonnement Azure. Le tableau ci-dessus comporte les détails des opérations autorisées une fois l’ordinateur « connecté ».

### Gestion des états d’abonnement
Il est possible de faire passer un abonnement Azure de l’état *Expiré* ou *Approvisionnement annulé* à l’état *Actif*. Cependant, cette opération a certaines conséquences sur le comportement du produit lorsque l’état n’est pas *Actif* :

* Un abonnement dont *l’approvisionnement est annulé* ne fonctionne pas pendant la période pour laquelle l’approvisionnement est annulé. En redevenant *Actif*, la fonctionnalité de sauvegarde/restauration du produit est rétablie. Les données de sauvegarde présentes sur le disque local peuvent également être récupérées si le délai de rétention est suffisant. Toutefois, les données de sauvegarde dans Azure sont irrémédiablement perdues une fois que l’abonnement passe à l’état *Approvisionnement annulé*.
* Un abonnement *Expiré* ne fonctionne plus tant qu’il n’a pas été *réactivé*. Lorsque l’abonnement est à l’état *Expiré*, les sauvegardes planifiées ne sont pas exécutées.

## Résolution de problèmes
Si le serveur Microsoft Azure Backup échoue avec des erreurs pendant la phase d’installation (ou de sauvegarde ou restauration), reportez-vous à ce [document de codes d’erreur](https://support.microsoft.com/kb/3041338) pour plus d’informations. Vous pouvez également vous reporter au [FAQ relatives à la sauvegarde Azure](backup-azure-backup-faq.md)

## Étapes suivantes
Vous pouvez obtenir des informations détaillées sur la [préparation de votre environnement pour DPM](https://technet.microsoft.com/library/hh758176.aspx) sur le site Microsoft TechNet. Ce dernier contient également des informations relatives aux configurations prises en charge sur lesquelles Azure Backup Server peut être déployé et utilisé.

Vous pouvez utiliser ces articles pour apprendre à mieux connaître la notion de protection de charge de travail à l’aide du serveur Microsoft Azure Backup.

* [Sauvegarde SQL Server](backup-azure-backup-sql.md)
* [Sauvegarde de serveur SharePoint](backup-azure-backup-sharepoint.md)
* [Sauvegarde sur un autre serveur](backup-azure-alternate-dpm-server.md)

<!---HONumber=AcomDC_0803_2016-->