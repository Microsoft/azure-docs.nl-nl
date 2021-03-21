---
title: Azure AD multi-factor Authentication-gegevens locatie
description: Meer informatie over persoonlijke en organisatie gegevens Azure AD multi-factor Authentication-archieven voor u en uw gebruikers en welke gegevens binnen het land of de regio van herkomst blijven.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7381ab62eb39c555c6b4eb34f150fc71bea1f10f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561461"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Gegevens locatie en klant gegevens voor Azure AD multi-factor Authentication

Azure Active Directory (Azure AD) slaat klant gegevens op in een geografische locatie op basis van het adres dat een organisatie biedt als u zich abonneert op een online service van micro soft, zoals Microsoft 365 of Azure. Zie [waar bevinden zich uw gegevens](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) in het micro soft vertrouwens centrum voor meer informatie over waar uw klant gegevens worden opgeslagen.

Cloud-based Azure AD multi-factor Authentication en Azure multifactor Authentication server verwerken en opslaan van persoonlijke gegevens en bedrijfs gegevens. In dit artikel wordt beschreven wat en waar gegevens worden opgeslagen.

De Azure AD multi-factor Authentication-service heeft data centers in de Verenigde Staten, Europa en Azië en Stille Oceaan. De volgende activiteiten zijn afkomstig uit de regionale data centers, tenzij anders vermeld:

* Multi-factor Authentication-telefoon gesprekken zijn afkomstig van Verenigde Staten-data centers en worden gerouteerd door globale providers.
* Verificatie aanvragen voor algemeen gebruik van andere regio's worden momenteel verwerkt op basis van de locatie van de gebruiker.
* Push meldingen die gebruikmaken van de app Microsoft Authenticator worden momenteel verwerkt in regionale data centers op basis van de locatie van de gebruiker. Leverancierspecifieke apparaten, zoals Apple Push Notification Service, kunnen zich buiten de locatie van de gebruiker bevinden.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Persoons gegevens die zijn opgeslagen door Azure AD multi-factor Authentication

Persoonlijke gegevens zijn gegevens op gebruikers niveau die zijn gekoppeld aan een specifieke persoon. De volgende gegevens archieven bevatten persoonlijke gegevens:

* Geblokkeerde gebruikers
* Overgeslagen gebruikers
* Wijzigings aanvragen voor het apparaat-token Microsoft Authenticator
* Rapporten voor multi-factor Authentication-activiteiten
* Microsoft Authenticator activeringen

Deze informatie wordt 90 dagen bewaard.

Azure AD multi-factor Authentication registreert geen persoonlijke gegevens zoals gebruikers namen, telefoon nummers of IP-adressen. *UserObjectId* identificeert echter verificatie pogingen aan gebruikers. Logboek gegevens worden 30 dagen opgeslagen.

### <a name="data-stored-by-azure-ad-multifactor-authentication"></a>Gegevens opgeslagen door Azure AD multi-factor Authentication

Voor open bare Azure-Clouds, met uitzonde ring van Azure AD B2C verificatie, de NPS-extensie en de Windows Server 2016-of 2019 Active Directory Federation Services-adapter (AD FS), worden de volgende persoons gegevens opgeslagen:

| Gebeurtenistype                           | Type gegevens archief |
|--------------------------------------|-----------------|
| OATH-token                           | Multi-factor Authentication-logboeken     |
| Eenrichtings-SMS                          | Multi-factor Authentication-logboeken     |
| Spraakoproep                           | Multi-factor Authentication-logboeken<br/>Gegevens Archief voor multi-factor Authentication-activiteit rapport<br/>Geblokkeerde gebruikers (als fraude is gerapporteerd) |
| Microsoft Authenticator-melding | Multi-factor Authentication-logboeken<br/>Gegevens Archief voor multi-factor Authentication-activiteit rapport<br/>Geblokkeerde gebruikers (als fraude is gerapporteerd)<br/>Wijzigings aanvragen wanneer het token van het Microsoft Authenticator-apparaat wordt gewijzigd |

Voor Microsoft Azure Government, Microsoft Azure Duitsland, Microsoft Azure beheerd door 21Vianet, Azure AD B2C verificatie, de NPS-extensie en de Windows Server 2016-of 2019 AD FS-adapter, worden de volgende persoons gegevens opgeslagen:

| Gebeurtenistype                           | Type gegevens archief |
|--------------------------------------|-----------------|
| OATH-token                           | Multi-factor Authentication-logboeken<br/>Gegevens Archief voor multi-factor Authentication-activiteit rapport |
| Eenrichtings-SMS                          | Multi-factor Authentication-logboeken<br/>Gegevens Archief voor multi-factor Authentication-activiteit rapport |
| Spraakoproep                           | Multi-factor Authentication-logboeken<br/>Gegevens Archief voor multi-factor Authentication-activiteit rapport<br/>Geblokkeerde gebruikers (als fraude is gerapporteerd) |
| Microsoft Authenticator-melding | Multi-factor Authentication-logboeken<br/>Gegevens Archief voor multi-factor Authentication-activiteit rapport<br/>Geblokkeerde gebruikers (als fraude is gerapporteerd)<br/>Wijzigings aanvragen wanneer het token van het Microsoft Authenticator-apparaat wordt gewijzigd |

