---
title: 'Fout oplossen: de Azure Functions-runtime is onbereikbaar'
description: Meer informatie over het oplossen van problemen met een ongeldig opslag account.
ms.topic: article
ms.date: 09/05/2018
ms.openlocfilehash: a62001cedd695badc72eb76c93ea9c3cb4507403
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499625"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Fout oplossen: ' Azure Functions-runtime is onbereikbaar '

Dit artikel helpt u bij het oplossen van de volgende fout teken reeks die wordt weer gegeven in de Azure Portal:

> "Fout: Azure Functions-runtime is onbereikbaar. Klik hier voor meer informatie over de opslag configuratie.

Dit probleem treedt op wanneer de functions-runtime niet kan worden gestart. De meest voorkomende reden hiervoor is dat de functie-app de toegang tot het opslag account heeft verloren. Zie [vereisten voor opslag accounts](storage-considerations.md#storage-account-requirements)voor meer informatie.

De rest van dit artikel helpt u bij het oplossen van specifieke oorzaken van deze fout, inclusief het identificeren en oplossen van elke case.

## <a name="storage-account-was-deleted"></a>Het opslag account is verwijderd

Voor elke functie-app moet een opslag account worden gebruikt. Als dat account wordt verwijderd, werken uw functies niet.

Zoek eerst de naam van uw opslag account op in de instellingen van uw toepassing. Ofwel `AzureWebJobsStorage` of `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` bevat de naam van uw opslag account als onderdeel van een Connection String. Zie voor meer informatie [app-instellingen naslag informatie voor Azure functions](./functions-app-settings.md#azurewebjobsstorage).

Zoek uw opslag account in de Azure Portal om te zien of het nog bestaat. Als deze is verwijderd, maakt u het opslag account opnieuw en vervangt u de verbindings reeksen voor de opslag. De functie code gaat verloren en u moet deze opnieuw implementeren.

## <a name="storage-account-application-settings-were-deleted"></a>De toepassings instellingen voor het opslag account zijn verwijderd

Als u in de vorige stap geen opslag account connection string kunt vinden, is deze waarschijnlijk verwijderd of overschreven. Het verwijderen van toepassings instellingen gebeurt meestal wanneer u implementatie sleuven of Azure Resource Manager scripts gebruikt om toepassings instellingen in te stellen.

### <a name="required-application-settings"></a>Vereiste toepassings instellingen

* Vereist:
    * [`AzureWebJobsStorage`](./functions-app-settings.md#azurewebjobsstorage)
* Vereist voor de functies van Premium-abonnementen:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](./functions-app-settings.md)
    * [`WEBSITE_CONTENTSHARE`](./functions-app-settings.md)

Zie voor meer informatie [app-instellingen naslag informatie voor Azure functions](./functions-app-settings.md).

### <a name="guidance"></a>Hulp

* Controleer geen **sleuf instelling** voor een van deze instellingen. Als u implementatie sleuven verwisselt, wordt de functie-app onderbroken.
* Wijzig deze instellingen niet als onderdeel van automatische implementaties.
* Deze instellingen moeten worden ingevoerd en geldig zijn tijdens het maken. Een geautomatiseerde implementatie die deze instellingen niet bevat, resulteert in een functie-app die niet wordt uitgevoerd, zelfs niet als de instellingen later worden toegevoegd.

## <a name="storage-account-credentials-are-invalid"></a>De referenties van het opslag account zijn ongeldig

De eerder beschreven verbindings reeksen voor opslag accounts moeten worden bijgewerkt als u opslag sleutels opnieuw genereert. Zie [een Azure Storage-account maken](../storage/common/storage-account-create.md)voor meer informatie over opslag sleutel beheer.

## <a name="storage-account-is-inaccessible"></a>Het opslag account is niet toegankelijk

De functie-app moet toegang hebben tot het opslag account. Veelvoorkomende problemen met het blok keren van toegang tot een opslag account met een functie-app:

* De functie-app wordt geïmplementeerd op uw App Service Environment (ASE) zonder de juiste netwerk regels om verkeer toe te staan van en naar het opslag account.

* De firewall voor het opslag account is ingeschakeld en is niet geconfigureerd om verkeer van en naar functies toe te staan. Raadpleeg [Firewalls en virtuele netwerken voor Azure Storage configureren](../storage/common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie.

## <a name="daily-execution-quota-is-full"></a>Het dagelijkse uitvoerings quotum is vol

Als u een dagelijks uitvoerings quotum hebt geconfigureerd, is de functie-app tijdelijk uitgeschakeld, waardoor veel van de besturings elementen van de portal niet meer beschikbaar zijn. 

Als u het quotum wilt controleren in de [Azure Portal](https://portal.azure.com), selecteert u **platform functies**  >  **functie-app instellingen** in uw functie-app. Als u de **dagelijkse gebruiks quota** hebt ingesteld, wordt het volgende bericht weer gegeven:

  > "De functie-app heeft het quotum voor dagelijks gebruik bereikt en is gestopt tot de volgende periode van 24 uur."

U kunt dit probleem oplossen door het dagelijkse quotum te verwijderen of te verhogen en vervolgens uw app opnieuw te starten. Anders wordt de uitvoering van uw app geblokkeerd tot de volgende dag.

## <a name="app-is-behind-a-firewall"></a>App bevindt zich achter een firewall

De functie-app kan om een van de volgende redenen onbereikbaar zijn:

* Uw functie-app wordt gehost in een [app service Environment met interne taak verdeling](../app-service/environment/create-ilb-ase.md) en is geconfigureerd om inkomend Internet verkeer te blok keren.

* Uw functie-app heeft [binnenkomende IP-beperkingen](functions-networking-options.md#inbound-access-restrictions) die zijn geconfigureerd om Internet toegang te blok keren. 

De Azure Portal maakt rechtstreeks aan de actieve app aanroepen om de lijst met functies op te halen en maakt HTTP-aanroepen naar het kudu-eind punt. Instellingen op platform niveau onder het tabblad **platform functies** zijn nog steeds beschikbaar.

Uw ASE-configuratie controleren:
1. Ga naar de netwerk beveiligings groep (NSG) van het subnet waar de ASE zich bevindt.
1. Valideer de regels voor binnenkomende verbindingen om verkeer toe te staan dat afkomstig is van het open bare IP-adres van de computer waarop u toegang tot de toepassing hebt. 
   
U kunt de portal ook gebruiken vanaf een computer die is verbonden met het virtuele netwerk waarop uw app wordt uitgevoerd of op een virtuele machine die wordt uitgevoerd in uw virtuele netwerk. 

Zie de sectie netwerk beveiligings groepen van [netwerk overwegingen voor een app service Environment](../app-service/environment/network-info.md#network-security-groups)voor meer informatie over de configuratie van de binnenkomende regel.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bewaken van uw functie-apps:

> [!div class="nextstepaction"]
> [Azure Functions controleren](functions-monitoring.md)
