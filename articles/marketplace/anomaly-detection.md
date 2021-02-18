---
title: Afwijkings detectie voor facturering in data limiet | Azure Marketplace
description: Meer informatie over hoe automatische afwijkings detectie voor factuur met data limiet ervoor zorgt dat uw klanten correct worden gefactureerd voor gebruik in de aanbieding van uw commerciële Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 2/17/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 7f1bcac49ccb12ea1e0517aed5a139e549451ec0
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653003"
---
# <a name="anomaly-detection-for-metered-billing"></a>Afwijkings detectie voor facturering met data limiet

De aangepaste optie voor facturering via data limiet is momenteel beschikbaar voor SaaS-aanbiedingen ( [Software as a Service](plan-saas-offer.md) ) en [Azure-toepassingen](plan-azure-application-offer.md#types-of-plans) met een beheerd toepassings abonnement.

Als u gebruikmaakt van de facturerings optie met data limiet voor het maken van aanbiedingen in het Commercial Marketplace-programma waarmee u het gebruik kunt berekenen op basis van niet-standaard eenheden, moet u weten wanneer uw klant meer van een service heeft gebruikt dan verwacht.

## <a name="use-the-anomaly-detection-feature"></a>De functie voor afwijkings detectie gebruiken

Micro soft is afhankelijk van u, de partner, om het overschrijding gebruik van hun SaaS-of Azure Managed Application-aanbiedingen te melden voordat micro soft de klant factureert. Als het verkeerde gebruik wordt gerapporteerd, zou de klant mogelijk een onjuiste factuur kunnen ontvangen, zowel micro soft als de geloofwaardigheid van de partner ondermijnen.

Om ervoor te zorgen dat uw klanten correct worden gefactureerd, gebruikt u de functie **anomalie detectie** voor zowel SaaS-apps als Azure Application Managed Application-abonnementen. Deze functie bewaakt het gebruik van facturering met data limieten en voor spelt de verwachte waarde van het gebruik binnen het verwachte bereik. Als het gebruik zich buiten het verwachte bereik bevindt, wordt het beschouwd als onverwacht, (een afwijkend) en ontvangt u een waarschuwings melding op de overzichts pagina van uw aanbieding in het Commercial Marketplace-programma van partner Center. U kunt het dagelijks gebruik van uw klanten bijhouden voor elke aangepaste meter dimensie die u hebt ingesteld.

## <a name="view-and-manage-metered-usage-anomalies"></a>Afwijkingen in het gebruik van een Data limiet weer geven en beheren

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
1. Selecteer in het menu aan de linkerkant de optie **commerciële Marketplace**  >  **analyseren**.
1. Selecteer het tabblad afwijkingen voor het gebruik van de **Data limiet** .

    [![Illustreert het tabblad afwijkingen van het gebruik op de pagina gebruik.](./media/anomaly-detection/metered-usage-anomalies.png)](./media/anomaly-detection/metered-usage-anomalies.png#lightbox)
    ***Afbeelding 1: het tabblad afwijkingen in het gebruik van een Data limiet***

1. Voor eventuele afwijkingen van het gebruik van een Data limiet, wordt u gevraagd om te onderzoeken en te bevestigen of de afwijkingen waar of niet zijn. Selecteer **als afwijkingen markeren** om de diagnose te bevestigen.

     [![Illustreert het dialoog venster markeren als afwijkend.](./media/anomaly-detection/mark-as-anomaly.png)](./media/anomaly-detection/mark-as-anomaly.png#lightbox)
    ***Afbeelding 2: het dialoog venster markeren als afwijkingen***

1. Als u denkt dat de door ons gedetecteerde overschrijding-gebruiks afwijkingen niet legitiem zijn, kunt u deze feedback geven door **geen afwijkingen** te selecteren voor het partner centrum met de vlag voor het desbetreffende overschrijding-gebruik.

    [![Illustreert het dialoog venster waarom is het geen afwijkingen.](./media/anomaly-detection/why-is-it-not-an-anomaly.png)](./media/anomaly-detection/why-is-it-not-an-anomaly.png#lightbox)
    ***Afbeelding 3: Waarom is het geen afwijkingen? in het dialoog venster***

1. U kunt op de pagina omlaag schuiven om een inventarisatie lijst van niet-bevestigde afwijkingen weer te geven. De lijst biedt een inventarisatie van afwijkingen die u niet hebt bevestigd. U kunt ervoor kiezen om een van de gemarkeerde afwijkingen van het partner centrum als authentiek of onwaar te markeren.

   [![Illustreert de lijst met niet-bevestigde afwijkingen van het partner centrum op de gebruiks pagina.](./media/anomaly-detection/unacknowledged-anomalies.png)](./media/anomaly-detection/unacknowledged-anomalies.png#lightbox)
    ***Afbeelding 4: lijst met niet-bevestigde afwijkingen van partner centrum***

1. U ziet ook een logboek voor afwijkings acties dat de bewerkingen weergeeft die u hebt uitgevoerd op het overschrijding-gebruik. In het actie logboek kunt u zien welke overschrijding-gebruiks gebeurtenissen zijn gemarkeerd als authentiek of onwaar.

   [ ![ Illustreert het logboek voor afwijkings acties op de pagina gebruik.](./media/anomaly-detection/anomaly-action-log.png)](./media/anomaly-detection/anomaly-action-log.png#lightbox) 
    ***Afbeelding 5: logboek voor afwijkings acties***

1. Partner Center Analytics ondersteunt geen aanpassing van overschrijding-gebruiks gebeurtenissen in de export rapporten. In het partner centrum kunt u het gecorrigeerde overschrijding-gebruik voor een afwijkend en de details worden door gegeven aan micro soft teams voor onderzoek. Op basis van het onderzoek brengt micro soft, indien van toepassing, tegoeden aan de overbelaste klant. Wanneer u een van de gemarkeerde afwijkingen selecteert, kunt u **markeren als afwijkingen** selecteren om het gebruik van overschrijding als legitiem te markeren.

   [ ![ Illustreert het dialoog venster markeren als een afwijkingen.](./media/anomaly-detection/new-reported-usage.png)](./media/anomaly-detection/new-reported-usage.png#lightbox) 
    ***Afbeelding: 6: markeren als afwijkend dialoog venster***

De eerste keer dat een overschrijding-gebruik als onregelmatig is gemarkeerd in het partner centrum, ontvangt u een venster van 30 dagen vanaf dat exemplaar om de anomalie te markeren als authentiek of onwaar. Na de periode van 30 dagen kunt u, als de uitgever, geen actie ondernemen voor de afwijkingen.

> [!IMPORTANT]
> Onder de gemelde overschrijding-gebruiks eenheden komen niet in aanmerking voor herwaardering en financiële correctie.

U kunt alle afwijkingen (bevestigd of anderszins) voor de geselecteerde reken periode bekijken. De verschillende reken perioden zijn de afgelopen 30 dagen, de afgelopen 60 dagen en de laatste 90 dagen.

> [!IMPORTANT]
> U wordt gevraagd om te reageren op de gemarkeerde afwijkingen binnen 30 dagen na de tijd vanaf het moment dat de afwijkingen voor het eerst worden gerapporteerd in het partner centrum.

Met het filter modaal in de widget kunt u afzonderlijke aanbiedingen en individuele aangepaste meters selecteren.

Nadat u een overschrijding-gebruik als anomalie hebt gemarkeerd of een model dat is gemarkeerd als legitiem hebt bevestigd, kunt u de selectie niet wijzigen in "geen afwijkingen".

> [!IMPORTANT]
> U kunt overschrijding-gebruik opnieuw verzenden in het geval van overbelaste situaties.

## <a name="see-also"></a>Zie ook
- [Factuur met data limiet voor SaaS met de commerciële Marketplace-meet service](./partner-center-portal/saas-metered-billing.md)
- [Factuur voor beheerde toepassing met data limiet](./partner-center-portal/azure-app-metered-billing.md)
