---
title: 'Étape 5 : Déploiement du service web Machine Learning | Microsoft Docs'
description: 'Étape 5 de la procédure pas à pas de développement d’une solution prédictive : Déploiement d’une expérience prédictive en tant que service web dans Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: garyericson
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: garye

---
# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>Étape 5 du didacticiel pas à pas : Déploiement du service web Azure Machine Learning
Voici la cinquième étape de la procédure pas à pas [Développement d’une solution d’analyse prédictive avec Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)

1. [Créer un espace de travail Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [Télécharger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3. [Créer une expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4. [Former et évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. **Déployer le service web**
6. [Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)

- - -
Pour que d’autres personnes puissent utiliser le modèle de prévision que nous avons développé dans cette procédure pas à pas, nous le déployons en tant que service web sur Azure.

Jusqu’à présent, nous avons réalisé l’expérience avec la formation de notre modèle. Mais le service déployé n’effectue plus l’apprentissage ; il produit des prédictions en évaluant l’entrée de l’utilisateur en fonction de notre modèle. Nous allons donc effectuer quelques préparatifs pour convertir cette expérience de ***i*** en expérience ***prédictive***. 

Ce processus comprend deux étapes :  

1. Convertir l’*expérience de formation* que nous avons créée en *expérience prédictive*.
2. Déployer l’expérience prédictive sous la forme d’un service web.

Mais tout d’abord, nous devons réduire un peu cette expérience. Nous disposons actuellement de deux modèles différents dans l’expérience, mais nous ne souhaitons avoir qu’un seul modèle au moment de déployer cette expérience en tant que service web.  

Supposons que nous ayons décidé que le modèle Arbre de décision optimisé est le plus adapté. La première chose à faire est de supprimer le module [Machine à vecteurs de support à deux classes][two-class-support-vector-machine], ainsi que les modules qui ont été utilisés pour sa formation. Vous pouvez d'abord copier l'expérience en cliquant sur **Enregistrer sous** dans la partie inférieure de la zone de dessin.

Nous devons supprimer les modules suivants :  

* [Machine à vecteurs de support à deux classes][two-class-support-vector-machine]
* Modules associés [Former le modèle][train-model] et [Noter le modèle][score-model]
* [Normaliser les données][normalize-data] (les deux)
* [Évaluer le modèle][evaluate-model]

Sélectionnez le module et appuyez sur la touche Suppr, ou cliquez avec le bouton droit sur le module, puis sélectionnez **Supprimer**.

À présent, nous sommes prêts à déployer ce modèle en utilisant l’[arbre de décision optimisé à deux classes][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertir l'expérience de formation en expérience prédictive
La conversion en expérience prédictive se déroule en trois étapes :

1. Enregistrer le modèle que nous avons formé, puis remplacer nos modules de formation
2. Réduire l’expérience en supprimant les modules uniquement nécessaires à l’apprentissage
3. Définir où le service web doit accepter l’entrée et où il génère la sortie

Heureusement, vous pouvez accomplir ces trois étapes en cliquant sur **Configurer le service web** dans la partie inférieure de la zone de dessin de l’expérience (sélectionnez l’option **Service web prédictif**).

Lorsque vous cliquez sur **Configurer le service web**, plusieurs choses se produisent :

* Le modèle formé est enregistré en tant que module **Modèle formé** unique dans la palette de module située à gauche de la zone de dessin de l’expérience (vous pouvez le trouver sous **Modèles formés**).
* Les modules qui ont été utilisés pour l’apprentissage sont supprimés. Plus précisément :
  * [Arbre de décision optimisé à deux classes][two-class-boosted-decision-tree]
  * [Former le modèle][train-model]
  * [Données fractionnées][split]
  * Le deuxième module [Exécuter le script R][execute-r-script] qui a été utilisé pour les données de test
* Le modèle formé enregistré est rajouté à l’expérience.
* Les modules **Entrée du service web** et **Sortie du service web** sont ajoutés.

> [!NOTE]
> L’expérience a été enregistrée en deux parties, sous des onglets qui ont été ajoutés en haut de la zone de dessin de l’expérience : l’expérience de formation d’origine se trouve sous l’onglet **Expérience de formation**, tandis que l’expérience de prévision qui vient d’être créée se trouve sous **Expérience prédictive**.
> 
> 

Nous devons effectuer une étape supplémentaire avec cette expérience.
Nous avons ajouté deux modules [Exécuter le script R][execute-r-script] pour fournir une fonction de pondération aux données pour l’apprentissage et le test. Nous n’avons pas besoin de le faire dans le modèle final.

Machine Learning Studio a supprimé un module [Exécuter le script R][execute-r-script] lors de la suppression du module [Fractionner][split]. Nous pouvons à présent supprimer l’autre et connecter [Éditeur de métadonnées][metadata-editor] directement à [Noter le modèle][score-model].    

Notre expérience doit alors ressembler à cela :  

![Scoring the trained model][4]  

> [!NOTE]
> Vous vous demandez peut-être pourquoi nous avons laissé le jeu de données Données de carte de crédit allemande UCI dans l’expérience prédictive. Ce service va utiliser les données de l’utilisateur et non le jeu de données d’origine : pourquoi laisser ce dernier dans le modèle ?
> 
> Il est vrai que ce service n'a pas besoin des données de la carte de crédit d'origine. Mais il a besoin du schéma pour ces données, incluant des informations telles que le nombre de colonnes et lesquelles sont numériques. Ces informations sur le schéma sont indispensables pour interpréter les données de l’utilisateur. Nous laissons ces composants connectés de façon à ce que le module de notation comporte le schéma du jeu de données lorsque le service est en cours d’exécution. Les données ne sont pas utilisées, uniquement le schéma.  
> 
> 

Exécutez une dernière fois l’expérience (cliquez sur **Exécuter**). Si vous voulez vérifier que le modèle fonctionne toujours, cliquez sur la sortie du module [Noter le modèle][score-model], puis sélectionnez **Afficher les résultats**. Vous constatez que les données d’origine sont affichées, ainsi que la valeur du risque sur le crédit (« Étiquettes notées ») et la probabilité de la notation (« Probabilités notées »). 

## <a name="deploy-the-web-service"></a>Déployer le service web
Vous pouvez déployer l’expérience en tant que service web classique ou nouveau service web basé sur Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Déployer comme un service web classique
Pour déployer un service web classique dérivé de notre expérience, cliquez sur **Déployer le service web** sous la zone de dessin, puis sélectionnez **Déployer le service web [Classic]**. Machine Learning Studio déploie l’expérience en tant que service web et vous amène au tableau de bord associé à ce service web. Dans le tableau de bord, vous pouvez revenir à l’expérience (**Afficher l’instantané** ou **Afficher les dernières**) et exécuter un test simple du service web (voir **Test du service web** ci-dessous). En outre, il contient des informations sur la création d’applications pouvant accéder au service web (l’étape suivante de cette procédure pas à pas aborde ce point plus en détail).

![Tableau de bord du service web][6]

Vous pouvez configurer le service en cliquant sur l'onglet **CONFIGURATION** . Vous pouvez modifier le nom du service (il s'agit par défaut du nom de l'expérience) et lui attribuer une description. Vous pouvez également attribuer des étiquettes plus significatives aux colonnes d'entrée et de sortie.  

![Configurer le service web][5]  

### <a name="deploy-as-a-new-web-service"></a>Déployer comme un nouveau service web
Pour déployer un nouveau service web dérivé de notre expérience, cliquez sur **Déployer le service web** sous la zone de dessin, puis sélectionnez **Déployer le service web [nouveau]**. Machine Learning Studio vous transfère vers la page consacrée à l’expérience de déploiement de services web Azure Machine Learning.

Entrez un nom pour le service web, puis choisissez un plan tarifaire. Si vous disposez d’un plan de tarification existant, vous pouvez le sélectionner. Sinon, vous devez créer un nouveau plan de tarification pour le service. 

1. Dans la liste déroulante **Price Plan** (Plan tarifaire), sélectionnez un plan existant ou choisissez l’option **Select new plan** (Sélectionner un nouveau plan).
2. Dans **Plan Name** (Nom du plan), tapez un nom servant à identifier le plan sur votre facture.
3. Sélectionnez un des **niveaux de plans mensuels**. Les niveaux de plan s’appliquent par défaut aux plans de votre région et votre service web est déployé dans cette région.

Cliquez sur **Déployer** pour ouvrir la page **Démarrage rapide** de votre service web.

Vous pouvez configurer le service en cliquant sur l'option de menu **Configurer** . Vous pouvez modifier le nom du service (il s'agit par défaut du nom de l'expérience) et lui attribuer une description. 

Pour tester le service web, cliquez sur l’option de menu **Test** (voir **Test du service web** ci-dessous). Pour plus d’informations sur la création d’applications pouvant accéder au service web, cliquez sur l’option de menu **Utiliser** (l’étape suivante de cette procédure pas à pas aborde ce point plus en détail).

> [!TIP]
> Vous pouvez mettre à jour le service web après l’avoir déployé. Par exemple, si vous souhaitez modifier votre modèle, modifiez l’expérience de formation, ajustez les paramètres du modèle, puis cliquez sur **Déployer le service web**. Sélectionnez ensuite **Déployer le service web [classique]** ou **Déployer le service web [nouveau]**. Lorsque vous redéployez l’expérience, le service web est remplacé par votre modèle mis à jour.  
> 
> 

## <a name="test-the-web-service"></a>Tester le service web
### <a name="test-a-classic-web-service"></a>Tester un service web classique
Vous pouvez tester le service dans Machine Learning Studio ou dans le portail des services web Azure Machine Learning. Tester dans le portail des services web Azure Machine Learning présente l’avantage de vous permettre d’activer les options suivantes. 

**Tester dans Machine Learning Studio**

Dans la page **TABLEAU DE BORD**, cliquez sur le bouton **Test** sous **Point de terminaison par défaut**. Une boîte de dialogue vous demande d’entrer les données du service. Les colonnes sont identiques à celles du jeu de données d'origine du risque sur le crédit allemand.  

Entrez un jeu de données, puis cliquez sur **OK**. 

**Tester dans le portail des services web Azure Machine Learning**.

Dans la page **TABLEAU DE BORD**, cliquez sur le lien d’aperçu **Test** sous **Point de terminaison par défaut**. La page de test dans le portail des services web Azure Machine Learning pour le point de terminaison de service web s’ouvre en vous invitant à entrer les données du service. Les colonnes sont identiques à celles du jeu de données d'origine du risque sur le crédit allemand.

Cliquez sur **Tester Demande-réponse**. 

Dans le service web, les données passent successivement par le module **Entrée du service web**, le module [Éditeur de métadonnées][metadata-editor] et le module [Noter le modèle][score-model], dans lequel elles sont évaluées. Les résultats sont ensuite générés à partir du service web par le biais du module **Sortie du service web**.

**Tester un nouveau service web**

Dans le portail des services web Azure Machine Learning, cliquez sur **Tester** en haut de la page. La page **Test** s’ouvre pour vous permettre d’entrer les données du service. Les champs d’entrée affichés sont identiques à ceux du jeu de données d'origine du risque sur le crédit allemand. 

Entrez un jeu de données, puis cliquez sur **Test Request-Response**(Tester la requête-réponse).

Les résultats du test apparaîtront sur le côté droit de la page, dans la colonne de sortie. 

Lorsque vous testez dans le portail des services web Azure Machine Learning, vous pouvez activer des exemples de données à utiliser pour tester le service Demande-Réponse. Si vous avez créé le service web dans Machine Learning Studio, les exemples de données proviennent des données utilisées dans votre modèle.

> [!TIP]
> Compte tenu de la façon dont nous avons configuré l’expérience prédictive, les résultats du module [Noter le modèle][score-model] sont retournés dans leur totalité. Ces résultats incluent toutes les données d’entrée, ainsi que la valeur du risque de crédit et la probabilité de la notation. Si vous souhaitez retourner autre chose, par exemple, uniquement la valeur du risque de crédit, vous pouvez insérer un module [Colonnes de projet][project-columns] entre les modules [Noter le modèle][score-model] et **Sortie du service web** pour éliminer les colonnes à ne pas retourner. 
> 
> 

## <a name="manage-the-web-service"></a>Gérer le service web
**Gérer un service web classique**

Après avoir déployé votre service web classique, vous pouvez le gérer à partir du [portail Azure Classic](https://manage.windowsazure.com).

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Dans le volet des services Microsoft Azure, cliquez sur **MACHINE LEARNING**.
3. Cliquez sur votre espace de travail.
4. Cliquez sur l’onglet **Services web**.
5. Cliquez sur le service web que nous avons créé.
6. Cliquez sur le point de terminaison « par défaut ».

À partir de là, vous pouvez effectuer des opérations telles que surveiller le fonctionnement du service web et effectuer des ajustements de performances en modifiant le nombre d’appels simultanés que le service peut gérer.
Vous pouvez même publier votre service web dans Azure Marketplace.

Pour plus d'informations, consultez la page suivante :

* [Création de points de terminaison](machine-learning-create-endpoint.md)
* [Mise à l’échelle du service web](machine-learning-scaling-webservice.md)
* [Publier un service web Azure Machine Learning sur Azure Marketplace](machine-learning-publish-web-service-to-azure-marketplace.md)

**Gérer un service web dans le portail des services web Azure Machine Learning**

Une fois votre service web déployé, qu’il soit classique ou nouveau, vous pouvez le gérer à partir du [portail des services web Azure Machine Learning](https://servics.azureml.net).

Pour surveiller les performances de votre service web :

1. Connectez-vous au [portail des services web Azure Machine Learning](https://servics.azureml.net).
2. Cliquez sur **Services web**.
3. Cliquez sur service web.
4. Cliquez sur **Tableau de bord**.

- - -
**Étape suivante : [Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/


<!--HONumber=Oct16_HO2-->


