---
title: Veelgestelde vragen over Azure-app configuratie
description: Lees de antwoorden op veelgestelde vragen over Azure-app configuratie, zoals hoe deze verschillen van Azure Key Vault.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alkemper
ms.openlocfilehash: 4e19574e5848d1ee86d13aa02a9cf583b92eff02
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929562"
---
# <a name="azure-app-configuration-faq"></a>Veelgestelde vragen over Azure-app configuratie

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure-app configuratie.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Hoe verschilt de app-configuratie van Azure Key Vault?

Met app-configuratie kunnen ontwikkel aars toepassings instellingen beheren en de beschik baarheid van functies bepalen. Het doel is om veel van de taken van het werken met complexe configuratie gegevens te vereenvoudigen.

App-configuratie ondersteunt:

- Hiërarchische naamruimten
- Labels
- Uitgebreide query's
- Bulksgewijs ophalen
- Gespecialiseerde beheer bewerkingen
- Een gebruikers interface voor het beheer van functies

App-configuratie complementen Key Vault en de twee moeten naast elkaar worden gebruikt in de meeste toepassings implementaties.

## <a name="should-i-store-secrets-in-app-configuration"></a>Moet ik geheimen opslaan in de app-configuratie?

Hoewel app-configuratie een beveiligde beveiliging biedt, is Key Vault nog steeds de beste plaats voor het opslaan van toepassings geheimen. Key Vault biedt versleuteling op hardwareniveau, granulair toegangs beleid en beheer bewerkingen zoals het draaien van certificaten.

U kunt app-configuratie waarden maken die verwijzen naar geheimen die zijn opgeslagen in Key Vault. Zie [Key Vault verwijzingen gebruiken in een ASP.net core-app](./use-key-vault-references-dotnet-core.md)voor meer informatie.

## <a name="does-app-configuration-encrypt-my-data"></a>Versleutelen de app-configuratie mijn gegevens?

Ja. App-configuratie versleutelt alle sleutel waarden die worden bewaard en versleutelt de netwerk communicatie. Sleutel namen en labels worden gebruikt als indexen voor het ophalen van configuratie gegevens en worden niet versleuteld.

## <a name="where-does-data-stored-in-app-configuration-reside"></a>Waar worden gegevens opgeslagen in de app-configuratie? 

