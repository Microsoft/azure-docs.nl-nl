---
title: Gids voor probleemoplossing voor Azure Service Bus | Microsoft Docs
description: Meer informatie over tips voor het oplossen van problemen en aanbevelingen voor een aantal problemen die u kunt zien wanneer u Azure Service Bus.
ms.topic: article
ms.date: 03/03/2021
ms.openlocfilehash: 27249d7e016ea8aee0552bbbf1687647760d4b6f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786563"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Gids voor probleemoplossing voor Azure Service Bus
Dit artikel bevat tips voor het oplossen van problemen en aanbevelingen voor enkele problemen die u kunt zien wanneer u Azure Service Bus. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Connectiviteits-, certificaat- of time-outproblemen
De volgende stappen kunnen u helpen bij het oplossen van problemen met connectiviteit/certificaten/time-outs voor alle services onder *.servicebus.windows.net. 

- Blader naar of [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/` . Het helpt bij het controleren of u IP-filtering of problemen met een virtueel netwerk of een certificaatketen hebt, wat gebruikelijk is bij het gebruik van de Java-SDK.

    Een voorbeeld van een geslaagd bericht:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Een voorbeeld van een foutbericht:

    ```xml
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Voer de volgende opdracht uit om te controleren of een poort op de firewall is geblokkeerd. Gebruikte poorten zijn 443 (HTTPS), 5671 (AMQP) en 9354 (Net Messaging/SBMP). Afhankelijk van de bibliotheek die u gebruikt, worden ook andere poorten gebruikt. Hier is de voorbeeldopdracht die controleert of de poort 5671 is geblokkeerd. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    In Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Wanneer er onregelmatige verbindingsproblemen zijn, voer dan de volgende opdracht uit om te controleren of er pakketten zijn uitgevallen. Met deze opdracht wordt geprobeerd om elke 1 seconde 25 verschillende TCP-verbindingen met de service tot stand te brengen. Vervolgens kunt u controleren hoeveel van deze zijn geslaagd/mislukt en ook de latentie van de TCP-verbinding bekijken. U kunt het hulpprogramma `psping` hier [downloaden.](/sysinternals/downloads/psping)

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    U kunt gelijkwaardige opdrachten gebruiken als u andere hulpprogramma's gebruikt, zoals `tnc` `ping` , , en meer. 
- Verkrijg een netwerk-trace als de vorige stappen niet helpen en analyseer deze met hulpprogramma's zoals [Wireshark](https://www.wireshark.org/). Neem [Microsoft-ondersteuning](https://support.microsoft.com/) contact op met de klant. 
- Zie What IP addresses do I [need to add to allowlist (Welke IP-adressen](service-bus-faq.yml#what-ip-addresses-do-i-need-to-add-to-allow-list-)moet ik toevoegen aan de allowlist) om de juiste IP-adressen te vinden die u wilt toevoegen aan de allowlist voor uw verbindingen. 


## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problemen met service-upgrades/opnieuw opstarten

### <a name="symptoms"></a>Symptomen
- Aanvragen kunnen tijdelijk worden beperkt.
- Mogelijk is er een daling in binnenkomende berichten/aanvragen.
- Het logboekbestand kan foutberichten bevatten.
- De verbinding van de toepassingen met de service kan een paar seconden worden verbroken.

### <a name="cause"></a>Oorzaak
Back-en-service-upgrades en opnieuw opstarten kunnen deze problemen in uw toepassingen veroorzaken.

### <a name="resolution"></a>Oplossing
Als de toepassingscode gebruikmaakt van SDK, is het beleid voor opnieuw proberen al ingebouwd en actief. De toepassing maakt opnieuw verbinding zonder aanzienlijke gevolgen voor de toepassing/werkstroom.

## <a name="unauthorized-access-send-claims-are-required"></a>Niet-geautoriseerde toegang: Claims verzenden is vereist

### <a name="symptoms"></a>Symptomen 
U ziet deze fout mogelijk wanneer u toegang probeert te krijgen tot een Service Bus-onderwerp vanaf Visual Studio op een on-premises computer met behulp van een door de gebruiker toegewezen beheerde identiteit met machtigingen voor verzenden.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>Oorzaak
De identiteit heeft geen machtigingen voor toegang tot het Service Bus onderwerp. 

### <a name="resolution"></a>Oplossing
Installeer de bibliotheek [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) om deze fout op te lossen.  Zie Lokale [ontwikkelingsverificatie voor meer informatie.](/dotnet/api/overview/azure/service-to-service-authentication#local-development-authentication) 

Zie Authenticate a managed identity with Azure Active Directory to access Azure Service Bus resources (Een beheerde identiteit verifiëren met Azure Active Directory toegang tot [Azure Service Bus-resources) voor meer informatie over](service-bus-managed-service-identity.md)het toewijzen van machtigingen aan rollen.

## <a name="service-bus-exception-put-token-failed"></a>Service Bus uitzondering: Put-token is mislukt

### <a name="symptoms"></a>Symptomen
Wanneer u meer dan 1000 berichten probeert te verzenden met dezelfde Service Bus verbinding, ontvangt u het volgende foutbericht: 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>Oorzaak
Er geldt een limiet voor het aantal tokens dat wordt gebruikt voor het verzenden en ontvangen van berichten met behulp van één verbinding met een Service Bus naamruimte. Het is 1000. 

### <a name="resolution"></a>Oplossing
Open een nieuwe verbinding met de Service Bus-naamruimte om meer berichten te verzenden.

## <a name="adding-virtual-network-rule-using-powershell-fails"></a>Het toevoegen van een regel voor een virtueel netwerk met Behulp van PowerShell mislukt

### <a name="symptoms"></a>Symptomen
U hebt twee subnetten van één virtueel netwerk geconfigureerd in een regel voor een virtueel netwerk. Wanneer u één subnet probeert te verwijderen met de cmdlet [Remove-AzServiceBusVirtualNetworkRule,](/powershell/module/az.servicebus/remove-azservicebusvirtualnetworkrule) wordt het subnet niet verwijderd uit de regel voor het virtuele netwerk. 

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName $resourceGroupName -Namespace $serviceBusName -SubnetId $subnetId
```

### <a name="cause"></a>Oorzaak
De Azure Resource Manager-id die u hebt opgegeven voor het subnet, is mogelijk ongeldig. Dit kan gebeuren wanneer het virtuele netwerk zich in een andere resourcegroep dan de resourcegroep met de Service Bus-naamruimte. Als u de resourcegroep van het virtuele netwerk niet expliciet opgeeft, wordt met de CLI-opdracht de Azure Resource Manager-id samengesteld met behulp van de resourcegroep van de Service Bus-naamruimte. Het subnet kan dus niet worden verwijderd uit de netwerkregel. 

### <a name="resolution"></a>Oplossing
Geef de volledige Azure Resource Manager id op van het subnet dat de naam bevat van de resourcegroep die het virtuele netwerk bevat. Bijvoorbeeld:

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName myRG -Namespace myNamespace -SubnetId "/subscriptions/SubscriptionId/resourcegroups/ResourceGroup/myOtherRG/providers/Microsoft.Network/virtualNetworks/myVNet/subnets/mySubnet"
```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen: 

- [Azure Resource Manager uitzonderingen .](service-bus-resource-manager-exceptions.md) Er worden uitzonderingen weergegeven die worden gegenereerd bij interactie met Azure Service Bus met Azure Resource Manager (via sjablonen of directe aanroepen).
- [Berichten-uitzonderingen](service-bus-messaging-exceptions.md). Het bevat een lijst met uitzonderingen die worden gegenereerd door .NET Framework voor Azure Service Bus.