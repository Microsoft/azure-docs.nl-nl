---
title: Azure Service Bus-migreren naar Shared Access Signature autorisatie
description: Meer informatie over het migreren van Azure Active Directory-Access Control Service naar Shared Access Signature-autorisatie.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e8cd12ac97020417f9958beded1fd198dd485fff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88064618"
---
# <a name="service-bus---migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Service Bus-migreren van Azure Active Directory Access Control Service naar Shared Access Signature autorisatie

Service Bus toepassingen hebben eerder een keuze nodig bij het gebruik van twee verschillende autorisatie modellen: het [Shared Access Signature (SAS)](service-bus-sas.md) -token model dat rechtstreeks wordt geleverd door service bus, en een federatief model waarin het beheer van autorisatie regels wordt beheerd door de [Azure Active Directory](../active-directory/index.yml) Access Control service (ACS), en tokens die zijn verkregen van ACS worden door gegeven aan service bus om toegang tot de gewenste functies te verlenen.

Het ACS-autorisatie model is al vervangen door [SAS-autorisatie](service-bus-authentication-and-authorization.md) als het voorkeurs model, en alle documentatie, richt lijnen en voor beelden bevatten exclusief SAS vandaag. Bovendien is het niet meer mogelijk om nieuwe Service Bus naam ruimten te maken die zijn gekoppeld aan ACS.

SAS heeft het voor deel dat het niet direct afhankelijk is van een andere service, maar kan rechtstreeks vanuit een client zonder tussen personen worden gebruikt door de client toegang te geven tot de naam en regel sleutel van de SAS-regel. SAS kan ook eenvoudig worden geïntegreerd met een aanpak waarbij een client eerst een verificatie controle bij een andere service moet door geven en vervolgens een token heeft uitgegeven. De laatste benadering is vergelijkbaar met het gebruiks patroon van de ACS, maar maakt het verlenen van toegangs tokens op basis van toepassingsspecifieke voor waarden die lastig zijn om in ACS te zijn.

Voor alle bestaande toepassingen die afhankelijk zijn van ACS, bevelen wij klanten aan om hun toepassingen te migreren om te vertrouwen op SAS.

## <a name="migration-scenarios"></a>Migratiescenario's

ACS en Service Bus zijn geïntegreerd met de gedeelde kennis van een *handtekening sleutel*. De handtekening sleutel wordt gebruikt door een ACS-naam ruimte voor het ondertekenen van autorisatie tokens en wordt gebruikt door Service Bus om te controleren of het token is uitgegeven door de gekoppelde ACS-naam ruimte. De ACS-naam ruimte bevat service-identiteiten en autorisatie regels. De autorisatie regels definiëren welke service-identiteit of welk token dat is uitgegeven door een externe ID-provider, het type toegang tot een deel van de Service Bus naam ruimte grafiek, in de vorm van een treffer voor het langste voor voegsel.

Een ACS-regel kan bijvoorbeeld de **Verzend** claim op het pad `/` naar een service-identiteit verlenen, wat betekent dat een token dat is uitgegeven door ACS op basis van die regel, de client rechten verleent die worden verzonden naar alle entiteiten in de naam ruimte. Als het voor voegsel van het pad is `/abc` , is de identiteit beperkt tot het verzenden van entiteiten die `abc` onder dat voor voegsel worden genoemd. Er wordt van uitgegaan dat lezers van deze migratie richtlijnen al bekend zijn met deze concepten.

De migratie scenario's vallen in drie categorieën:

1.  **Standaard instellingen ongewijzigd**. Sommige klanten gebruiken een [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) -object, waarbij de automatisch gegenereerde **eigenaar** -service-identiteit en de geheime sleutel voor de ACS-naam ruimte worden door gegeven, gekoppeld aan de naam ruimte service bus en geen nieuwe regels toevoegen.

2.  **Aangepaste service-identiteiten met eenvoudige regels**. Sommige klanten voegen nieuwe service-identiteiten toe en verlenen elke nieuwe service-id machtigingen voor **verzenden**, **belui Steren** en **beheren** voor één specifieke entiteit.

3.  **Aangepaste service-identiteiten met complexe regels**. Zeer weinig klanten hebben complexe regel sets waarin extern uitgegeven tokens worden toegewezen aan rechten op relay of waarbij aan één service-identiteit gedifferentieerde rechten voor verschillende naam ruimte paden worden toegewezen via meerdere regels.

Voor hulp bij de migratie van complexe regel sets kunt u contact opnemen met de [ondersteuning van Azure](https://azure.microsoft.com/support/options/). De andere twee scenario's maken eenvoudige migratie mogelijk.

### <a name="unchanged-defaults"></a>Standaard instellingen ongewijzigd

Als uw toepassing de ACS-standaard waarden niet heeft gewijzigd, kunt u alle [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) -gebruik vervangen door een [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) -object en de vooraf geconfigureerde naam ruimte gebruiken **in plaats van** het ACS- **eigenaars** account. Houd er rekening mee dat zelfs met het account van de ACS- **eigenaar** deze configuratie (en nog steeds) niet doorgaans wordt aanbevolen, omdat deze account/regel volledige beheer bevoegdheid biedt voor de naam ruimte, inclusief machtiging voor het verwijderen van entiteiten.

### <a name="simple-rules"></a>Eenvoudige regels

Als voor de toepassing aangepaste service-identiteiten met eenvoudige regels worden gebruikt, is de migratie eenvoudig in het geval waarin een ACS-service-identiteit is gemaakt om toegangs beheer te bieden voor een specifieke wachtrij. Dit scenario is vaak het geval in SaaS-oplossingen waarbij elke wachtrij wordt gebruikt als een brug naar een Tenant site of filiaal, en de service-identiteit voor die specifieke site wordt gemaakt. In dit geval kan de betreffende service-identiteit rechtstreeks op de wachtrij worden gemigreerd naar een Shared Access Signature regel. De naam van de service-identiteit kan de naam van de SAS-regel worden en de Service-id-sleutel kan de SAS-regel sleutel worden. De rechten van de SAS-regel worden vervolgens geconfigureerd die gelijk zijn aan de respectievelijke ACS-regel voor de entiteit.

U kunt deze nieuwe en extra configuratie van SAS in-place maken op een bestaande naam ruimte die federatieve is met ACS en de migratie van ACS wordt vervolgens uitgevoerd met behulp van [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) in plaats van [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). De naam ruimte hoeft niet te worden gekoppeld aan ACS.

### <a name="complex-rules"></a>Complexe regels

SAS-regels zijn niet bedoeld om accounts te zijn, maar ze hebben de naam aanmeldings sleutels die zijn gekoppeld aan rechten. Als zodanig, scenario's waarin de toepassing veel service-identiteiten maakt en toegangs rechten voor verschillende entiteiten verleent, of de volledige naam ruimte, nog steeds een intermedia voor token afgifte hebben. [Neem contact op met de ondersteuning](https://azure.microsoft.com/support/options/)voor hulp bij het verkrijgen van een dergelijke intermediair.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Service Bus-verificatie:

* [Vereenvoudigde Service Bus-verificatie en -autorisatie](service-bus-authentication-and-authorization.md)
* [Verificatie Service Bus met hand tekeningen voor gedeelde toegang](service-bus-sas.md)