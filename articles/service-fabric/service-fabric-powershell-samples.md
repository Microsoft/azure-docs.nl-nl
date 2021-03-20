---
title: Voorbeelden van Azure PowerShell - Service Fabric
description: Meer informatie over het maken en beheren van Azure-Service Fabric clusters, -apps en -services met behulp van PowerShell.
ms.topic: sample
ms.date: 11/29/2018
ms.custom: mvc
ms.openlocfilehash: 4b85fd604eb27f0963af882b41e823d800005dda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86187095"
---
# <a name="azure-service-fabric-powershell-samples"></a>Voorbeelden van Azure Service Fabric PowerShell

De volgende tabel bevat koppelingen naar voorbeelden van PowerShell-scripts voor het maken en beheren van Service Fabric-clusters, -toepassingen en -services.

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| Script | Beschrijving |
|-|-|
| **Cluster maken** ||
| [Een cluster maken (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Hiermee wordt een Azure Service Fabric-cluster gemaakt. |
| **Cluster, knooppunten en infrastructuur beheren** ||
| [Een toepassingscertificaat toevoegen](./scripts/service-fabric-powershell-add-application-certificate.md)| Hiermee maakt u een x509-certificaat voor Key Vault en implementeert u dit voor een virtuele-machineschaalset in uw cluster. |
| [Het RDP-poortbereik bijwerken op cluster-VM's](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Hiermee wijzigt u het RDP-poortbereik op clusterknooppunt-VM's in een geïmplementeerd cluster.|
| [De gebruiker met beheerdersrechten en het wachtwoord bijwerken voor clusterknooppunt-VM's](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Hiermee worden de gebruiker met beheerdersrechten en het wachtwoord voor clusterknooppunt-VM's bijgewerkt. |
| [Een poort in de load balancer openen](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Open een toepassingspoort in de Azure-load balancer om inkomend verkeer via een specifieke poort toe te staan. |
| [Een regel voor inkomend verkeer voor een netwerkbeveiligingsgroep maken](./scripts/service-fabric-powershell-add-nsg-rule.md) | Maak een regel voor inkomend verkeer voor een netwerkbeveiligingsgroep om inkomend verkeer naar het cluster op een specifieke poort toe te staan. |
| **Toepassingen beheren** ||
| [Een app implementeren](./scripts/service-fabric-powershell-deploy-application.md)| Hiermee wordt een toepassing geïmplementeerd in een cluster.|
| [Een upgrade van een app uitvoeren](./scripts/service-fabric-powershell-upgrade-application.md)| Hiermee voert u een upgrade uit van een app.|
| [Een app verwijderen](./scripts/service-fabric-powershell-remove-application.md)| Hiermee verwijdert u een app uit een cluster.|
