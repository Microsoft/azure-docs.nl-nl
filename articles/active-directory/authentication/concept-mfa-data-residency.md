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
ms.openlocfilehash: 788a666e8ec509bbd29a8dbd503a60b3dddefd6b
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208349"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Gegevens locatie en klant gegevens voor Azure AD multi-factor Authentication

Klant gegevens worden door Azure AD opgeslagen op een geografische locatie op basis van het adres van uw organisatie bij het abonneren op een micro soft online service, zoals Microsoft 365 en Azure. Voor informatie over waar uw klant gegevens worden opgeslagen, kunt u de sectie [waar bevinden zich uw gegevens?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) in het micro soft vertrouwens centrum.

Cloud-based Azure AD multi-factor Authentication en Azure AD-server proces voor multi-factor Authentication en Store een aantal persoons gegevens en bedrijfs gegevens. In dit artikel wordt beschreven wat en waar gegevens worden opgeslagen.

De Azure AD multi-factor Authentication-service heeft data centers in de Verenigde Staten, Europa en Azië en Stille Oceaan. De volgende activiteiten zijn afkomstig uit de regionale data centers, tenzij anders vermeld:

* Multi-factor Authentication met telefoon gesprekken afkomstig van Amerikaanse data centers en wordt gerouteerd door wereld wijde providers.
* Verificatie aanvragen voor algemeen gebruik van andere regio's, zoals Europa of Australië, worden momenteel verwerkt op basis van de locatie van de gebruiker.
* Push meldingen met behulp van de app Microsoft Authenticator worden momenteel verwerkt in de regionale data centers op basis van de locatie van de gebruiker.
    * Leverancierspecifieke services van apparaten, zoals Apple Push meldingen, kunnen buiten de locatie van de gebruiker vallen.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Persoons gegevens die zijn opgeslagen door Azure AD multi-factor Authentication

Persoons gegevens zijn gegevens op gebruikers niveau die aan een specifieke persoon zijn gekoppeld. De volgende gegevens archieven bevatten persoonlijke gegevens:

* Geblokkeerde gebruikers
* Overgeslagen gebruikers
* Wijzigings aanvragen voor het apparaat-token Microsoft Authenticator
* Rapporten voor multi-factor Authentication-activiteiten
* Microsoft Authenticator activeringen

Deze informatie wordt 90 dagen bewaard.

Azure AD multi-factor Authentication registreert geen persoonlijke gegevens zoals de gebruikers naam, het telefoon nummer of het IP-adres, maar er is een *UserObjectId* die multi-factor Authentication-pogingen voor gebruikers identificeert. Logboek gegevens worden 30 dagen opgeslagen.

### <a name="azure-ad-multifactor-authentication"></a>Azure AD multi-factor Authentication

Voor open bare Azure-Clouds, met uitzonde ring van Azure B2C-verificatie, NPS-extensie en Windows Server 2016-of 2019 AD FS-adapter, worden de volgende persoons gegevens opgeslagen:

| Gebeurtenistype                           | Type gegevens archief |
|--------------------------------------|-----------------|
| OATH-token                           | Bij multi-factor Authentication-logboeken     |
| Eenrichtings-SMS                          | Bij multi-factor Authentication-logboeken     |
| Spraakoproep                           | Bij multi-factor Authentication-logboeken<br />Gegevens Archief voor multi-factor Authentication-activiteit rapport<br />Geblokkeerde gebruikers als fraude gerapporteerd |
| Microsoft Authenticator-melding | Bij multi-factor Authentication-logboeken<br />Gegevens Archief voor multi-factor Authentication-activiteit rapport<br />Geblokkeerde gebruikers als fraude gerapporteerd<br />Wijzigings aanvragen wanneer het token van Microsoft Authenticator wordt gewijzigd |

Voor Microsoft Azure Government, Microsoft Azure Duitsland, Microsoft Azure beheerd door 21Vianet, Azure B2C-verificatie, NPS-extensie en Windows Server 2016-of 2019 AD FS-adapter, worden de volgende persoons gegevens opgeslagen:

| Gebeurtenistype                           | Type gegevens archief |
|--------------------------------------|-----------------|
| OATH-token                           | Bij multi-factor Authentication-logboeken<br />Gegevens Archief voor multi-factor Authentication-activiteit rapport |
| Eenrichtings-SMS                          | Bij multi-factor Authentication-logboeken<br />Gegevens Archief voor multi-factor Authentication-activiteit rapport |
| Spraakoproep                           | Bij multi-factor Authentication-logboeken<br />Gegevens Archief voor multi-factor Authentication-activiteit rapport<br />Geblokkeerde gebruikers als fraude gerapporteerd |
| Microsoft Authenticator-melding | Bij multi-factor Authentication-logboeken<br />Gegevens Archief voor multi-factor Authentication-activiteit rapport<br />Geblokkeerde gebruikers als fraude gerapporteerd<br />Wijzigings aanvragen wanneer het token van Microsoft Authenticator wordt gewijzigd |

