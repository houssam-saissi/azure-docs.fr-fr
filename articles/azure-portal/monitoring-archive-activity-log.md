---
title: Archiver le journal d’activité Azure | Microsoft Docs
description: Découvrez comment archiver votre journal d’activité Azure pour une conservation à long terme dans un compte de stockage.
author: johnkemnetz
manager: rboucher
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2016
ms.author: johnkem

---
# Archiver le journal d’activité Azure
Dans cet article, nous vous expliquons comment vous pouvez utiliser le portail Azure, les applets de commande PowerShell ou l’interface de ligne de commande multiplateforme pour archiver votre [**journal d’activité Azure**](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) dans un compte de stockage. Cette option est utile si vous souhaitez conserver votre journal d’activité pendant une période supérieure à 90 jours (en disposant d’un contrôle total sur la stratégie de rétention) à des fins d’audit, d’analyse statique ou de sauvegarde. Si vous devez conserver vos événements pendant 90 jours ou moins, il est inutile de configurer l’archivage sur un compte de stockage, puisque les événements du journal d’activité sont conservés dans la plateforme Azure pendant 90 jours sans que l’archivage ne soit activé.

## Composants requis
Avant de commencer, vous devez [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) sur lequel vous pouvez archiver votre journal d’activité. Nous vous recommandons vivement de ne pas utiliser un compte de stockage existant sur lequel sont stockées d’autres données de non-analyse, afin de pouvoir mieux contrôler l’accès aux données d’analyse. En revanche, si vous archivez également des journaux de diagnostic et des métriques sur un compte de stockage, il peut être judicieux d’utiliser ce compte pour votre journal d’activité afin de regrouper toutes vos données d’analyse au même emplacement. Le compte de stockage que vous utilisez doit être un compte de stockage à usage général, et non un compte de stockage Blob.

## Profil de journal
Pour archiver le journal d’activité à l’aide de l’une des méthodes ci-dessous, définissez le **profil journal** pour un abonnement. Le profil de journal définit le type d’événements qui sont stockés ou diffusés et les types de sortie : compte de stockage et/ou hub d’événements. Il définit également la stratégie de rétention (nombre de jours de conservation) pour les événements stockés dans un compte de stockage. Si la stratégie de rétention est définie sur zéro, les événements sont stockés indéfiniment. Elle peut également être définie sur n’importe quelle valeur comprise entre 1 et 2147483647. [Vous trouverez plus d’informations sur les profils de journal ici](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles).

