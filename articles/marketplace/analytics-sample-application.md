---
title: Voorbeeld toepassing voor toegang tot de gegevens van commerciële Marketplace Analytics
description: Gebruik de voorbeeld toepassing om uw eigen commerciële Marketplace Analytics-toepassing te bouwen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8fe2301c4d4ed2a582774c65d5dbe68bab416aa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583860"
---
# <a name="sample-application-for-accessing-commercial-marketplace-analytics-data"></a>Voorbeeld toepassing voor toegang tot de gegevens van commerciële Marketplace Analytics

Voorbeeld toepassingen worden gemaakt in C#-en JAVA-talen en zijn beschikbaar op [github](https://github.com/partneranalytics).

- [C#-voorbeeld toepassing](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV)
- [JAVA-voorbeeld toepassing](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV_Java)

U kunt ervoor kiezen om inspiratie uit de voorbeeld toepassing te halen en uw eigen toepassing in een wille keurige taal te bouwen.

De voorbeeld toepassing behaalt de volgende doelen:

- Hiermee wordt een Azure Active Directory-token (Azure AD) gegenereerd.
- Hiermee worden de beschik bare gegevens sets opgehaald.
- Hiermee maakt u door de gebruiker gedefinieerde query's.
- Hiermee worden door de gebruiker gedefinieerde en systeem query's opgehaald.
- Hiermee plant u een rapport.

De voorbeeld toepassing heeft geen betrekking op de methode voor het aanroepen van Api's voor andere functies. Het proces voor het aanroepen van andere Api's blijft echter hetzelfde als hierboven beschreven.

## <a name="how-to-run-the-application"></a>De toepassing uitvoeren

1. Kloon de opslag plaats met een lokaal systeem met behulp van deze opdracht:

    `git clone https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git`

    > [!NOTE]
    > Raadpleeg het `ProgrammaticExportSampleAppISV/README.md` bestand in de GitHub- [opslag plaats](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git)voor meer instructies.

1. Als u de app snel wilt uitvoeren, moet u de client-ID en het client geheim bijwerken in de **appsettings.Development.jsop**

    :::image type="content" source="./media/analytics-programmatic-access/appsettings-development-json.png" alt-text="Illustreert een fragment van de appsettings.Development.jsvoor het bestand.":::

Als u de app uitvoert, wordt een lokale webserver gestart en wordt er een pagina geopend ( `https://localhost:44365/ProgrammaticExportSampleApp/sample` ).

[![Illustreert de pagina rapport plannen.](./media/analytics-programmatic-access/schedule-report.png)](./media/analytics-programmatic-access/schedule-report.png#lightbox)

Op deze pagina worden API-aanroepen naar de webserver uitgevoerd op de lokale computer, die op zijn beurt de daad werkelijke programmatische API-aanroepen.

## <a name="code-snippets"></a>Codefragmenten

De basis structuur van de C#-code voor het uitvoeren van de API-aanroepen programmatische toegang is als volgt:

:::image type="content" source="./media/analytics-programmatic-access/code-snippets.png" alt-text="Scherm opname van API-aanroepen.":::

## <a name="next-steps"></a>Volgende stappen

- [Api's voor het verkrijgen van toegang tot commerciële Marketplace Analytics-gegevens](analytics-available-apis.md)
