---
title: Azure API Management integreren met Azure-toepassing inzichten
titleSuffix: Azure API Management
description: Meer informatie over het registreren en weer geven van gebeurtenissen vanuit Azure API Management in Azure-toepassing Insights.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2021
ms.author: apimpm
ms.openlocfilehash: 97f4eb34b88b3454d65b65d236833e1256c98671
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564245"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Azure API Management integreren met Azure Application Insights

Azure API Management biedt eenvoudige integratie met Azure-toepassing Insights: een uitbreid bare service voor webontwikkelaars die apps op meerdere platforms bouwen en beheren. In deze hand leiding wordt elke stap van deze integratie uitgelegd en worden strategieën beschreven voor het verminderen van de prestaties van uw API Management service-exemplaar.

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt volgen, moet u een Azure API Management-exemplaar hebben. Als u er nog geen hebt, voltooit u eerst de [zelf studie](get-started-create-service-instance.md) .

## <a name="create-an-application-insights-instance"></a>Een Application Insights-exemplaar maken

Voordat u Application Insights kunt gebruiken, moet u eerst een exemplaar van de service maken. Zie [Application Insights resources op basis van werk ruimte](../azure-monitor/app/create-workspace-resource.md)voor stappen om een exemplaar te maken met behulp van de Azure Portal.
## <a name="create-a-connection-between-application-insights-and-api-management"></a>Een verbinding maken tussen Application Insights en API Management

1. Navigeer naar uw **Azure API Management service-exemplaar** in de **Azure Portal**.
1. Selecteer **Application Insights** in het menu aan de linkerkant.
1. Klik op **+ Toevoegen**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="Scherm afbeelding die laat zien waar een nieuwe verbinding moet worden toegevoegd":::
1. Selecteer het eerder gemaakte **Application Insights** -exemplaar en geef een korte beschrijving op.
1. Klik op **Create**.
1. U hebt zojuist een Application Insights logger gemaakt met een instrumentatie sleutel. Deze wordt nu weer gegeven in de lijst.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="Scherm afbeelding die laat zien waar u de zojuist gemaakte Application Insights logger met instrumentatie sleutel wilt weer geven":::

> [!NOTE]
> Achter de scène wordt een [logger](/rest/api/apimanagement/2019-12-01/logger/createorupdate) -entiteit gemaakt in uw API Management-exemplaar met de instrumentatie sleutel van de Application Insights instantie.

## <a name="enable-application-insights-logging-for-your-api"></a>Application Insights logboek registratie inschakelen voor uw API

1. Navigeer naar uw **Azure API Management service-exemplaar** in de **Azure Portal**.
1. Selecteer **API's** in het menu aan de linkerkant.
1. Klik op uw API, in dit geval **demo** van de API van de vergadering. Selecteer een versie als deze is geconfigureerd.
1. Ga naar het tabblad **instellingen** op de bovenste balk.
1. Schuif omlaag naar de sectie **Diagnostische logboeken** .  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="Logboek voor app Insights":::
1. Schakel het **selectie vakje in** .
1. Selecteer de bijgevoegde logboek registratie in de vervolg keuzelijst **bestemming** .
1. Invoer **100** als **steek proef (%)** en schakel het selectie vakje **altijd logboek fouten registreren** in.
1. Selecteer **Opslaan**.

> [!WARNING]
> Als u de standaard waarde **0** in het **aantal Payload-bytes naar de logboek** instelling overschrijft, kunnen de prestaties van uw api's aanzienlijk afnemen.

> [!NOTE]
> Achter de scène wordt een [Diagnostische](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) entiteit met de naam ' applicationinsights ' gemaakt op het API-niveau.

