---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 72588ccfd9ad00305cbdeaf8132999731d86b9b1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92164903"
---
Er wordt een *logboek niveau* toegewezen aan elk logboek. De waarde is een geheel getal dat het relatieve belang aangeeft:

|Logniveau    |Code| Beschrijving |
|------------|---|--------------|
|Tracering       | 0 |Logboeken die de meest gedetailleerde berichten bevatten. Deze berichten kunnen gevoelige toepassings gegevens bevatten. Deze berichten zijn standaard uitgeschakeld en mogen nooit worden ingeschakeld in een productie omgeving.|
|Fouten opsporen       | 1 | Logboeken die tijdens de ontwikkeling worden gebruikt voor interactief onderzoek. Deze logboeken moeten voornamelijk informatie bevatten die nuttig is voor het opsporen van fouten en die geen lange termijn waarde heeft. |
|Informatie | 2 | Logboeken die de algemene stroom van de toepassing volgen. Deze logboeken moeten een lange termijn waarde hebben. |
|Waarschuwing     | 3 | Logboeken die een abnormale of onverwachte gebeurtenis in de toepassings stroom markeren, maar niet anders de uitvoering van de toepassing laten stoppen. |
|Fout       | 4 | Logboeken die markeren wanneer de huidige uitvoerings stroom wordt gestopt vanwege een fout. Deze fouten moeten duiden op een fout in de huidige activiteit, niet op toepassings niveau. |
|Kritiek    | 5 | Logboeken waarin een onherstelbare toepassing of systeem crash wordt beschreven of een fatale fout die onmiddellijke aandacht vereist. |
|Geen        | 6 | Hiermee schakelt u de logboek registratie voor de opgegeven categorie. |

De [ *host.jsvan* de bestands](../articles/azure-functions/functions-host-json.md) configuratie bepaalt hoeveel logboek registratie van een functie-app naar Application Insights verzendt.  
