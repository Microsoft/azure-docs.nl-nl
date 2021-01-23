---
title: Aanbevolen procedures voor Azure-app-configuratie | Microsoft Docs
description: Leer de aanbevolen procedures voor het gebruik van Azure-app configuratie. Onderwerpen zijn onder andere belang rijke groeperingen, sleutel waarde-samen stellingen, Boots trap van de app-configuratie en meer.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 1e5a04d385ba6c6dda5b52b23fd4793860f991dc
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737198"
---
# <a name="azure-app-configuration-best-practices"></a>Aanbevolen procedures voor Azure-app configuratie

In dit artikel worden algemene patronen en aanbevolen procedures besproken wanneer u Azure-app-configuratie gebruikt.

## <a name="key-groupings"></a>Sleutel groeperingen

App-configuratie biedt twee opties voor het ordenen van sleutels:

* Sleutel voorvoegsels
* Labels

U kunt een of beide opties gebruiken om uw sleutels te groeperen.

*Sleutel voorvoegsels* zijn de begin onderdelen van sleutels. U kunt een set sleutels logisch groeperen met behulp van hetzelfde voor voegsel in hun namen. Voor voegsels kunnen meerdere onderdelen bevatten die zijn verbonden met een scheidings teken, zoals `/` , vergelijkbaar met een URL-pad, om een naam ruimte te vormen. Dergelijke hiërarchieën zijn handig wanneer u sleutels opslaat voor veel toepassingen, Component Services en omgevingen in één app-configuratie opslag.

Het is belang rijk om ervoor te zorgen dat sleutels uw toepassings code bevat om de waarden van de overeenkomstige instellingen op te halen. Sleutels mogen niet worden gewijzigd, anders moet u de code altijd wijzigen.

*Labels* zijn een kenmerk op sleutels. Ze worden gebruikt om varianten van een sleutel te maken. U kunt bijvoorbeeld labels toewijzen aan meerdere versies van een sleutel. Een versie kan een iteratie, een omgeving of andere contextuele informatie zijn. Uw toepassing kan een volledig andere set sleutel waarden aanvragen door een ander label op te geven. Als gevolg hiervan blijven alle sleutel verwijzingen ongewijzigd in uw code.

## <a name="key-value-compositions"></a>Sleutel-waarde-samen stellingen

App-configuratie behandelt alle sleutels die ermee zijn opgeslagen als onafhankelijke entiteiten. App-configuratie probeert geen relatie tussen sleutels af te leiden of sleutel waarden over te nemen op basis van de hiërarchie. U kunt meerdere sets sleutels samen voegen, met behulp van labels die zijn gekoppeld aan de juiste configuratie stacks in uw toepassings code.

We kijken naar een voorbeeld. Stel dat u een instelling hebt met de naam **Asset1**, waarvan de waarde kan variëren op basis van de ontwikkelings omgeving. U maakt een sleutel met de naam ' Asset1 ' met een leeg label en een label met de naam ' ontwikkeling '. In het eerste label plaatst u de standaard waarde voor **Asset1** en plaatst u een specifieke waarde voor ' ontwikkeling ' in de laatste.

In uw code haalt u eerst de sleutel waarden zonder labels op en vervolgens haalt u dezelfde set sleutel waarden een tweede keer op met het label ' ontwikkeling '. Wanneer u de waarden de tweede keer ophaalt, worden de vorige waarden van de sleutels overschreven. Met het configuratie systeem van .NET Core kunt u meerdere sets configuratie gegevens boven op elkaar stapelen. Als een sleutel in meer dan één set bestaat, wordt de laatste set die deze bevat, gebruikt. Met een modern programmeer raamwerk, zoals .NET core, krijgt u deze gestapelde mogelijkheid gratis als u een systeem eigen configuratie provider gebruikt om toegang te krijgen tot de app-configuratie. Het volgende code fragment laat zien hoe u stacking in een .NET core-toepassing kunt implementeren:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[Gebruik labels om verschillende configuraties voor verschillende omgevingen in te scha kelen voor](./howto-labels-aspnet-core.md) een volledig voor beeld.

## <a name="app-configuration-bootstrap"></a>Boots trap configuratie app

Als u toegang wilt krijgen tot een app-configuratie archief, kunt u de bijbehorende connection string gebruiken, die beschikbaar is in de Azure Portal. Omdat verbindings reeksen referentie gegevens bevatten, worden ze beschouwd als geheimen. Deze geheimen moeten worden opgeslagen in Azure Key Vault en uw code moet worden geverifieerd bij Key Vault om ze op te halen.

Een betere optie is het gebruik van de functie Managed Identities in Azure Active Directory. Met beheerde identiteiten hebt u alleen de app configuratie-eind punt-URL nodig om de Boots trap toegang te geven tot uw app-configuratie archief. U kunt de URL insluiten in de code van uw toepassing (bijvoorbeeld in de *appsettings.jsin* het bestand). Zie [integreren met door Azure beheerde identiteiten](howto-integrate-azure-managed-service-identity.md) voor meer informatie.

