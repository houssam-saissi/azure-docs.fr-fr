---
title: Analytics, le puissant outil de recherche d’Application Insights | Microsoft Docs
description: 'Présentation d’Analytics, le puissant outil de recherche d’Application Insights. '
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: awills

---
# Analytics dans Application Insights
[Analytics](app-insights-analytics.md) est la fonctionnalité de recherche performante d’[Application Insights](app-insights-overview.md). Ces pages décrivent le langage de requête Analytics.

* **[Regardez la vidéo d’introduction](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Testez la version d’évaluation d’Analytics sur nos données simulées](https://analytics.applicationinsights.io/demo)** si votre application n’envoie pas encore de données à Application Insights.

## Requêtes dans Analytics
Une requête classique est une table *source* suivie d’une série *d’opérateurs* séparés par des `|`.

Par exemple, essayons de découvrir à quelle heure les citoyens de Hyderabad accèdent à notre application web. Et parallèlement, découvrons les codes de résultats qui sont retournés à leurs requêtes HTTP.

```AIQL

    requests      // Table of events that log HTTP requests.
    | where timestamp > ago(7d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

Nous comptons des adresses IP client distinctes, en les regroupant par heure de la journée sur les sept derniers jours.

Affichons les résultats avec la présentation graphique à barres, en choisissant d’empiler les résultats issus de codes de réponse différents :

![Sélectionnez le graphique à barres, les axes x et y, puis la segmentation](./media/app-insights-analytics/020.png)

Il semblerait que notre application soit plus populaire lors de la pause déjeuner et à l’heure du coucher à Hyderabad. (Et nous devrions examiner ces 500 codes).

Il existe également des opérations statistiques puissantes :

![](./media/app-insights-analytics/025.png)

Le langage possède de nombreuses fonctionnalités attrayantes :

* [Filtrer](app-insights-analytics-reference.md#where-operator) vos données de télémétrie d’application brutes sur tous les champs, y compris les propriétés et métriques personnalisées.
* [Joindre](app-insights-analytics-reference.md#join-operator) plusieurs tables ; mettez en corrélation les demandes avec les affichages de page, les appels de dépendance, les exceptions et les suivis du journal.
* [Agrégations](app-insights-analytics-reference.md#aggregations) statistiques puissantes.
* Toutes aussi puissantes que SQL, mais beaucoup plus facile pour les requêtes complexes : au lieu d’imbriquer des instructions, vous dirigez les données d’une opération élémentaire à l’autre.
* Visualisations immédiates et puissantes.

## Connectez-vous à vos données Application Insights
Ouvrez Analytics à partir du [panneau Vue d’ensemble](app-insights-dashboards.md) de votre application dans Application Insights :

![Ouvrez portal.azure.com, ouvrez votre ressource Application Insights, puis cliquez sur Analyse.](./media/app-insights-analytics/001.png)

## Limites
Les résultats de requête sont actuellement limités à une seule semaine de données d’historique.

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

## Étapes suivantes
* Nous vous recommandons de commencer par la [visite guidée du langage](app-insights-analytics-tour.md).

<!---HONumber=AcomDC_0831_2016-->