| Naam van de instelling                        | Waardetype                        | Beschrijving                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Inschakelen                              | booleaans                           | Hiermee wordt aangegeven of logboek registratie van deze API is ingeschakeld.                                                                                                                                                                                                                                                                                                |
| Doel                         | Azure-toepassing Insights-logboek registratie | Hiermee wordt aangegeven Azure-toepassing Insights-logboeken moeten worden gebruikt                                                                                                                                                                                                                                                                                           |
| Sampling (%)                        | decimal                           | Waarden van 0 tot 100 (procent). <br/> Hiermee geeft u het percentage aanvragen op dat wordt geregistreerd bij Application Insights. met een steek proef van 0% worden er nul aanvragen geregistreerd, terwijl 100% de steek proef alle geregistreerde aanvragen houdt. <br/> Gebruik deze instelling om het effect op de prestaties te beperken bij het registreren van aanvragen bij Application Insights. Bekijk [prestatie implicaties en logboek sampling](#performance-implications-and-log-sampling). |
| Altijd fouten in logboek registreren                   | booleaans                           | Als deze instelling is geselecteerd, worden alle fouten geregistreerd in Application Insights, ongeacht de **bemonsterings** instelling.   
| IP-adres van client registreren | |  Als deze instelling is geselecteerd, wordt het IP-adres van de client voor API-aanvragen geregistreerd in Application Insights.                                         |
| Uitgebreidheid         |                                   | Hiermee geeft u het uitbreidings niveau op. Alleen aangepaste traceringen met een hoger Ernst niveau worden vastgelegd. Standaard: informatie.      | 
| Correlatie Protocol |  |  Selecteer het protocol dat wordt gebruikt voor het correleren van telemetrie die door meerdere onderdelen worden verzonden. Standaard: verouderd <br/>Zie [correlatie tussen de telemetrie in Application Insights](../azure-monitor/app/correlation.md)voor meer informatie.  |
| Basis opties: te registreren kopteksten              | list                              | Hiermee geeft u de koppen op die worden geregistreerd voor Application Insights voor aanvragen en antwoorden.  Standaard: er worden geen headers vastgelegd.                                                                                                                                                                                                             |
| Basis opties: aantal Payload-bytes dat moet worden geregistreerd | geheel getal                           | Hiermee geeft u op hoeveel eerste bytes van de hoofd tekst moeten worden geregistreerd in Application Insights voor aanvragen en antwoorden.  Standaard waarde: 0.                                                                                                                                                                                                    |
| Geavanceerde opties: front-end-aanvraag  |                                   | Hiermee wordt aangegeven of en hoe de front- *End-aanvragen* worden geregistreerd bij Application Insights. De front- *End-aanvraag* is een aanvraag die binnenkomend is voor de Azure API Management-service.                                                                                                                                                                        |
| Geavanceerde opties: frontend-antwoord |                                   | Hiermee wordt aangegeven of en hoe de *frontend-antwoorden* bij Application Insights worden geregistreerd. *Frontend-antwoord* is een antwoord van de Azure API Management-service.                                                                                                                                                                   |
| Geavanceerde opties: back-end-aanvraag   |                                   | Hiermee wordt aangegeven of en hoe *back-aanvragen* worden geregistreerd bij Application Insights. *Back-end-aanvraag* is een uitgaande aanvraag van de Azure API Management-service.                                                                                                                                                                        |
| Geavanceerde opties: reactie op back-end  |                                   | Hiermee wordt aangegeven of en hoe *back-end-reacties* worden geregistreerd bij Application Insights. *Back-end-respons* is een antwoord dat binnenkomt bij de Azure API Management-service.                                                                                                                                                                       |

> [!NOTE]
> U kunt Logboeken opgeven op verschillende niveaus: enkelvoudige API-logboek registratie of een logger voor alle Api's.
>  
> Als u beide opgeeft:
> + Als ze verschillende logboeken zijn, worden beide gebruikt (multiplex Logboeken),
> + Als ze dezelfde logboeken hebben, maar wel verschillende instellingen hebben, overschrijft het één voor één API (meer gedetailleerd niveau) de voor alle Api's.

## <a name="what-data-is-added-to-application-insights"></a>Welke gegevens worden toegevoegd aan Application Insights

Application Insights ontvangt:

+ Een telemetrie-item *aanvragen* voor elke inkomende aanvraag (front-*End-aanvraag*, *frontend-antwoord*)
+ Telemetrie-item voor *afhankelijkheid* , voor elke aanvraag die wordt doorgestuurd naar een back-end-service (back-end-*aanvraag*, *back-end*)
+ Telemetrie van *uitzonde ring* voor elke mislukte aanvraag:
    + is mislukt vanwege een gesloten client verbinding
    + Er is een *fout opgetreden bij* het API-beleid
    + heeft een antwoord-HTTP-status code die overeenkomt met 4xx of 5xx.
+ Een telemetrie-item *traceren* als u een [tracerings](api-management-advanced-policies.md#Trace) beleid configureert. De `severity` instelling in het `trace` beleid moet gelijk zijn aan of groter zijn dan de `verbosity` instelling in de Application Insights logboek registratie.

> [!NOTE]
> Zie [Application Insights limieten](../azure-monitor/service-limits.md#application-insights) voor informatie over de maximale grootte en het aantal metrische gegevens en gebeurtenissen per Application Insights exemplaar.

## <a name="performance-implications-and-log-sampling"></a>Prestatie implicaties en logboek steekproef

> [!WARNING]
> Het vastleggen van alle gebeurtenissen kan ernstige gevolgen hebben voor de prestaties, afhankelijk van het aantal binnenkomende aanvragen.

Op basis van interne belasting tests heeft het inschakelen van deze functie een reductie van 40%-50% veroorzaakt wanneer de aanvraag snelheid de 1.000 aanvragen per seconde overschrijdt. Application Insights is ontworpen om statistische analyses te gebruiken voor het beoordelen van de prestaties van toepassingen. Het is niet bedoeld als controle systeem en is niet geschikt voor het vastleggen van elke afzonderlijke aanvraag voor high-volume-Api's.

U kunt het aantal aanvragen dat wordt geregistreerd, bewerken door de **sampling** -instelling aan te passen (Zie de voor gaande stappen). Een waarde van 100% betekent dat alle aanvragen worden geregistreerd, terwijl 0% geen logboek registratie weerspiegelt. Door de **steek proef** wordt de hoeveelheid telemetrie verminderd, waardoor de prestaties aanzienlijk worden verkleind, terwijl de voor delen van logboek registratie nog steeds worden verzorgd.

Het overs laan van de logboek registratie van headers en hoofd tekst van aanvragen en antwoorden heeft ook een positieve invloed op het oplossen van prestatie problemen.

## <a name="video"></a>Video

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over [Azure-toepassing Insights](/azure/application-insights/).
+ Overweeg [logboek registratie met Azure Event hubs](api-management-howto-log-event-hubs.md).
