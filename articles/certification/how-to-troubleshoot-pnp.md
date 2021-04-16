---
title: Problemen met uw IoT-Plug en Play oplossen
description: Een handleiding met aanbevolen stappen voor probleemoplossing voor partners die een IoT-Plug en Play certificeren.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 04/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 9b406e489fb83083d47f01e1483160181601d518
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559065"
---
# <a name="troubleshoot-your-iot-plug-and-play-certification-project"></a>Problemen met uw IoT Plug en Play certificeringsproject oplossen

Tijdens de testfase & verbinding maken van uw IoT Plug en Play-certificeringsproject, kunt u enkele scenario's tegen komen die verhinderen dat u de AICS-tests (Azure for IoT Certification Service) doorstaan.

## <a name="prerequisites"></a>Vereisten

- U moet zijn aangemeld en een project voor uw apparaat hebben gemaakt in de [Azure Certified Device-portal.](https://certify.azure.com) Bekijk de zelfstudie voor [meer informatie.](tutorial-01-creating-your-project.md)

## <a name="when-aics-tests-arent-passing"></a>Wanneer AICS-tests niet worden doorstaan

AICS-test is mogelijk niet geslaagd vanwege verschillende oorzaken. Volg deze stappen om te controleren op veelvoorkomende problemen en problemen met uw apparaat op te lossen.

1. Controleer of uw apparaatcode de nettolading van de model-id instelt tijdens het inrichten van DPS. Dit is een vereiste voor AICS om uw apparaat te valideren.
1. U kunt de telemetrielogboeken van eerdere tests bekijken door op de knop te drukken om te bepalen waardoor de `View Logs` test mislukt. Zowel de testberichten als onbewerkte gegevens kunnen worden beoordeeld.  

    ![Testgegevens controleren](./media/images/review-logs.png)

1. In sommige gevallen waar in de logboeken wordt aangegeven, moet u het apparaat opnieuw opstarten `Failed to get Digital Twin Model ID of device xx due to DeviceNotConnected` en het inrichtingsproces voor het apparaat opnieuw starten.
1. Als de geautomatiseerde tests blijven mislukken, kunt u `request a manual review` de resultaten vervangen. Hiermee wordt een aanvraag voor **handmatige validatie met** het Azure Certified Device-team uitgevoerd.  

    ![Handmatige beoordeling aanvragen](./media/images/request-manual-review.png)

## <a name="when-you-see-passed-with-warnings"></a>Wanneer u 'Geslaagd met waarschuwingen' ziet

Als u tijdens het uitvoeren van de tests een resultaat van ontvangt, betekent dit dat er geen telemetrie is ontvangen `Passed with warnings` tijdens de testperiode. Dit kan worden veroorzaakt door een afhankelijkheid van de telemetrie op langere tijdsintervallen of externe triggers die niet beschikbaar waren. U kunt doorgaan met het indienen van uw apparaat voor beoordeling, waarbij het beoordelingsteam bepaalt of **handmatige** validatie in de toekomst nodig is.

## <a name="when-you-need-help-with-the-model-repository"></a>Wanneer u hulp nodig hebt met de modelopslagplaats

Raadpleeg onze Docs-Plug en Play over de opslagplaats voor apparaatmodellen voor IoT-problemen met betrekking tot [de modelopslagplaats.](https://docs.microsoft.com/azure/iot-pnp/concepts-model-repository)

## <a name="next-steps"></a>Volgende stappen

Hopelijk helpt deze handleiding u verder te gaan met uw IoT Plug en Play certificeringstraject! Nadat u AICS hebt doorgegeven, kunt u doorgaan met onze zelfstudies om uw apparaat in te dienen en te publiceren.

- [Zelfstudie: Uw apparaat testen](tutorial-03-testing-your-device.md)
