---
title: Lier un réseau virtuel à un circuit ExpressRoute avec PowerShell | Microsoft Docs
description: Ce document explique comment lier des réseaux virtuels à des circuits ExpressRoute à l’aide du modèle de déploiement Resource Manager et de PowerShell.
services: expressroute
documentationcenter: na
author: ganesr
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: ganesr

---
# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Lier un réseau virtuel à un circuit ExpressRoute
> [!div class="op_single_selector"]
> * [Portail Azure - Resource Manager](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell - Resource Manager](expressroute-howto-linkvnet-arm.md)
> * [PowerShell - Classique](expressroute-howto-linkvnet-classic.md)
> 
> 

Cet article vous aide à lier des réseaux virtuels à des circuits Azure ExpressRoute en utilisant le modèle de déploiement Resource Manager et PowerShell. Les réseaux virtuels peuvent appartenir au même abonnement ou faire partie d’un autre abonnement.

**À propos des modèles de déploiement Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Conditions préalables à la configuration
* Vous devez utiliser la dernière version des modules Azure PowerShell (au moins la version 1.0). Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) .
* Avant de commencer la configuration, vous devez examiner les [conditions préalables](expressroute-prerequisites.md), la [configuration requise pour le routage](expressroute-routing.md) et les [flux de travail](expressroute-workflows.md).
* Vous devez disposer d’un circuit ExpressRoute actif. 
  * Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et faites-le activer par votre fournisseur de service de connectivité. 
  * Vérifiez que l’homologation privée Azure est configurée pour votre circuit. Pour obtenir des instructions de routage, consultez l'article sur la [configuration du routage](expressroute-howto-routing-arm.md) . 
  * Vérifiez que l’homologation privée Azure est être configurée, et que l’homologation BGP entre votre réseau et Microsoft est être opérationnelle pour pouvoir activer la connectivité de bout en bout.
  * Vérifiez qu’un réseau virtuel et une passerelle de réseau virtuel ont été créés et entièrement approvisionnés. Suivez les instructions pour créer une [passerelle VPN](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), mais assurez-vous d’utiliser `-GatewayType ExpressRoute`.

Vous pouvez lier jusqu’à 10 réseaux virtuels à un circuit ExpressRoute standard. Tous les réseaux virtuels doivent figurer dans la même région géopolitique lors de l’utilisation d’un circuit ExpressRoute standard. 

Vous pouvez lier des réseaux virtuels à l'extérieur de la zone géopolitique du circuit ExpressRoute ou lier un plus grand nombre de réseaux virtuels à votre circuit ExpressRoute si vous avez activé le module complémentaire Premium d’ExpressRoute. Pour plus d’informations sur le module complémentaire Premium, consultez le [FAQ](expressroute-faqs.md) .

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Connecter un réseau virtuel du même abonnement à un circuit
Vous pouvez connecter une passerelle de réseau virtuel à un circuit ExpressRoute à l’aide de l’applet de commande suivante. Vérifiez que la passerelle de réseau virtuel est créée et prête pour la liaison avant d’exécuter l’applet de commande.

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Connecter un réseau virtuel d’un autre abonnement à un circuit
Vous pouvez partager un circuit ExpressRoute entre plusieurs abonnements. La figure suivante montre un schéma simple sur le fonctionnement du partage de circuits ExpressRoute entre plusieurs abonnements.

Chacun des petits clouds dans le cloud principal est utilisé pour représenter les abonnements appartenant à différents services au sein d’une organisation. Chacun des services au sein de l’organisation peut utiliser son propre abonnement pour déployer ses services, mais ils peuvent partager un même circuit ExpressRoute pour se connecter à votre réseau local. Un seul service (dans cet exemple, le service informatique) peut posséder le circuit ExpressRoute. D’autres abonnements au sein de l’organisation peuvent utiliser le circuit ExpressRoute.

> [!NOTE]
> Les frais de connectivité et de bande passante pour le circuit dédié s’appliquent au propriétaire du circuit ExpressRoute. Tous les réseaux virtuels partagent la même bande passante.
> 
> 

![Connectivité entre abonnements](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administration
Le *propriétaire du circuit* est l’utilisateur avec pouvoir autorisé de la ressource de circuit ExpressRoute. Le propriétaire du circuit peut créer des autorisations utilisables par *les utilisateurs du circuit*. *Les utilisateurs du circuit* sont les propriétaires des passerelles de réseau virtuel (qui ne figurent pas dans le même abonnement que le circuit ExpressRoute). *Les utilisateurs du circuit* peuvent échanger des autorisations (une seule autorisation par réseau virtuel).

Le *propriétaire du circuit* a le pouvoir de modifier et de révoquer les autorisations à tout moment. La révocation d’une autorisation entraîne la suppression de toutes les connexions de l’abonnement dont l’accès a été révoqué.

### <a name="circuit-owner-operations"></a>Opérations du propriétaire du circuit
#### <a name="creating-an-authorization"></a>Création d’une autorisation
Le propriétaire du circuit crée une autorisation. Cela entraîne la création d'une clé d'autorisation qui peut être utilisée par un utilisateur du circuit pour se connecter à ses passerelles de réseau virtuel vers un circuit ExpressRoute. Une autorisation n’est valide que pour une seule connexion.

L’extrait de code d’applet de commande ci-dessous montre comment créer une autorisation :

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

        $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"


La réponse contient la clé d’autorisation et le statut :

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



#### <a name="reviewing-authorizations"></a>Vérification des autorisations
Le propriétaire du circuit peut vérifier toutes les autorisations émises sur un circuit donné en exécutant l’applet de commande suivante :

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit


#### <a name="adding-authorizations"></a>Ajout d'autorisations
Le propriétaire du circuit peut ajouter des autorisations à l’aide de l’applet de commande suivante :

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit


#### <a name="deleting-authorizations"></a>Suppression des autorisations
Le propriétaire du circuit peut révoquer/supprimer les autorisations accordées à l’utilisateur en exécutant l’applet de commande suivante :

    Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit    

### <a name="circuit-user-operations"></a>Opérations de l’utilisateur du circuit
L'utilisateur du circuit a besoin de l'ID de l'homologue et une clé d'autorisation du propriétaire du circuit. La clé d'autorisation est un GUID.

L’ID de l’homologue peut être consulté avec la commande suivante.

    Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

#### <a name="redeeming-connection-authorizations"></a>Utilisation des autorisations de connexion
L’utilisateur du circuit peut exécuter l’applet de commande suivante pour échanger une autorisation de lien :

    $id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"  
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### <a name="releasing-connection-authorizations"></a>Libération des autorisations de connexion
Vous pouvez libérer une autorisation en supprimant la connexion qui lie le circuit ExpressRoute et le réseau virtuel.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations sur ExpressRoute, consultez le [FAQ sur ExpressRoute](expressroute-faqs.md).

<!--HONumber=Oct16_HO2-->


