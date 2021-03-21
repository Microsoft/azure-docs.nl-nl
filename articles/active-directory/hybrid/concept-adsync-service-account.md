---
title: 'Azure AD Connect: ADSync-service account | Microsoft Docs'
description: In dit onderwerp wordt het ADSync-service account beschreven en worden aanbevolen procedures met betrekking tot het account geboden.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8dddfb8426b769c06cb5b7494431b7eee34dbf9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94410892"
---
# <a name="adsync-service-account"></a>ADSync-serviceaccount
Azure AD Connect een on-premises service installeert waarmee de synchronisatie tussen Active Directory en Azure Active Directory wordt georchestrator.  De Microsoft Azure AD Sync Synchronization-Service (ADSync) wordt uitgevoerd op een server in uw on-premises omgeving.  De referenties voor de service worden standaard ingesteld in de snelle installaties, maar kunnen worden aangepast om te voldoen aan de beveiligings vereisten van uw organisatie.  Deze referenties worden niet gebruikt om verbinding te maken met uw on-premises forests of Azure Active Directory.

Het kiezen van het ADSync-service account is een belang rijke plannings beslissing die u moet nemen voordat u Azure AD Connect installeert.  Bij een poging om de referenties na de installatie te wijzigen, wordt de service niet gestart, wordt de toegang tot de synchronisatie database verbroken en kan niet worden geverifieerd met uw verbonden directory's (Azure en AD DS).  Er vindt geen synchronisatie plaats totdat de oorspronkelijke referenties zijn hersteld.

## <a name="the-default-adsync-service-account"></a>Het standaard ADSync-service account

Wanneer u uitvoert op een lidserver, wordt de AdSync-service uitgevoerd in de context van een Virtual service-account (LEVERANCIERSPECIFIEKE naam).  Als gevolg van een product beperking wordt een aangepast Service account gemaakt wanneer dit wordt geïnstalleerd op een domein controller.  Als het service account voor snelle instellingen niet voldoet aan de beveiligings vereisten van uw organisatie, implementeert u Azure AD Connect door de optie aanpassen te kiezen.  Kies vervolgens de optie voor het service account die voldoet aan de vereisten van uw organisatie.

>[!NOTE]
>Het standaard service account wanneer dit is geïnstalleerd op een domein controller is van het formulier domein \ AAD_InstallationIdentifier.  Het wacht woord voor dit account wordt wille keurig gegenereerd en biedt aanzienlijke uitdagingen voor herstel en het roteren van wacht woorden.  Micro soft raadt aan om het service account aan te passen tijdens de eerste installatie op een domein controller voor het gebruik van een zelfstandig of groep beheerd service account (sMSA/gMSA)

|Azure AD Connect locatie|Het service account is gemaakt|
|-----|-----|
|Lidserver|NT-SERVICE\ADSync|
|Domeincontroller|Domein \ AAD_74dc30c01e80 (zie opmerking)|

## <a name="custom-adsync-service-accounts"></a>Aangepaste ADSync-service accounts
Micro soft raadt aan om de ADSync-service uit te voeren in de context van een virtueel service account of een zelfstandig of een door een groep beheerd service account.  Uw domein beheerder kan er ook voor kiezen om een service account te maken dat is ingericht om te voldoen aan uw specifieke beveiligings vereisten van de organisatie.   Als u het service account wilt aanpassen dat tijdens de installatie wordt gebruikt, kiest u de optie aanpassen op de pagina snelle instellingen hieronder.   De volgende opties zijn beschikbaar:

- standaard account: Azure AD Connect wordt het service account ingericht zoals hierboven wordt beschreven
- beheerd service account: een zelfstandige of groep-MSA gebruiken die door uw beheerder is ingericht
- domein account: gebruik een domein service account dat is ingericht door uw beheerder

![Scherm afbeelding van de pagina Azure AD Connect Express-instellingen met de optie knoppen aanpassen of snelle instellingen gebruiken.](media/concept-adsync-service-account/adsync1.png)

![Scherm afbeelding van Azure AD Connect pagina vereiste onderdelen installeren met de optie voor het gebruik van een bestaand beheerd service account geselecteerd.](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Wijzigingen in het ADSync-service account vaststellen
Als u de referenties voor de ADSync-service na de installatie wijzigt, wordt de service niet gestart, wordt de toegang tot de synchronisatie database verbroken en kan niet worden geverifieerd met uw verbonden directory's (Azure en AD DS).  Het verlenen van toegang tot de data base aan het nieuwe ADSync-service account is ontoereikend om dit probleem te verhelpen. Er vindt geen synchronisatie plaats totdat de oorspronkelijke referenties zijn hersteld.

De ADSync-service geeft het gebeurtenis logboek een bericht fout niveau wanneer het niet kan worden gestart.  De inhoud van het bericht kan variëren, afhankelijk van het feit of de ingebouwde data base (localdb) of volledige SQL in gebruik is.  Hier volgen enkele voor beelden van de vermeldingen in het gebeurtenis logboek die mogelijk aanwezig zijn.

### <a name="example-1"></a>Voorbeeld 1

De versleutelings sleutels voor de AdSync-service zijn niet gevonden en zijn opnieuw gemaakt.  Er wordt geen synchronisatie uitgevoerd totdat dit probleem is opgelost.

Probleem oplossing voor dit probleem de versleutelings sleutels voor de Microsoft Azure AD-synchronisatie worden ontoegankelijk als de aanmeldings referenties van de AdSync-service worden gewijzigd.  Als de referenties zijn gewijzigd, gebruikt u de service toepassing om het aanmeldings account terug te zetten op de oorspronkelijk geconfigureerde waarde (bijvoorbeeld NT SERVICE\AdSync) en start de service opnieuw.  Hiermee herstelt u onmiddellijk de juiste werking van de AdSync-service.

Raadpleeg het volgende [artikel](./whatis-hybrid-identity.md) voor meer informatie.

### <a name="example-2"></a>Voorbeeld 2

De service kan niet worden gestart omdat er geen verbinding kan worden gemaakt met de lokale data base (localdb).

Problemen met dit probleem oplossen de Microsoft Azure AD synchronisatie service heeft geen toegang tot de lokale database provider als de aanmeldings referenties van de AdSync-service worden gewijzigd.  Als de referenties zijn gewijzigd, gebruikt u de service toepassing om het aanmeldings account terug te zetten op de oorspronkelijk geconfigureerde waarde (bijvoorbeeld NT SERVICE\AdSync) en start de service opnieuw.  Hiermee herstelt u onmiddellijk de juiste werking van de AdSync-service.

Raadpleeg het volgende [artikel](./whatis-hybrid-identity.md) voor meer informatie.

Aanvullende informatie de volgende fout gegevens zijn geretourneerd door de provider:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).