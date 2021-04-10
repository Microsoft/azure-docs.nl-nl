---
title: Logboeken van Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over logboekregistratie in Azure Communication Services
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0bf7213e5cfdc57194482c6914d4e25b9336832e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935871"
---
# <a name="communication-services-logs"></a>Logboeken van Communication Services

Azure Communication Services biedt mogelijkheden voor logboekregistratie die u kunt gebruiken om uw Communication Services-oplossing te bewaken en fouten op te sporen. Deze mogelijkheden kunnen worden geconfigureerd via de Azure Portal.

## <a name="enable-diagnostic-logs-in-your-resource"></a>Diagnostische logboeken inschakelen in uw resource

Logboekregistratie is standaard uitgeschakeld wanneer een resource wordt gemaakt. Als u logboekregistratie wilt inschakelen, gaat u naar de blade **Diagnostische instellingen** in het resource-menu onder het gedeelte **Bewaking**. Klik vervolgens op **Diagnostische instelling toevoegen**.

Selecteer vervolgens het gewenste archiefdoel. Momenteel ondersteunen we opslagaccounts en Log Analytics als archiefdoelen. Sla de diagnostische instellingen op nadat u de typen logboeken hebt geselecteerd die u wilt opnemen.
 
Nieuwe instellingen worden na ongeveer tien minuten van kracht. Logboeken worden weergegeven in het geconfigureerde archiveringsdoel in het deelvenster Logboeken van uw Communication Services-resource.

:::image type="content" source="./media/diagnostic-settings.png" alt-text="Opties voor de diagnostische instellingen van ACS.":::