## <a name="app-or-function-access-to-app-configuration"></a>App of functie toegang tot de app-configuratie

U kunt een van de volgende methoden gebruiken om toegang te krijgen tot de app-configuratie voor web-apps of functies:

* Voer in de Azure Portal de connection string in voor uw app-configuratie archief in de toepassings instellingen van App Service.
* Sla de connection string op in de Key Vault van uw app-configuratie en [refereer deze vanuit app service](../app-service/app-service-key-vault-references.md).
* Gebruik Azure Managed Identities om toegang te krijgen tot de app-configuratie opslag. Zie [integratie met door Azure beheerde identiteiten](howto-integrate-azure-managed-service-identity.md)voor meer informatie.
* Push configuratie van app-configuratie naar App Service. App-configuratie biedt een export functie (in Azure Portal en de Azure CLI) waarmee gegevens rechtstreeks naar App Service worden verzonden. Met deze methode hoeft u de toepassings code helemaal niet te wijzigen.

## <a name="reduce-requests-made-to-app-configuration"></a>Aanvragen voor app-configuratie beperken

Buitensporige aanvragen voor app-configuratie kunnen leiden tot beperking of overschrijding kosten. Zo beperkt u het aantal aanvragen dat wordt gedaan:

* Verhoog de time-out voor vernieuwen, met name als uw configuratie waarden niet vaak veranderen. Geef een nieuwe time-out voor vernieuwen op met behulp van de [ `SetCacheExpiration` methode](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration).

* Bekijk één *Sentinel-sleutel* in plaats van de afzonderlijke sleutels te bekijken. Vernieuw alle configuratie alleen als de verklikker sleutel wordt gewijzigd. Zie [dynamische configuratie in een ASP.net core-app gebruiken](enable-dynamic-configuration-aspnet-core.md) voor een voor beeld.

* Gebruik Azure Event Grid om meldingen te ontvangen wanneer de configuratie wordt gewijzigd, in plaats van voortdurend te pollen voor wijzigingen. Zie [Azure-app configuratie gebeurtenissen naar een webeindpunt routeren](./howto-app-configuration-event.md) voor meer informatie

## <a name="importing-configuration-data-into-app-configuration"></a>Configuratie gegevens importeren in de app-configuratie

App-configuratie biedt de mogelijkheid om uw configuratie-instellingen bulksgewijs te [importeren](./howto-import-export-data.md) uit uw huidige configuratie bestanden met behulp van de Azure portal of cli. U kunt ook dezelfde opties gebruiken om waarden te exporteren uit de app-configuratie, bijvoorbeeld tussen gerelateerde winkels. Als u een doorlopende synchronisatie met uw GitHub opslag plaats wilt instellen, kunt u de [actie github](./concept-github-action.md) gebruiken zodat u uw bestaande broncode beheer procedures kunt blijven gebruiken terwijl u de voor delen van app-configuratie krijgt.

## <a name="multi-region-deployment-in-app-configuration"></a>Implementatie met meerdere regio's in app-configuratie

App-configuratie is regionale service. Voor toepassingen met verschillende configuraties per regio kunt u deze configuraties in één exemplaar opslaan om een Single Point of Failure te maken. Het implementeren van één app-configuratie-instanties per regio in meerdere regio's is mogelijk een betere optie. Dit kan helpen bij het herstellen van regionale nood herstel, prestaties en beveiligings Silo. Het configureren per regio verbetert ook de latentie en maakt gebruik van gescheiden beperkings quota's, omdat beperking per instantie is. Voor het Toep assen van herstel na nood gevallen kunt u [meerdere configuratie archieven](./concept-disaster-recovery.md)gebruiken. 

## <a name="client-applications-in-app-configuration"></a>Client toepassingen in app-configuratie 

Buitensporige aanvragen voor app-configuratie kunnen leiden tot beperking of overschrijding kosten. Toepassingen maken gebruik van de cache en intelligent vernieuwen die op dit moment beschikbaar zijn om het aantal verzonden aanvragen te optimaliseren. Dit proces kan worden gespiegeld in client toepassingen met een hoog volume door directe verbindingen met de configuratie opslag te voor komen. Client toepassingen maken in plaats daarvan verbinding met een aangepaste service en deze service communiceert met de configuratie opslag. Deze proxy oplossing kan ervoor zorgen dat de client toepassingen niet de beperkings limiet voor het configuratie archief benadert. Raadpleeg [de veelgestelde vragen](./faq.md#are-there-any-limits-on-the-number-of-requests-made-to-app-configuration)voor meer informatie over beperking.  

## <a name="next-steps"></a>Volgende stappen

* [Sleutels en waarden](./concept-key-value.md)