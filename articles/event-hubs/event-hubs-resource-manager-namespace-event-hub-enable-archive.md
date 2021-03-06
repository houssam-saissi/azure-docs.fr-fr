---
title: Créer un espace de noms Event Hubs avec Event Hub et activer Archive à l’aide d’un modèle Azure Resource Manager | Microsoft Docs
description: Créer un espace de noms Event Hubs avec Event Hub et activer Archive à l’aide d’un modèle Azure Resource Manager
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''

ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 09/14/2016
ms.author: ShubhaVijayasarathy

---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-enable-archive-using-an-azure-resource-manager-template"></a>Créer un espace de noms Event Hubs avec Event Hub et activer Archive à l’aide d’un modèle Azure Resource Manager
Cet article montre comment utiliser un modèle Azure Resource Manager qui crée un espace de noms Event Hubs avec un Event Hub et active Archive sur votre Event Hub. Vous apprenez à définir les ressources à déployer et à configurer les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins

Pour en savoir plus sur la création de modèles, voir [Création de modèles Azure Resource Manager][Création de modèles Azure Resource Manager].

Pour plus d’informations sur les pratiques et les modèles des conventions d’affectation de noms des ressources Azure, consultez [Conventions d’affectation de noms des ressources Azure][Conventions d’affectation de noms des ressources Azure].

Pour le modèle complet, consultez le [modèle d’Event Hub et activer Archive][modèle d’Event Hub et activer Archive] sur GitHub.

> [!NOTE]
> Pour connaître les derniers modèles, recherchez Event Hubs dans la galerie de [modèles de démarrage rapide Azure][modèles de démarrage rapide Azure] .
> 
> 

## <a name="what-you-deploy?"></a>Que déployer ?
Avec ce modèle, vous déployez un espace de noms Event Hubs avec un Event Hub et vous activez Archive.

[Event Hubs](event-hubs-what-is-event-hubs.md) est un service de traitement des événements utilisé pour fournir des entrées d’événements et de télémétrie dans Azure à grande échelle, avec faible latence et fiabilité élevée. Event Hubs Archive vous permet de transmettre automatiquement les données en continu de vos Event Hubs au stockage d’objets blob Azure de votre choix dans un intervalle de temps ou de taille que vous spécifiez.

Pour exécuter automatiquement le déploiement, cliquez sur le bouton ci-dessous :

[![Déploiement sur Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-archive%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres
Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Ce modèle inclut une section appelée `Parameters` , qui contient toutes les valeurs des paramètres. Vous devez définir un paramètre pour les valeurs qui varient en fonction du projet que vous déployez ou de l’environnement dans lequel vous effectuez le déploiement. Ne définissez pas de paramètres pour les valeurs qui restent inchangées. Chaque valeur de paramètre est utilisée dans le modèle pour définir les ressources déployées.

Le modèle définit les paramètres suivants.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName
Nom de l’espace de noms Event Hubs à créer.

```
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName
Nom du Event Hub créé dans l’espace de noms Event Hubs.

```
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the Event Hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays
Le nombre de jours pendant lesquels vous voulez conserver les messages dans votre Event Hub. 

```
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in Event Hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount
Le nombre de partitions que vous voulez dans votre Event Hub.

```
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="archiveenabled"></a>archiveEnabled
Activer Archive dans votre Event Hub.

```
"archiveEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Archive for your Event Hub"
    }
 }
```
### <a name="archiveencodingformat"></a>archiveEncodingFormat
Le format de codage que vous spécifiez pour sérialiser les données d’événement.

```
"archiveEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format Archive serializes the EventData"
    }
}
```

### <a name="archivetime"></a>archiveTime
L’intervalle de temps où Archive démarre l’archivage des données dans le stockage des objets blob Azure.

```
"archiveTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"the time window in seconds for the archival"
    }
}
```

### <a name="archivesize"></a>archiveSize
L’intervalle de taille selon lequel Archive démarre l’archivage des données dans le stockage des objets blob Azure.

```
"archiveSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"the size window in bytes for archival"
    }
}
```

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId
Archive nécessite un ID de ressource de compte de stockage pour activer l’archivage dans le stockage Azure de votre choix.

```
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Account resource id where you want the blobs be archived"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName
Conteneur d’objets blob dans lequel vous voulez archiver les données d’événement.

```
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Container that you want the blobs archived in"
    }
}
```


### <a name="apiversion"></a>apiVersion
Version d’API du modèle.

```
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

## <a name="resources-to-deploy"></a>Ressources à déployer
Crée un espace de noms de type **Event Hubs**, avec un Event Hub et active Archive.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
                  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
                  "PartitionCount":"[parameters('partitionCount')]",
                  "ArchiveDescription":{
                        "enabled":"[parameters('archiveEnabled')]",
                        "encoding":"[parameters('archiveEncodingFormat')]",
                        "intervalInSeconds":"[parameters('archiveTime')]",
                        "sizeLimitInBytes":"[parameters('archiveSize')]",
                        "destination":{
                            "name":"EventHubArchive.AzureBlockBlob",
                            "properties":{
                                "StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
                                "BlobContainer":"[parameters('blobContainerName')]"
                            }
                        } 
                  }

               }

            }
         ]
      }
   ]
```

## <a name="commands-to-run-deployment"></a>Commandes pour exécuter le déploiement
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json
```

## <a name="azure-cli"></a>Interface de ligne de commande Azure
```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json][]
```

[Création de modèles Azure Resource Manager]: ../resource-group-authoring-templates.md
[Modèles de démarrage rapide Azure]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Utilisation d’Azure PowerShell avec Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Utilisation de l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Modèle d’Event Hub et de groupe de consommateurs]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-eventhubs-create-namespace-and-enable-archive/
[Conventions d’affectation de noms des ressources Azure]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
[Modèle d’Event Hub et activer Archive]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-archive



<!--HONumber=Oct16_HO2-->


