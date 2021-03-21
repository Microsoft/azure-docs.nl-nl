---
title: Risico's onderzoeken met Azure Active Directory B2C identiteits beveiliging
description: Meer informatie over het onderzoeken van Risk ante gebruikers en detecties in Azure AD B2C identiteits beveiliging
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8919285f31e04a51ce10afe3313b28cf86b64ee0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102055692"
---
# <a name="investigate-risk-with-identity-protection-in-azure-ad-b2c"></a>Het risico met betrekking tot identiteits beveiliging in Azure AD B2C onderzoeken

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Identiteitsbeveiliging biedt voortdurende risicodetectie voor uw Azure AD B2C-Tenant. Hiermee kunnen organisaties op identiteit gebaseerde Risico's detecteren, onderzoeken en oplossen. Identiteits beveiliging wordt geleverd met risico rapporten die kunnen worden gebruikt voor het onderzoeken van identiteits Risico's in Azure AD B2C tenants. In dit artikel leert u hoe u Risico's kunt onderzoeken en oplossen.

## <a name="overview"></a>Overzicht

Azure AD B2C identiteits beveiliging biedt twee rapporten. Het rapport *Risk ante gebruikers* is de plek waar beheerders kunnen vinden welke gebruikers risico lopen en Details over detecties. Het rapport van de *risico detectie* biedt informatie over elke risico detectie, waaronder het type, andere Risico's die tegelijkertijd worden geactiveerd, de locatie van aanmeldings pogingen, en meer.

Elk rapport wordt gestart met een lijst met alle detecties voor de periode die boven aan het rapport wordt weer gegeven. Rapporten kunnen worden gefilterd met behulp van de filters aan de bovenkant van het rapport. Beheerders kunnen ervoor kiezen om de gegevens te downloaden of u kunt [MS Graph API en Microsoft Graph Power shell SDK](../active-directory/identity-protection/howto-identity-protection-graph-api.md) gebruiken om de gegevens voortdurend te exporteren.

## <a name="service-limitations-and-considerations"></a>Service beperkingen en overwegingen

Bij het gebruik van identiteits beveiliging moet u rekening houden met het volgende:

- Identiteitsbeveiliging is standaard ingeschakeld.
- Identiteits beveiliging is beschikbaar voor zowel lokale als sociale identiteiten, zoals Google of Facebook. Voor sociale identiteiten moet voorwaardelijke toegang worden geactiveerd. Detectie is beperkt omdat de referenties van het sociale account worden beheerd door de externe ID-provider.
- In Azure AD B2C tenants is er slechts een subset van de [Azure AD Identity Protection risico detecties](../active-directory/identity-protection/overview-identity-protection.md) beschikbaar. De volgende risico detecties worden ondersteund door Azure AD B2C:  

|Risicodetectietype  |Beschrijving  |
|---------|---------|
| Ongewoon traject     | Meld u aan vanaf een ongewoone locatie op basis van de recente aanmeldingen van de gebruiker.        |
|Anoniem IP-adres     | Meld u aan bij een anoniem IP-adres (bijvoorbeeld: Tor browser, Anonymizer Vpn's).        |
|Aan malware gekoppeld IP-adres     | Meld u aan bij een IP-adres dat is gekoppeld aan malware.         |
|Onbekende aanmeldingseigenschappen     | Aanmelden met eigenschappen die niet recent zijn gedetecteerd voor de gegeven gebruiker.        |
|Door beheerder bevestigd misbruik van gebruiker    | Een beheerder heeft aangegeven dat er misbruik is gemaakt een gebruiker.             |
|Wachtwoordspray     | Meld u aan met een aanval met een wacht woord.      |
|Azure AD-bedreigingsinformatie     | De interne en externe bedreigingsinformatiebronnen van Microsoft hebben een bekend aanvalspatroon geïdentificeerd.        |

## <a name="pricing-tier"></a>Prijscategorie

Azure AD B2C Premium P2 is vereist voor een aantal functies voor identiteits beveiliging. Wijzig indien nodig [uw Azure AD B2C-prijscategorie in Premium P2](./billing.md). De volgende tabel bevat een overzicht van de functies voor identiteits beveiliging en de vereiste prijs categorie.  

|Functie   |P1   |P2|
|----------|:-----------:|:------------:|
|Rapport over riskante gebruikers     |&#x2713; |&#x2713; |
|Details van rapport Risk ante gebruikers  | |&#x2713; |
|Risk ante gebruikers rapporteren herstel    | &#x2713; |&#x2713; |
|Rapport over risico detectie   |&#x2713;|&#x2713;|
|Details van rapport over risico detectie  ||&#x2713;|
|Rapport downloaden |  &#x2713;| &#x2713;|
|MS Graph API Access |  &#x2713;| &#x2713;|

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="investigate-risky-users"></a>Riskante gebruikers onderzoeken

Met de informatie die door het rapport Risk ante gebruikers wordt verstrekt, kunnen beheerders het volgende vinden:

- Welke gebruikers lopen risico, hebben het risico gelost of hebben ze een risico gemist?
- Details over detecties
- Geschiedenis van alle Risk ante aanmeldingen
- Risico geschiedenis
 
Beheerders kunnen er vervolgens voor kiezen om actie te ondernemen op deze gebeurtenissen. Beheerders kunnen kiezen uit:

- Het gebruikers wachtwoord opnieuw instellen
- Inbreuk op gebruikers bevestigen
- Gebruikers risico negeren
- Blok keren dat de gebruiker zich aanmeldt
- Meer onderzoeken met behulp van Azure ATP

### <a name="navigating-the-risky-users-report"></a>Het rapport Risk ante gebruikers navigeren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.

1. Onder **Azure-Services** selecteert u **Azure AD B2C**. Of gebruik het zoekvak om **Azure AD B2C** te zoeken en te selecteren.

1. Selecteer onder **Beveiliging** **Riskante gebruikers (preview)** .

   ![Riskante gebruikers](media/identity-protection-investigate-risk/risky-users.png)

    Als u afzonderlijke items selecteert, wordt een detail venster uitgebreid met de detecties. De weer gave Details stelt beheerders in staat om op elke detectie acties te onderzoeken en uit te voeren.

    ![Risk ante gebruikers acties](media/identity-protection-investigate-risk/risky-users-report-actions.png)


## <a name="risk-detections-report"></a>Rapport over risico detectie

Het rapport van de risico detectie bevat filter bare gegevens gedurende de afgelopen 90 dagen (drie maanden).

Met de informatie die door het rapport van de risico detectie wordt verstrekt, kunnen beheerders het volgende vinden:

- Informatie over elke risico detectie, inclusief type.
- Andere Risico's die tegelijkertijd worden geactiveerd.
- Locatie van aanmeldings poging.

Beheerders kunnen vervolgens teruggaan naar het rapport met het risico of het aanmeldingen van de gebruiker om acties uit te voeren op basis van verzamelde gegevens.

### <a name="navigating-the-risk-detections-report"></a>Het rapport over risico detecties navigeren

1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer onder **Beveiliging** **Risicodetecties (preview)** .

   ![Risicodetectie](media/identity-protection-investigate-risk/risk-detections.png)


## <a name="next-steps"></a>Volgende stappen

- [Voorwaardelijke toegang toevoegen aan een gebruikersstroom](conditional-access-user-flow.md).