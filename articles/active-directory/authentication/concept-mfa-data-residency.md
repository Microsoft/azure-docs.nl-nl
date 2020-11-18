---
title: Azure AD Multi-Factor Authentication data locatie
description: Meer informatie over persoonlijke en organisatie gegevens Azure AD Multi-Factor Authentication winkels en uw gebruikers en welke gegevens binnen het land of de regio van herkomst blijven.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3854bb410aba8da65b47a335bf13f1dcc51a0180
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839893"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multi-factor-authentication"></a>Gegevens locatie en klant gegevens voor Azure AD-Multi-Factor Authentication

Klant gegevens worden door Azure AD opgeslagen op een geografische locatie op basis van het adres van uw organisatie bij het abonneren op een micro soft online service, zoals Microsoft 365 en Azure. Voor informatie over waar uw klant gegevens worden opgeslagen, kunt u de sectie [waar bevinden zich uw gegevens?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) in het micro soft vertrouwens centrum.

Azure AD Multi-Factor Authentication van de Cloud en Azure Multi-Factor Authentication-server proces en een zekere hoeveelheid persoonlijke gegevens en bedrijfs gegevens opslaan. In dit artikel wordt beschreven wat en waar gegevens worden opgeslagen.

De Azure AD Multi-Factor Authentication-service heeft data centers in de Verenigde Staten, Europa en Azië en Stille Oceaan. De volgende activiteiten zijn afkomstig uit de regionale data centers, tenzij anders vermeld:

* Multi-factor Authentication met telefoon gesprekken afkomstig van Amerikaanse data centers en wordt gerouteerd door wereld wijde providers.
* Verificatie aanvragen voor algemeen gebruik van andere regio's, zoals Europa of Australië, worden momenteel verwerkt op basis van de locatie van de gebruiker.
* Push meldingen met behulp van de app Microsoft Authenticator worden momenteel verwerkt in de regionale data centers op basis van de locatie van de gebruiker.
    * Leverancierspecifieke services van apparaten, zoals Apple Push meldingen, kunnen buiten de locatie van de gebruiker vallen.

## <a name="personal-data-stored-by-azure-ad-multi-factor-authentication"></a>Persoons gegevens die zijn opgeslagen door Azure AD Multi-Factor Authentication

Persoons gegevens zijn gegevens op gebruikers niveau die aan een specifieke persoon zijn gekoppeld. De volgende gegevens archieven bevatten persoonlijke gegevens:

* Geblokkeerde gebruikers
* Overgeslagen gebruikers
* Wijzigings aanvragen voor het apparaat-token Microsoft Authenticator
* Rapporten van Multi-Factor Authentication activiteiten
* Microsoft Authenticator activeringen

Deze informatie wordt 90 dagen bewaard.

Azure AD Multi-Factor Authentication registreert geen persoonlijke gegevens zoals de gebruikers naam, het telefoon nummer of het IP-adres, maar er is een *UserObjectId* waarmee multi-factor Authentication pogingen voor gebruikers worden geïdentificeerd. Logboek gegevens worden 30 dagen opgeslagen.

### <a name="azure-ad-multi-factor-authentication"></a>Azure AD-Multi-Factor Authentication

Voor open bare Azure-Clouds, met uitzonde ring van Azure B2C-verificatie, NPS-extensie en Windows Server 2016-of 2019 AD FS-adapter, worden de volgende persoons gegevens opgeslagen:

| Gebeurtenistype                           | Type gegevens archief |
|--------------------------------------|-----------------|
| OATH-token                           | In Multi-Factor Authentication-logboeken     |
| Eenrichtings-SMS                          | In Multi-Factor Authentication-logboeken     |
| Spraakoproep                           | In Multi-Factor Authentication-logboeken<br />Gegevens archief van Multi-Factor Authentication-activiteiten rapport<br />Geblokkeerde gebruikers als fraude gerapporteerd |
| Microsoft Authenticator-melding | In Multi-Factor Authentication-logboeken<br />Gegevens archief van Multi-Factor Authentication-activiteiten rapport<br />Geblokkeerde gebruikers als fraude gerapporteerd<br />Wijzigings aanvragen wanneer het token van Microsoft Authenticator wordt gewijzigd |

> [!NOTE]
> Het gegevens archief van het Multi-Factor Authentication-activiteiten rapport wordt opgeslagen in de Verenigde Staten voor alle Clouds, ongeacht de regio die de verificatie aanvraag verwerkt. Microsoft Azure Duitsland, Microsoft Azure beheerd door 21Vianet en micro soft Government Cloud hebben hun eigen onafhankelijke gegevens opslag gescheiden van de gegevens archieven van open bare Cloud regio's, maar deze gegevens worden altijd opgeslagen in de Verenigde Staten.

Voor Microsoft Azure Government, Microsoft Azure Duitsland, Microsoft Azure beheerd door 21Vianet, Azure B2C-verificatie, NPS-extensie en Windows Server 2016-of 2019 AD FS-adapter, worden de volgende persoons gegevens opgeslagen:

| Gebeurtenistype                           | Type gegevens archief |
|--------------------------------------|-----------------|
| OATH-token                           | In Multi-Factor Authentication-logboeken<br />Gegevens archief van Multi-Factor Authentication-activiteiten rapport |
| Eenrichtings-SMS                          | In Multi-Factor Authentication-logboeken<br />Gegevens archief van Multi-Factor Authentication-activiteiten rapport |
| Spraakoproep                           | In Multi-Factor Authentication-logboeken<br />Gegevens archief van Multi-Factor Authentication-activiteiten rapport<br />Geblokkeerde gebruikers als fraude gerapporteerd |
| Microsoft Authenticator-melding | In Multi-Factor Authentication-logboeken<br />Gegevens archief van Multi-Factor Authentication-activiteiten rapport<br />Geblokkeerde gebruikers als fraude gerapporteerd<br />Wijzigings aanvragen wanneer het token van Microsoft Authenticator wordt gewijzigd |

### <a name="multi-factor-authentication-server"></a>Multi-Factor Authentication-server

Als u Azure Multi-Factor Authentication-server implementeert en uitvoert, worden de volgende persoons gegevens opgeslagen:

> [!IMPORTANT]
> Met ingang van 1 juli 2019 biedt micro soft niet langer Multi-Factor Authentication-server voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication van hun gebruikers willen vereisen, moeten gebruikmaken van Azure AD-Multi-Factor Authentication in de Cloud. Bestaande klanten die Multi-Factor Authentication-server voorafgaand aan 1 juli hebben geactiveerd, kunnen de meest recente versie downloaden, toekomstige updates en activerings referenties genereren.

| Gebeurtenistype                           | Type gegevens archief |
|--------------------------------------|-----------------|
| OATH-token                           | In Multi-Factor Authentication-logboeken<br />Gegevens archief van Multi-Factor Authentication-activiteiten rapport |
| Eenrichtings-SMS                          | In Multi-Factor Authentication-logboeken<br />Gegevens archief van Multi-Factor Authentication-activiteiten rapport |
| Spraakoproep                           | In Multi-Factor Authentication-logboeken<br />Gegevens archief van Multi-Factor Authentication-activiteiten rapport<br />Geblokkeerde gebruikers als fraude gerapporteerd |
| Microsoft Authenticator-melding | In Multi-Factor Authentication-logboeken<br />Gegevens archief van Multi-Factor Authentication-activiteiten rapport<br />Geblokkeerde gebruikers als fraude gerapporteerd<br />Wijzigings aanvragen wanneer het token van Microsoft Authenticator wordt gewijzigd |

## <a name="organizational-data-stored-by-azure-ad-multi-factor-authentication"></a>Organisatie gegevens opgeslagen door Azure AD Multi-Factor Authentication

Organisatie gegevens zijn informatie op Tenant niveau die configuratie-of omgevings instellingen beschikbaar kunnen maken. Tenant-instellingen van de volgende Azure Portal Multi-Factor Authentication pagina's kunnen gegevens van de organisatie, zoals drempel waarden voor vergren delingen of beller-id's, opslaan voor binnenkomende aanvragen voor telefoon verificatie:

* Account vergrendeling
* Fraudewaarschuwing
* Meldingen
* Instellingen voor telefoon gesprek

Voor Azure Multi-Factor Authentication-server kunnen de volgende Azure Portal pagina's organisatie gegevens bevatten:

* Serverinstellingen
* Eenmalige bypass
* Regels voor opslaan in cache
* Multi-Factor Authentication-server status

## <a name="log-data-location"></a>Locatie van logboek gegevens

Waar logboek gegevens worden opgeslagen, is afhankelijk van de regio waarin ze worden verwerkt. De meeste geografies hebben systeem eigen Azure AD-Multi-Factor Authentication mogelijkheden, waardoor logboek gegevens worden opgeslagen in dezelfde regio waarin de Multi-Factor Authentication aanvraag wordt verwerkt. In geographs zonder systeem eigen ondersteuning van Azure AD Multi-Factor Authentication worden deze services ondersteund door de geographs van Verenigde Staten of Europa en logboek gegevens worden opgeslagen in dezelfde regio waarin de Multi-Factor Authentication aanvraag wordt verwerkt.

Sommige kern verificatie logboek gegevens worden alleen opgeslagen in de Verenigde Staten. Microsoft Azure Duitsland en Microsoft Azure die worden beheerd door 21Vianet, worden altijd opgeslagen in hun respectieve Cloud. Micro soft Government-Cloud-logboek gegevens worden altijd opgeslagen in de Verenigde Staten.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure ad multi-factor Authentication verzameling van gebruikers gegevens](howto-mfa-reporting-datacollection.md)voor meer informatie over welke gebruikers gegevens worden verzameld door azure AD multi-factor Authentication en Azure multi-factor Authentication-Server op basis van de Cloud.