Klant gegevens die zijn opgeslagen in app-configuratie bevinden zich in de regio waar de app-configuratie van de klant is gemaakt. App-configuratie kan gegevens repliceren naar [gekoppelde regio's](../best-practices-availability-paired-regions.md) voor gegevens tolerantie, maar er worden geen klant gegevens gerepliceerd of verplaatst buiten hun geografische regio zoals gedefinieerd door [Data locatie in azure](https://azure.microsoft.com/global-infrastructure/data-residency/). Klanten en eind gebruikers kunnen hun klant gegevens vanaf elke locatie wereld wijd verplaatsen, kopiëren of gebruiken.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Hoe wijkt de configuratie van de app af van Azure App Service instellingen?

Met Azure App Service kunt u app-instellingen definiëren voor elk App Service exemplaar. Deze instellingen worden door gegeven als omgevings variabelen aan de toepassings code. Als u wilt, kunt u een instelling koppelen aan een specifieke implementatie sleuf. Zie [app-instellingen configureren](../app-service/configure-common.md#configure-app-settings)voor meer informatie.

Azure-app configuratie daarentegen kunt u instellingen definiëren die kunnen worden gedeeld tussen meerdere apps. Dit geldt ook voor apps die worden uitgevoerd in App Service, evenals andere platforms. Uw toepassings code heeft toegang tot deze instellingen via de configuratie providers voor .NET en Java, via de Azure SDK of rechtstreeks via REST Api's.

U kunt ook instellingen importeren en exporteren tussen App Service en app-configuratie. Met deze mogelijkheid kunt u snel een nieuwe app-configuratie opslag instellen op basis van bestaande App Service instellingen. U kunt ook de configuratie delen met een bestaande app die afhankelijk is van App Service instellingen.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Zijn er beperkingen voor de grootte van sleutels en waarden die zijn opgeslagen in de app-configuratie?

Er geldt een limiet van 10 KB voor een item met een enkele sleutel waarde.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Hoe kan ik configuraties voor meerdere omgevingen (testen, fase ring, productie, enzovoort) opslaan?

U bepaalt wie toegang kan krijgen tot de app-configuratie op een niveau per opslag. Gebruik een afzonderlijke opslag voor elke omgeving waarvoor verschillende machtigingen zijn vereist. Deze aanpak biedt de best mogelijke beveiligings isolatie.

Als u geen beveiligings isolatie nodig hebt tussen omgevingen, kunt u labels gebruiken om onderscheid te maken tussen de configuratie waarden. [Gebruik labels om verschillende configuraties voor verschillende omgevingen in te scha kelen voor](./howto-labels-aspnet-core.md) een volledig voor beeld.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Wat zijn de aanbevolen manieren om app-configuratie te gebruiken?

Zie [Aanbevolen procedures](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Wat kost de configuratie van de app?

Er zijn twee prijs Categorieën:

- Gratis laag
- Standard-laag.

Als u een winkel hebt gemaakt vóór de introductie van de laag standaard, wordt deze automatisch verplaatst naar de gratis laag op algemene Beschik baarheid. U kunt een upgrade uitvoeren naar de Standard-laag of blijven op de laag gratis.

U kunt een archief niet van de Standard-laag naar de gratis laag downgradeen. U kunt een nieuw archief maken in de gratis laag en vervolgens configuratie gegevens in dat archief importeren.

## <a name="which-app-configuration-tier-should-i-use"></a>Welke app-configuratie-laag moet ik gebruiken?

Beide app-configuratie lagen bieden kern functionaliteit, waaronder configuratie-instellingen, functie vlaggen, Key Vault verwijzingen, basis beheer bewerkingen, metrische gegevens en Logboeken.

Hier volgen enkele aandachtspunten voor het kiezen van een laag.

- **Resources per abonnement**: een resource bestaat uit één configuratie opslag. Elk abonnement is beperkt tot één configuratie archief in de gratis laag. Abonnementen kunnen een onbeperkt aantal configuratie archieven hebben in de laag standaard.
- **Opslag per resource**: in de gratis laag is een configuratie archief beperkt tot 10 MB aan opslag ruimte. In de laag standaard kan elk configuratie archief Maxi maal 1 GB aan opslag ruimte gebruiken.
- **Revisie geschiedenis**: app-configuratie slaat een geschiedenis op van alle wijzigingen die zijn aangebracht in sleutels. In de laag gratis wordt deze geschiedenis gedurende zeven dagen opgeslagen. In de laag standaard wordt deze geschiedenis 30 dagen opgeslagen.
- **Quota voor aanvragen**: de gratis laag winkels zijn beperkt tot 1.000 aanvragen per dag. Wanneer een Store 1.000 aanvragen heeft bereikt, wordt de HTTP-status code 429 voor alle aanvragen tot middernacht UTC geretourneerd.

    De Standard-laag archieven zijn beperkt tot 20.000 aanvragen per uur. Wanneer het quotum is uitgeput, wordt de HTTP-status code 429 geretourneerd voor alle aanvragen tot het eind van het uur.

- **Service overeenkomst**: de laag Standard heeft een SLA van 99,9% Beschik baarheid. De laag gratis heeft geen SLA.
- **Beveiligings functies**: beide lagen bevatten basis beveiligings functionaliteit, inclusief versleuteling met door micro soft beheerde sleutels, verificatie via HMAC of Azure Active Directory, ondersteuning voor Azure RBAC, beheerde identiteit en service tags. De laag standaard biedt meer geavanceerde beveiligings functies, waaronder ondersteuning voor persoonlijke koppelingen en versleuteling met door de klant beheerde sleutels.
- **Kosten**: voor de Standard-laag worden dagelijks gebruiks kosten in rekening gebracht. De eerste 200.000 aanvragen worden elke dag opgenomen in de dagelijkse kosten. Er is ook een overschrijding-vergoeding voor aanvragen die na de dagelijkse toewijzing zijn. Er zijn geen kosten verbonden aan het gebruik van een gratis laag opslag.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>Kan ik een archief van de gratis laag upgraden naar de laag standaard? Kan ik een archief van de Standard-laag naar de gratis laag downgradeen?

U kunt op elk gewenst moment een upgrade uitvoeren van de gratis laag naar de Standard-laag.

U kunt een archief niet van de Standard-laag naar de gratis laag downgradeen. U kunt een nieuw archief maken in de gratis laag en vervolgens [configuratie gegevens in dat archief importeren](howto-import-export-data.md).

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>Gelden er limieten voor het aantal aanvragen dat is gemaakt voor de app-configuratie?

Configuratie archieven in de gratis laag zijn beperkt tot 1.000 aanvragen per dag. Configuratie archieven in de Standard-laag kunnen tijdelijk worden beperkt wanneer de aanvraag snelheid 20.000 aanvragen per uur overschrijdt.

Wanneer de limiet van een Store is bereikt, wordt de HTTP-status code 429 geretourneerd voor alle aanvragen die worden gedaan totdat de periode is verstreken. De `retry-after-ms` header in het antwoord geeft een aanbevolen wacht tijd (in milliseconden) aan voordat de aanvraag opnieuw wordt uitgevoerd.

Als uw toepassing regel matig HTTP-status code 429 reacties ondervindt, kunt u deze het beste opnieuw ontwerpen om het aantal aanvragen te verminderen. Zie [aanvragen beperken tot app-configuratie](./howto-best-practices.md#reduce-requests-made-to-app-configuration) voor meer informatie.

## <a name="my-application-receives-http-status-code-429-responses-why"></a>Mijn toepassing ontvangt HTTP-status code 429-reacties. Waarom?

In deze gevallen ontvangt u een HTTP-status code van 429:

* De dagelijkse aanvraag limiet voor een archief in de gratis laag overschrijden.
* Tijdelijke beperking als gevolg van een hoge aanvraag snelheid voor een archief in de laag standaard.
* Overmatig bandbreedte gebruik.
* Er wordt geprobeerd een sleutel te maken of te wijzigen wanneer de opslag limiet wordt overschreden.

Controleer de hoofd tekst van het 429-antwoord op de specifieke reden waarom de aanvraag is mislukt.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Hoe kan ik aankondigingen ontvangen over nieuwe releases en andere informatie met betrekking tot app-configuratie?

Abonneer u op onze [github aankondigingen opslag plaats](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Hoe kan ik een probleem melden of een suggestie geven?

U kunt ons rechtstreeks bereiken op [github](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Volgende stappen

* [Over Azure App Configuration](./overview.md)