### <a name="data-stored-by-azure-multifactor-authentication-server"></a>Gegevens opgeslagen door de Azure multi-factor Authentication-Server

Als u Azure multi-factor Authentication-Server gebruikt, worden de volgende persoons gegevens opgeslagen.

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt micro soft geen multi-factor Authentication-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication van hun gebruikers willen vereisen, moeten gebruikmaken van Azure AD-authenticatie op basis van de Cloud. Bestaande klanten die een multi-factor Authentication-server hebben geactiveerd vóór 1 juli 2019, kunnen de meest recente versie en updates downloaden en de activerings referenties zo gebruikelijk genereren.

| Gebeurtenistype                           | Type gegevens archief |
|--------------------------------------|-----------------|
| OATH-token                           | Multi-factor Authentication-logboeken<br />Gegevens Archief voor multi-factor Authentication-activiteit rapport |
| Eenrichtings-SMS                          | Multi-factor Authentication-logboeken<br />Gegevens Archief voor multi-factor Authentication-activiteit rapport |
| Spraakoproep                           | Multi-factor Authentication-logboeken<br />Gegevens Archief voor multi-factor Authentication-activiteit rapport<br />Geblokkeerde gebruikers (als fraude is gerapporteerd) |
| Microsoft Authenticator-melding | Multi-factor Authentication-logboeken<br />Gegevens Archief voor multi-factor Authentication-activiteit rapport<br />Geblokkeerde gebruikers (als fraude is gerapporteerd)<br />Wijzigings aanvragen wanneer het token van Microsoft Authenticator wordt gewijzigd |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Organisatie gegevens opgeslagen door Azure AD multi-factor Authentication

Organisatie gegevens zijn informatie op Tenant niveau waarmee configuratie-of omgevings instellingen kunnen worden weer gegeven. Tenant-instellingen van de volgende Azure Portal multi-factor Authentication-pagina's kunnen gegevens van de organisatie bevatten, zoals drempel waarden voor vergren delingen of beller-ID-gegevens voor binnenkomende aanvragen voor telefoon verificatie:

* Account vergrendeling
* Fraudewaarschuwing
* Meldingen
* Instellingen voor telefoon gesprek

Voor de Azure multi-factor Authentication-Server kunnen de volgende Azure Portal pagina's gegevens van de organisatie bevatten:

* Serverinstellingen
* Eenmalige bypass
* Regels voor opslaan in cache
* Status van de multi-factor Authentication-Server

## <a name="multifactor-authentication-logs-location"></a>Locatie van Logboeken voor multi-factor Authentication

De volgende tabel bevat de locatie voor service logboeken voor open bare Clouds.

| Openbare cloud| Aanmeldingslogboeken | Rapport activiteit multi-factor Authentication        | Logboeken voor multi-factor Authentication-Service       |
|-------------|--------------|----------------------------------------|------------------------|
| Verenigde Staten          | Verenigde Staten           | Verenigde Staten                                     | Verenigde Staten                     |
| Europa      | Europa       | Verenigde Staten                                     | Europa <sup>2</sup>    |
| Australië   | Australië    | Verenigde Staten<sup>1</sup>                         | Australië <sup>2</sup> |

<sup>1</sup> OATH-code logboeken worden opgeslagen in Australië.

<sup>2</sup> Spraak aanroepen multi-factor Authentication-Service logboeken worden opgeslagen in de Verenigde Staten.

De volgende tabel toont de locatie voor service logboeken voor soevereine Clouds.

| Onafhankelijke cloud                      | Aanmeldingslogboeken                         | Rapport activiteit multi-factor Authentication (inclusief persoonlijke gegevens)| Logboeken voor multi-factor Authentication-Service |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Duitsland              | Duitsland                              | Verenigde Staten                            | Verenigde Staten               |
| Azure China 21Vianet                 | China                                | Verenigde Staten                            | Verenigde Staten               |
| Micro soft Government-Cloud           | Verenigde Staten                                   | Verenigde Staten                            | Verenigde Staten               |

De rapporten voor multi-factor Authentication-activiteiten bevatten persoonlijke gegevens, zoals User Principal Name (UPN) en het volledige telefoon nummer.

De logboeken voor multi-factor Authentication-service worden gebruikt om de service te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie Azure AD multi-factor [Authentication-gebruikers gegevens verzameling](howto-mfa-reporting-datacollection.md)voor meer informatie over welke gebruikers gegevens worden verzameld door Azure AD multi-factor Authentication en de Azure multi-factor Authentication-Server.
