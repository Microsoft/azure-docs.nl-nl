---
title: Gids voor probleem oplossing voor Azure Service Bus | Microsoft Docs
description: Meer informatie over tips en aanbevelingen voor het oplossen van problemen die kunnen optreden bij het gebruik van Azure Service Bus.
ms.topic: article
ms.date: 03/03/2021
ms.openlocfilehash: b44587747a59acb3c0124c0a76b63de68d6d8ae7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105031287"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Gids voor probleem oplossing voor Azure Service Bus
In dit artikel vindt u tips en aanbevelingen voor het oplossen van problemen die kunnen optreden bij het gebruik van Azure Service Bus. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemen met connectiviteit, certificaten of time-outs
De volgende stappen kunnen u helpen bij het oplossen van problemen met connectiviteit/certificaat/time-out voor alle services onder *. servicebus.windows.net. 

- Blader naar of [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/` . Het helpt te controleren of er problemen zijn met de IP-filtering of het virtuele netwerk of de certificaat keten, die gemeen schappelijk zijn bij het gebruik van Java SDK.

    Een voor beeld van een geslaagd bericht:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Een voor beeld van een fout bericht:

    ```xml
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Voer de volgende opdracht uit om te controleren of een poort is geblokkeerd op de firewall. De gebruikte poorten zijn 443 (HTTPS), 5671 (AMQP) en 9354 (net Messa ging/SBMP). Afhankelijk van de bibliotheek die u gebruikt, worden ook andere poorten gebruikt. Hier volgt een voor beeld van de opdracht waarmee wordt gecontroleerd of de 5671-poort is geblokkeerd. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Op Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Als er onregelmatige verbindings problemen zijn, voert u de volgende opdracht uit om te controleren of er verloren pakketten zijn. Met deze opdracht wordt geprobeerd 25 verschillende TCP-verbindingen elke 1 seconde te maken met de service. Vervolgens kunt u controleren of het aantal geslaagde/mislukte en ook TCP-verbindings latentie wordt weer geven. U kunt het `psping` hulp programma [hier](/sysinternals/downloads/psping)downloaden.

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    U kunt gelijkwaardige opdrachten gebruiken als u andere hulp middelen gebruikt, zoals `tnc` , `ping` , enzovoort. 
- Verkrijg een netwerk tracering als de vorige stappen niet helpen en analyseren met behulp van hulpprogram ma's zoals [wireshark](https://www.wireshark.org/). Neem zo nodig contact op met [Microsoft ondersteuning](https://support.microsoft.com/) . 
- Als u de juiste IP-adressen wilt vinden die u wilt toevoegen aan allowlist voor uw verbindingen, raadpleegt u de [IP-adressen die ik moet toevoegen aan allowlist](service-bus-faq.md#what-ip-addresses-do-i-need-to-add-to-allow-list). 


## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problemen die zich kunnen voordoen met Service-upgrades/opnieuw opstarten

### <a name="symptoms"></a>Symptomen
- Aanvragen kunnen tijdelijk worden beperkt.
- Er is mogelijk een neerzetten in inkomende berichten/aanvragen.
- Het logboek bestand kan fout berichten bevatten.
- De verbinding van de toepassingen kan enkele seconden worden verbroken met de service.

### <a name="cause"></a>Oorzaak
Het upgraden van de back-end-service en opnieuw starten kan deze problemen in uw toepassingen veroorzaken.

### <a name="resolution"></a>Oplossing
Als de toepassings code SDK gebruikt, is het beleid voor opnieuw proberen al ingebouwd en actief. De toepassing zal opnieuw verbinding maken zonder belang rijke gevolgen voor de toepassing/werk stroom.

## <a name="unauthorized-access-send-claims-are-required"></a>Onbevoegde toegang: verzend claims zijn vereist

### <a name="symptoms"></a>Symptomen 
Deze fout kan optreden wanneer u probeert een Service Bus onderwerp te openen vanuit Visual Studio op een on-premises computer met behulp van een door de gebruiker toegewezen beheerde identiteit met verzend machtigingen.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>Oorzaak
De identiteit heeft geen machtigingen voor toegang tot het onderwerp Service Bus. 

### <a name="resolution"></a>Oplossing
Om deze fout op te lossen, installeert u de bibliotheek [micro soft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) .  Zie [Local Development Authentication](/dotnet/api/overview/azure/service-to-service-authentication#local-development-authentication)(Engelstalig) voor meer informatie. 

Zie [een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot Azure service bus resources](service-bus-managed-service-identity.md)voor meer informatie over het toewijzen van machtigingen aan rollen.

## <a name="service-bus-exception-put-token-failed"></a>Service Bus-uitzonde ring: put-token mislukt

### <a name="symptoms"></a>Symptomen
Wanneer u probeert meer dan 1000 berichten te verzenden met behulp van dezelfde Service Bus verbinding, wordt het volgende fout bericht weer gegeven: 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>Oorzaak
Er is een limiet voor het aantal tokens dat wordt gebruikt voor het verzenden en ontvangen van berichten met één verbinding met een Service Bus naam ruimte. Het is 1000. 

### <a name="resolution"></a>Oplossing
Open een nieuwe verbinding met de Service Bus naam ruimte om meer berichten te verzenden.

## <a name="adding-virtual-network-rule-using-powershell-fails"></a>Het toevoegen van een regel voor het virtuele netwerk met Power shell mislukt

### <a name="symptoms"></a>Symptomen
U hebt twee subnetten geconfigureerd vanaf één virtueel netwerk in een regel voor een virtueel netwerk. Wanneer u probeert één subnet te verwijderen met de cmdlet [Remove-AzServiceBusVirtualNetworkRule](/powershell/module/az.servicebus/remove-azservicebusvirtualnetworkrule) , wordt het subnet niet verwijderd uit de regel van het virtuele netwerk. 

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName $resourceGroupName -Namespace $serviceBusName -SubnetId $subnetId
```

### <a name="cause"></a>Oorzaak
De Azure Resource Manager-ID die u voor het subnet hebt opgegeven, is mogelijk ongeldig. Dit kan gebeuren wanneer het virtuele netwerk zich in een andere resource groep bevindt dan die waarvan de Service Bus naam ruimte is. Als u de resource groep van het virtuele netwerk niet expliciet opgeeft, bouwt de CLI-opdracht de Azure Resource Manager-ID met behulp van de resource groep van de Service Bus naam ruimte. Hierdoor kan het subnet niet worden verwijderd uit de netwerk regel. 

### <a name="resolution"></a>Oplossing
Geef de volledige Azure Resource Manager ID op van het subnet met de naam van de resource groep die het virtuele netwerk bevat. Bijvoorbeeld:

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName myRG -Namespace myNamespace -SubnetId "/subscriptions/SubscriptionId/resourcegroups/ResourceGroup/myOtherRG/providers/Microsoft.Network/virtualNetworks/myVNet/subnets/mySubnet"
```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen: 

- [Uitzonde ringen Azure Resource Manager](service-bus-resource-manager-exceptions.md). De lijst met uitzonde ringen die zijn gegenereerd bij het werken met Azure Service Bus met behulp van Azure Resource Manager (via sjablonen of directe aanroepen).
- [Messa ging-uitzonde ringen](service-bus-messaging-exceptions.md). Het bevat een lijst met uitzonde ringen die zijn gegenereerd door .NET Framework voor Azure Service Bus.