## Archiver le journal d’activité à l’aide du portail
1. Dans le portail, cliquez sur le lien **Journal d’activité** dans le volet de navigation de gauche. Si vous ne voyez pas de lien pour le journal d’activité, cliquez d’abord sur le lien **More Services** (Plus de services).
   
    ![Accéder au panneau Journal d’activité](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. En haut du panneau, cliquez sur **Exporter**.
   
    ![Cliquer sur le bouton Exporter](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. Dans le panneau qui s’affiche, cochez la case pour **exporter vers un compte de stockage** et sélectionnez un compte de stockage.
   
    ![Créer un compte de stockage](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. À l’aide du curseur ou dans la zone de texte, définissez le nombre de jours pendant lesquels les événements du journal d’activité doivent être conservés dans votre compte de stockage. Si vous préférez que vos données soient conservées indéfiniment dans le compte de stockage, indiquez zéro.
5. Cliquez sur **Save**.

## Archiver le journal d’activité avec PowerShell
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus -RetentionInDays 180 -Categories Write,Delete,Action
```

| Propriété | Requis | Description |
| --- | --- | --- |
| StorageAccountId |Non |ID de ressource du compte de stockage dans lequel les journaux d’activité doivent être enregistrés. |
| Emplacements |Oui |Liste séparée par des virgules des régions pour lesquelles vous souhaitez collecter les événements du journal d’activité. Vous pouvez afficher une liste de toutes les régions [en consultant cette page](https://azure.microsoft.com/regions) ou en utilisant [l’API REST de gestion Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| RetentionInDays |Oui |Nombre de jours pendant lesquels les événements doivent être conservés, compris entre 1 et 2147483647. Une valeur de zéro signifie que les journaux seront stockés pour une durée indéfinie (pour toujours). |
| Catégories |Oui |Liste séparée par des virgules des catégories d’événements qui doivent être collectées. Les valeurs possibles sont Write, Delete et Action. |

## Archiver le journal d’activité avec l’interface de ligne de commande
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --locations global,westus,eastus,northeurope --retentionInDays 180 –categories Write,Delete,Action
```

| Propriété | Requis | Description |
| --- | --- | --- |
| name |Oui |Nom de votre profil de journal. |
| storageId |Non |ID de ressource du compte de stockage dans lequel les journaux d’activité doivent être enregistrés. |
| emplacements |Oui |Liste séparée par des virgules des régions pour lesquelles vous souhaitez collecter les événements du journal d’activité. Vous pouvez afficher une liste de toutes les régions [en consultant cette page](https://azure.microsoft.com/regions) ou en utilisant [l’API REST de gestion Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| retentionInDays |Oui |Nombre de jours pendant lesquels les événements doivent être conservés, compris entre 1 et 2147483647. Une valeur de zéro signifie que les journaux seront stockés pour une durée indéfinie (pour toujours). |
| categories |Oui |Liste séparée par des virgules des catégories d’événements qui doivent être collectées. Les valeurs possibles sont Write, Delete et Action. |

## Schéma de stockage du journal d’activité
Une fois que vous avez configuré l’archivage, un conteneur de stockage est créé dans le compte de stockage dès qu’un événement de journal d’activité se produit. Les objets blob au sein du conteneur suivent le même format dans le journal d’activité et les journaux de diagnostic. La structure de ces objets blob est la suivante :

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{ID de l’abonnement}/y={année, quatre chiffes}/m={mois, deux chiffres}/d={jour, deux chiffres}/h={heure, deux chiffres, format 24 heures}/m=00/PT1H.json
> 
> 

Voici un exemple de nom d’objet blob :

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json
> 
> 

Chaque objet blob PT1H.json contient un objet blob d’événements JSON qui se sont produits pendant l’heure spécifiée dans l’URL de l’objet blob (par exemple, h = 12). Pendant l’heure en cours, les événements sont ajoutés au fichier PT1H.json à mesure qu’ils se produisent. La valeur de minute (m = 00) est toujours 00, étant donné que les événements du journal d’activité sont répartis en différents objets blob par heure.

Dans le fichier PT1H.json, chaque événement est stocké dans le tableau « enregistrements », au format suivant :

```
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


| Nom de l'élément | Description |
| --- | --- |
| time |Horodatage lorsque l’événement a été généré par le service Azure traitant la demande correspondant à l’événement. |
| resourceId |ID de ressource de la ressource affectée. |
| operationName |Le nom de l’opération. |
| category |Catégorie de l’action, par exemple : Write (Écriture), Read (Lecture), Action. |
| resultType |Type de résultat, par exemple : Success (Réussite), Failure (Échec), Start (Démarrage) |
| resultSignature |Dépend du type de ressource. |
| durationMS |Durée de l’opération en millisecondes |
| callerIpAddress |Adresse IP de l’utilisateur qui a effectué l’opération, la revendication UPN ou la revendication SPN basée sur la disponibilité. |
| correlationId |Généralement un GUID au format chaîne. Les événements qui partagent un correlationId appartiennent à la même action uber. |
| identité |Objet blob JSON décrivant l’autorisation et les revendications. |
| autorisation |Objet blob des propriétés RBAC de l’événement. Inclut généralement les propriétés « action », « role » et « scope ». |
| level |Niveau de l’événement. Une des valeurs suivantes : Critical, Error, Warning, Informational et Verbose |
| location |Région dans laquelle se trouve l’emplacement (ou global). |
| properties |Jeu de paires `<Key, Value>` (p. ex. Dictionary) décrivant les détails de l’événement. |

> [!NOTE]
> Les propriétés et l’utilisation de ces propriétés peuvent varier en fonction de la ressource.
> 
> 

## Étapes suivantes
* [Télécharger des objets blob pour analyse](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
* [Transférer le journal d’activité vers Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
* [En savoir plus sur le journal d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

<!---HONumber=AcomDC_0907_2016-->