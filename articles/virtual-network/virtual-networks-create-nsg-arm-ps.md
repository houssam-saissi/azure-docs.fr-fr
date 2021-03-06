---
title: Création de groupes de sécurité réseau dans Azure Resource Manager à l’aide de PowerShell | Microsoft Docs
description: Découvrez comment créer et déployer des groupes de sécurité réseau dans Azure Resource Manager à l’aide de PowerShell
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: jdial

---
# <a name="how-to-create-nsgs-in-resource-manager-by-using-powershell"></a>Création de groupes de sécurité réseau dans Resource Manager à l’aide de PowerShell
[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Cet article traite du modèle de déploiement de Resource Manager. Vous pouvez également [créer des groupes de sécurité réseau dans le modèle de déploiement classique](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Les exemples de commandes PowerShell ci-dessous supposent qu’un environnement simple a déjà été créé conformément au scénario décrit ci-dessous. Si vous souhaitez exécuter les commandes telles qu’elles sont présentées dans ce document, commencez par créer l’environnement de test en déployant [ce modèle](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), cliquez sur **Déployer dans Azure**, remplacez les valeurs des paramètres par défaut si nécessaire, puis suivez les instructions dans le portail.

## <a name="how-to-create-the-nsg-for-the-front-end-subnet"></a>Création du groupe de sécurité réseau pour le sous-réseau frontal
Pour créer un groupe de sécurité réseau nommé *NSG-FrontEnd* selon le scénario ci-dessus, suivez les étapes ci-dessous :

[!INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Si vous n’avez jamais utilisé Azure PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) et suivez les instructions jusqu’à la fin pour vous connecter à Azure et sélectionner votre abonnement.
2. Créer une règle de sécurité autorisant l'accès à partir d'Internet vers le port 3389.
   
        $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP" `
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
            -SourceAddressPrefix Internet -SourcePortRange * `
            -DestinationAddressPrefix * -DestinationPortRange 3389
3. Créer une règle de sécurité autorisant l'accès à partir d'Internet vers le port 80.
   
        $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP" `
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
            -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * `
            -DestinationPortRange 80
4. Ajoutez les règles créées précédemment à un nouveau groupe de sécurité réseau nommé **NSG-FrontEnd**.
   
        $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus `
        -Name "NSG-FrontEnd" -SecurityRules $rule1,$rule2
5. Vérifier les règles créées dans le NSG.
   
        $nsg
   
    La sortie affiche uniquement les règles de sécurité :
   
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule",
                                   "Description": "Allow RDP",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "3389",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 100,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "web-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule",
                                   "Description": "Allow HTTP",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "80",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 101,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
6. Associez le groupe de sécurité réseau créé précédemment au sous-réseau *FrontEnd* .
   
                    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
                    Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
                        -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $nsg
   
                Output showing only the *FrontEnd* subnet settings, notice the value for the **NetworkSecurityGroup** property:
   
                    Subnets           : [
                                          {
                                            "Name": "FrontEnd",
                                            "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                            "AddressPrefix": "192.168.1.0/24",
                                            "IpConfigurations": [
                                              {
                                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                              },
                                              {
                                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                              }
                                            ],
                                            "NetworkSecurityGroup": {
                                              "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                            },
                                            "RouteTable": null,
                                            "ProvisioningState": "Succeeded"
                                          }
   
   > [!WARNING]
   > La sortie de la commande ci-dessus affiche le contenu de l’objet de configuration de réseau virtuel, qui existe uniquement sur l’ordinateur sur lequel vous exécutez PowerShell. Vous devez exécuter l’applet de commande `Set-AzureRmVirtualNetwork` pour enregistrer ces paramètres dans Azure.
   > 
   > 
7. Enregistrer les nouveaux paramètres de réseau virtuel dans Azure.
   
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
   
    La sortie affiche uniquement la partie NSG :
   
        "NetworkSecurityGroup": {
          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
        }

## <a name="how-to-create-the-nsg-for-the-back-end-subnet"></a>Création du groupe de sécurité réseau pour le sous-réseau principal
Pour créer un groupe de sécurité réseau nommé *NSG-BackEnd* selon le scénario ci-dessus, suivez les étapes ci-dessous :

1. Créer une règle de sécurité permettant l’accès depuis le sous-réseau frontal vers le port 1433 (port par défaut utilisé par SQL Server).
   
        $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name frontend-rule -Description "Allow FE subnet" `
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
            -SourceAddressPrefix 192.168.1.0/24 -SourcePortRange * `
            -DestinationAddressPrefix * -DestinationPortRange 1433
2. Créer une règle de sécurité bloquant l'accès à Internet.
   
        $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name web-rule -Description "Block Internet" `
            -Access Deny -Protocol * -Direction Outbound -Priority 200 `
            -SourceAddressPrefix * -SourcePortRange * `
            -DestinationAddressPrefix Internet -DestinationPortRange *
3. Ajoutez les règles créées précédemment à un nouveau groupe de sécurité réseau nommé **NSG-BackEnd**.
   
        $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus -Name "NSG-BackEnd" `
            -SecurityRules $rule1,$rule2
4. Associez le groupe de sécurité réseau créé précédemment au sous-réseau *BackEnd* .
   
        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd
            -AddressPrefix 192.168.2.0/24 -NetworkSecurityGroup $nsg
   
    La sortie indique seulement les paramètres du sous-réseau *BackEnd*. Notez la valeur de la propriété **NetworkSecurityGroup** :
   
        Subnets           : [
                      {
                        "Name": "BackEnd",
                        "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                        "AddressPrefix": "192.168.2.0/24",
                        "IpConfigurations": [...],
                        "NetworkSecurityGroup": {
                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "RouteTable": null,
                        "ProvisioningState": "Succeeded"
                      }
5. Enregistrer les nouveaux paramètres de réseau virtuel dans Azure.
   
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## <a name="how-to-remove-an-nsg"></a>Supprimer un groupe de sécurité réseau
Pour supprimer un groupe de sécurité réseau existant, appelé *NSG-Frontend* dans ce cas, suivez la procédure ci-dessous :

Exécutez la commande **Remove-AzureRmNetworkSecurityGroup** ci-dessous et veillez à inclure le groupe de ressources dans lequel se trouve le groupe de sécurité réseau.

            Remove-AzureRmNetworkSecurityGroup -Name "NSG-FrontEnd" -ResourceGroupName "TestRG"



<!--HONumber=Oct16_HO2-->