### <a name="multifactor-authentication-server"></a>Multi-factor Authentication-Server

Als u Azure AD multi-factor Authentication-Server implementeert en uitvoert, worden de volgende persoons gegevens opgeslagen:

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt micro soft geen multi-factor Authentication-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication van hun gebruikers willen vereisen, moeten gebruikmaken van Azure AD-authenticatie op basis van de Cloud. Bestaande klanten die de multi-factor Authentication-Server voorafgaand aan 1 juli hebben geactiveerd, kunnen de meest recente versie downloaden, toekomstige updates en activerings referenties genereren.

| Gebeurtenistype                           | Type gegevens archief |
|--------------------------------------|-----------------|
| OATH-token                           | Bij multi-factor Authentication-logboeken<br />Gegevens Archief voor multi-factor Authentication-activiteit rapport |
| Eenrichtings-SMS                          | Bij multi-factor Authentication-logboeken<br />Gegevens Archief voor multi-factor Authentication-activiteit rapport |
| Spraakoproep                           | Bij multi-factor Authentication-logboeken<br />Gegevens Archief voor multi-factor Authentication-activiteit rapport<br />Geblokkeerde gebruikers als fraude gerapporteerd |
| Microsoft Authenticator-melding | Bij multi-factor Authentication-logboeken<br />Gegevens Archief voor multi-factor Authentication-activiteit rapport<br />Geblokkeerde gebruikers als fraude gerapporteerd<br />Wijzigings aanvragen wanneer het token van Microsoft Authenticator wordt gewijzigd |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Organisatie gegevens opgeslagen door Azure AD multi-factor Authentication

Organisatie gegevens zijn informatie op Tenant niveau die configuratie-of omgevings instellingen beschikbaar kunnen maken. Tenant-instellingen van de volgende Azure Portal multi-factor Authentication-pagina's kunnen bedrijfs gegevens opslaan, zoals drempel waarden voor vergren delingen of beller-ID-gegevens voor binnenkomende aanvragen voor telefoon verificatie:

* Account vergrendeling
* Fraudewaarschuwing
* Meldingen
* Instellingen voor telefoon gesprek

En voor Azure AD multi-factor Authentication-Server kunnen de volgende Azure Portal pagina's gegevens van de organisatie bevatten:

* Serverinstellingen
* Eenmalige bypass
* Regels voor opslaan in cache
* Status van de multi-factor Authentication-Server

## <a name="multifactor-authentication-logs-location"></a>Locatie van Logboeken voor multi-factor Authentication

De volgende tabel bevat de locatie voor service logboeken voor open bare Clouds.

| Openbare cloud| Aanmeldingslogboeken | Rapport activiteit multi-factor Authentication        | Logboeken voor multi-factor Authentication-Service       |
|-------------|--------------|----------------------------------------|------------------------|
| VS          | VS           | VS                                     | VS                     |
| Europa      | Europa       | VS                                     | Europa <sup>2</sup>    |
| Australië   | Australië    | VS<sup>1</sup>                         | Australië <sup>2</sup> |

<sup>1</sup> OATH-code logboeken worden opgeslagen in Australië

<sup>2</sup> Spraak aanroepen multi-factor Authentication-Service logboeken worden opgeslagen in de Verenigde Staten

De volgende tabel toont de locatie voor service logboeken voor soevereine Clouds.

| Onafhankelijke cloud                      | Aanmeldingslogboeken                         | Rapport activiteit multi-factor Authentication (inclusief persoonlijke gegevens)| Logboeken voor multi-factor Authentication-Service |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Duitsland              | Duitsland                              | VS                            | VS               |
| Microsoft Azure beheerd door 21Vianet | China                                | VS                            | VS               |
| Micro soft Government-Cloud           | VS                                   | VS                            | VS               |

De gegevens van het rapport met multi-factor Authentication-activiteiten bevatten persoonlijke gegevens zoals user principal name (UPN) en het volledige telefoon nummer.

De logboeken voor multi-factor Authentication-service worden gebruikt om de service te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie Azure AD multi-factor [Authentication-gebruikers gegevens verzameling](howto-mfa-reporting-datacollection.md)voor meer informatie over welke gebruikers gegevens worden verzameld door Azure AD multi-factor Authentication en de Azure AD multi-factor Authentication-Server.