Zie voor meer informatie over het configureren van diagnostische gegevens het overzicht van [Azure-resourcelogboeken](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="resource-log-categories"></a>Categorieën van resourcelogboeken

Communication Services biedt drie typen logboeken die u kunt inschakelen:

* **Gebruikslogboeken** - bevat gebruiksgegevens die zijn gekoppeld aan elke gefactureerde serviceaanbieding
* **Operationele logboeken van chats** - bevat basisinformatie met betrekking tot de chatservice
* **Operationele logboeken van sms** - bevat basisinformatie met betrekking tot de sms-service
* **Operationele logboeken van verificatie** - bevat basisinformatie met betrekking tot de verificatieservice

### <a name="usage-logs-schema"></a>Schema voor gebruikslogboeken

| Eigenschap | Beschrijving |
| -------- | ---------------|
| Tijdstempel | De timestamp (UTC) van het moment waarop het logboek werd gegenereerd. |
| Naam van bewerking | De bewerking die aan de logboekrecord is gekoppeld. |
| Versie van de bewerking | De `api-version` gekoppeld aan de bewerking, als operationName is uitgevoerd met behulp van een API. Als er geen API is die overeenkomt met deze bewerking, vertegenwoordigt de versie de versie van die bewerking in het geval dat de eigenschappen die aan de bewerking zijn gekoppeld in de toekomst worden gewijzigd. |
| Categorie | De logboekcategorie van de gebeurtenis. De categorie is de granulariteit waarmee u logboeken voor op een bepaalde resource kunt in- of uitschakelen. De eigenschappen die worden weergegeven in de blob-eigenschappen van een gebeurtenis zijn hetzelfde binnen een bepaalde logboekcategorie en resourcetype. |
| Correlatie-id | De id voor gecorreleerde gebeurtenissen. Kan worden gebruikt om gecorreleerde gebeurtenissen tussen meerdere tabellen te identificeren. |
| Eigenschappen | Andere gegevens die van toepassing zijn op verschillende modi van Communication Services. |
| Record-id | De unieke id voor een bepaald gebruiksrecord. |
| Gebruikstype | De modus van gebruik. (bijvoorbeeld chat, PSTN, NAT, enz.) |
| Type eenheid | Het type eenheid waarop het gebruik van een bepaalde gebruiksmodus is gebaseerd. (bijvoorbeeld minuten, megabytes, berichten, enz.). |
| Aantal | Het aantal eenheden dat is gebruikt of verbruikt voor deze record. |

### <a name="chat-operational-logs"></a>Operationele logboeken van chats

| Eigenschap | Beschrijving |
| -------- | ---------------|
| TimeGenerated | De timestamp (UTC) van het moment waarop het logboek werd gegenereerd. |
| OperationName | De bewerking die aan de logboekrecord is gekoppeld. |
| Correlatie-id | De id voor gecorreleerde gebeurtenissen. Kan worden gebruikt om gecorreleerde gebeurtenissen tussen meerdere tabellen te identificeren. |
| OperationVersion | De API-versie gekoppeld aan de bewerking, als operationName is uitgevoerd met behulp van een API. Als er geen API is die overeenkomt met deze bewerking, vertegenwoordigt de versie de versie van die bewerking in het geval dat de eigenschappen die aan de bewerking zijn gekoppeld in de toekomst worden gewijzigd. |
| Categorie | De logboekcategorie van de gebeurtenis. De categorie is de granulariteit waarmee u logboeken voor op een bepaalde resource kunt in- of uitschakelen. De eigenschappen die worden weergegeven in de blob-eigenschappen van een gebeurtenis zijn hetzelfde binnen een bepaalde logboekcategorie en resourcetype. |
| ResultType | De status van de bewerking. |
| ResultSignature | De substatus van de bewerking. Als deze bewerking overeenkomt met een REST API-aanroep, dan is dit veld de HTTP-statuscode van de bijbehorende REST-aanroep. |
| ResultDescription | De statische tekstbeschrijving van deze bewerking. |
| DurationMs | De duur van de bewerking in milliseconden. |
| CallerIpAddress | Het IP-adres van de oproepende functie, als de bewerking overeenkomt met een API-aanroep die afkomstig zou zijn van een entiteit met een openbaar beschikbaar IP-adres. |
| Niveau | De ernst van de gebeurtenis. |
| URI | De URI van de aanvraag. |
| UserId | De gebruikers-id van de afzender van de aanvraag. |
| ChatThreadId | De id van de chat-thread die aan de aanvraag is gekoppeld. |
| ChatMessageId | De id van het chatbericht die aan de aanvraag is gekoppeld. |
| SdkType | Het SDK-type dat in de aanvraag wordt gebruikt. |
| PlatformType | Het platformtype dat in de aanvraag wordt gebruikt. |

### <a name="sms-operational-logs"></a>Operationele logboeken van sms

| Eigenschap | Beschrijving |
| -------- | ---------------|
| TimeGenerated | De timestamp (UTC) van het moment waarop het logboek werd gegenereerd. |
| OperationName | De bewerking die aan de logboekrecord is gekoppeld. |
| Correlatie-id | De id voor gecorreleerde gebeurtenissen. Kan worden gebruikt om gecorreleerde gebeurtenissen tussen meerdere tabellen te identificeren. |
| OperationVersion | De API-versie gekoppeld aan de bewerking, als operationName is uitgevoerd met behulp van een API. Als er geen API is die overeenkomt met deze bewerking, vertegenwoordigt de versie de versie van die bewerking in het geval dat de eigenschappen die aan de bewerking zijn gekoppeld in de toekomst worden gewijzigd. |
| Categorie | De logboekcategorie van de gebeurtenis. De categorie is de granulariteit waarmee u logboeken voor op een bepaalde resource kunt in- of uitschakelen. De eigenschappen die worden weergegeven in de blob-eigenschappen van een gebeurtenis zijn hetzelfde binnen een bepaalde logboekcategorie en resourcetype. |
| ResultType | De status van de bewerking. |
| ResultSignature | De substatus van de bewerking. Als deze bewerking overeenkomt met een REST API-aanroep, dan is dit veld de HTTP-statuscode van de bijbehorende REST-aanroep. |
| ResultDescription | De statische tekstbeschrijving van deze bewerking. |
| DurationMs | De duur van de bewerking in milliseconden. |
| CallerIpAddress | Het IP-adres van de oproepende functie, als de bewerking overeenkomt met een API-aanroep die afkomstig zou zijn van een entiteit met een openbaar beschikbaar IP-adres. |
| Niveau | De ernst van de gebeurtenis. |
| URI | De URI van de aanvraag. |
| OutgoingMessageLength | Het aantal tekens in het uitgaande bericht. |
| IncomingMessageLength | Het aantal tekens in het binnenkomende bericht. |
| DeliveryAttempts | Het aantal pogingen dat is gedaan om dit bericht af te leveren. |
| PhoneNumber | Het telefoon nummer van het SMS-bericht dat wordt verzonden. |
| SdkType | Het SDK-type dat in de aanvraag wordt gebruikt. |
| PlatformType | Het platformtype dat in de aanvraag wordt gebruikt. |
| Methode | De methode die in de aanvraag wordt gebruikt. |

### <a name="authentication-operational-logs"></a>Operationele logboeken van verificatie

| Eigenschap | Beschrijving |
| -------- | ---------------|
| TimeGenerated | De timestamp (UTC) van het moment waarop het logboek werd gegenereerd. |
| OperationName | De bewerking die aan de logboekrecord is gekoppeld. |
| Correlatie-id | De id voor gecorreleerde gebeurtenissen. Kan worden gebruikt om gecorreleerde gebeurtenissen tussen meerdere tabellen te identificeren. |
| OperationVersion | De `api-version` gekoppeld aan de bewerking, als de `operationName` is uitgevoerd met behulp van een API. Als er geen API is die overeenkomt met deze bewerking, vertegenwoordigt de versie de versie van die bewerking in het geval dat de eigenschappen die aan de bewerking zijn gekoppeld in de toekomst worden gewijzigd. |
| Categorie | De logboekcategorie van de gebeurtenis. De categorie is de granulariteit waarmee u logboeken voor op een bepaalde resource kunt in- of uitschakelen. De eigenschappen die worden weergegeven in de blob-eigenschappen van een gebeurtenis zijn hetzelfde binnen een bepaalde logboekcategorie en resourcetype. |
| ResultType | De status van de bewerking. |
| ResultSignature | De substatus van de bewerking. Als deze bewerking overeenkomt met een REST API-aanroep, dan is dit veld de HTTP-statuscode van de bijbehorende REST-aanroep. |
| DurationMs | De duur van de bewerking in milliseconden. |
| CallerIpAddress | Het IP-adres van de oproepende functie, als de bewerking overeenkomt met een API-aanroep die afkomstig zou zijn van een entiteit met een openbaar beschikbaar IP-adres. |
| Niveau | De ernst van de gebeurtenis. |
| URI | De URI van de aanvraag. |
| SdkType | Het SDK-type dat in de aanvraag wordt gebruikt. |
| PlatformType | Het platformtype dat in de aanvraag wordt gebruikt. |
| Identiteit | De Communication Services-identiteit die betrekking heeft op de bewerking. |
| Bereiken | De Communication Services-bereiken die aanwezig zijn in het toegangstoken. |
