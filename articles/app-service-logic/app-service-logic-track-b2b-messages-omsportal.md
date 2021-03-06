---
title: Suivi des messages B2B dans le portail OMS | Microsoft Docs
description: Suivi des messages B2B dans le portail OMS
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: b3f564e32105708ddbd9027240c897fdd8ae2ac6
ms.openlocfilehash: 673b128c628a13c4af6c73c1a2d82953aadfd45a


---
# <a name="tracking-b2b-messages-in-oms-portal"></a>Suivi des messages B2B dans le portail OMS
La communication B2B implique des échanges de messages entre deux processus ou applications métier en cours d’exécution. Le suivi des messages B2B dans le portail OMS fournit des fonctionnalités de suivi web riches qui permettent de visualiser si les messages sont correctement traités.  Vous pouvez effectuer le suivi des éléments suivants :

* Nombre et état des messages
* État des accusés de réception
* Corrélation entre les messages et les accusés de réception
* Description détaillée des erreurs en cas échec
* Fonctionnalités de recherche

## <a name="prerequisites"></a>Composants requis
* Un compte Azure (que vous pouvez [créer gratuitement)](https://azure.microsoft.com/free)
* Un compte d’intégration (vous pouvez créer un [compte d’intégration](app-service-logic-enterprise-integration-create-integration-account.md) et activer la journalisation en suivant les étapes présentées [ici](app-service-logic-monitor-b2b-message.md))
* Une application logique (vous pouvez créer une [application logique](app-service-logic-create-a-logic-app.md) et activer la journalisation en suivant les étapes présentées [ici](app-service-logic-monitor-your-logic-apps.md))

## <a name="adding-logic-apps-b2b-solution-to-oms-portal"></a>Ajout d’une solution B2B Logic Apps sur le portail OMS
1. Sélectionnez **Plus de services** dans le portail, recherchez **Log Analytics** et sélectionnez **Log Analytics**
![Rechercher Log Analytics](./media/app-service-logic-track-b2b-messages-omsportal/browseloganalytics.png)  

2. Sélectionnez **Log Analytics**
![Sélectionner Log Analytics](./media/app-service-logic-track-b2b-messages-omsportal/selectla.png)

3. Sélectionnez **Portail OMS**pour ouvrir la page d’accueil du portail OMS ![Parcourir le portail OMS](./media/app-service-logic-track-b2b-messages-omsportal/omsportalpage.png)

4. Sélectionnez **Galerie de solutions**    
![Sélectionnez Galerie de solutions](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage1.png)

5. Sélectionnez **Logic Apps B2B**
![Sélectionner Logic Apps B2B](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage2.png)

6. Cliquez sur **Ajouter** pour ajouter **Messages Logic Apps B2B** sur la page d’accueil ![Sélectionner Ajouter](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage3.png)

7. Parcourez la page d’accueil pour afficher **Messages Logic Apps B2B**
![Sélectionner la page d’accueil](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage4.png)

8. Processus de publication de message ; la mise à jour de la page d’accueil affiche le nombre de messages ![Sélectionner la page d’accueil](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage6.png)

9. La sélection de **Messages Logic Apps B2B** sur la page d’accueil aboutit aux états de message AS2 et X12.  Les données sont basées sur le dernier jour.
![Sélection des messages Logic Apps B2B](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage5.png)

10. La sélection des messages AS2 ou X12 en fonction de leur état vous permet d’accéder à la liste des messages ![Sélectionner l’état de message AS2](./media/app-service-logic-track-b2b-messages-omsportal/as2messagelist.png)

    ![Sélectionner l’état de message X12](./media/app-service-logic-track-b2b-messages-omsportal/x12messagelist.png)

11. Sélectionnez une ligne dans la liste de messages AS2 ou X12 pour accéder à la recherche de journal.  La recherche de journal répertorie toutes les actions qui présentent le même **ID d’exécution**
![Sélectionner l’état du message](./media/app-service-logic-track-b2b-messages-omsportal/logsearch.png)


## <a name="next-steps"></a>Étapes suivantes
[Schéma de suivi personnalisé](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[Schéma de suivi AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[Schéma de suivi X12](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[En savoir plus sur Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


