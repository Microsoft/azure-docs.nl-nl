---
title: Key Vault .NET 2. x API-opmerkingen bij de release | Microsoft Docs
description: Meer informatie over het bijwerken van apps die zijn geschreven voor eerdere versies van Azure Key Vault om te kunnen werken met de 2,0-versie van de Azure Key Vault-bibliotheek voor C# en .NET.
services: key-vault
author: msmbaldwin
manager: rkarlin
editor: bryanla
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 018570019b306dced76760fefa4441ee7d86ad2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013952"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2,0-opmerkingen bij de release en de migratie handleiding
De volgende informatie helpt u bij het migreren naar de 2,0-versie van de Azure Key Vault-bibliotheek voor C# en .NET.  Apps die zijn geschreven voor eerdere versies moeten worden bijgewerkt ter ondersteuning van de nieuwste versie.  Deze wijzigingen zijn nodig om nieuwe en verbeterde functies, zoals **Key Vault certificaten**, volledig te ondersteunen.

## <a name="key-vault-certificates"></a>Key Vault certificaten

Key Vault certificaten x509-certificaten beheren en ondersteunt het volgende gedrag:  

* Certificaten maken met behulp van een Key Vault aanmaak proces of bestaand certificaat importeren. Dit omvat zowel zelfondertekende als door CERTIFICERINGs instanties gegenereerde certificaten.
* Bewaar en beheer x509-certificaat opslag veilig zonder interactie met behulp van privé sleutel materiaal.  
* Beleids regels definiëren die direct Key Vault voor het beheren van de levens duur van het certificaat.  
* Geef contact gegevens op voor levenscyclus gebeurtenissen, zoals verloop waarschuwingen en verlengings meldingen.  
* Certificaten met geselecteerde verleners (Key Vault partner x509-certificaat providers en certificerings instanties) automatisch vernieuwen. * ondersteunings certificaat van alternatieve (niet-partner) biedt en certificerings instanties (biedt geen ondersteuning voor automatische vernieuwing).  

## <a name="net-support"></a>.NET-ondersteuning

* **.Net 4,0** wordt niet ondersteund door de 2,0-versie van de Azure Key Vault .net-bibliotheek
* **.NET Framework 4.5.2** wordt ondersteund door de 2,0-versie van de Azure Key Vault .net-bibliotheek
* **.NET Standard 1,4** wordt ondersteund door de 2,0-versie van de Azure Key Vault .net-bibliotheek

## <a name="namespaces"></a>Naamruimten

* De naam ruimte voor **modellen** is gewijzigd van **micro soft. Azure.** de sleutel kluis naar **micro soft. Azure. de sleutel kluis. Models**.
* De naam ruimte **micro soft. Azure. de sleutel kluis. Internal** wordt verwijderd.
* De volgende Azure SDK-afhankelijkheden van naam ruimten hebben 

    - **Hyak. common** is nu **micro soft. rest**.
    - **Hyak. internals** is nu **micro soft. rest. serialisatie**.

## <a name="type-changes"></a>Type wijzigingen

* Het *geheim* is gewijzigd in *SecretBundle*
* *Woorden lijst* gewijzigd in *IDictionary*
* *Lijst \<T> , teken reeks []* gewijzigd *in \<T> IList*
* *NextList* is gewijzigd in  *NextPageLink*

## <a name="return-types"></a>Retour typen

* De **lijst** met **SecretList** en de *IPage \<T>* retourneert nu in plaats van *ListKeysResponseMessage*
* De gegenereerde **BackupKeyAsync** retourneert nu *BackupKeyResult*, met de *waarde* (back-upblob). Voorheen werd de methode ingepakt en alleen de waarde geretourneerd.

## <a name="exceptions"></a>Uitzonderingen

* *KeyVaultClientException* is gewijzigd in *KeyVaultErrorException*
* De service fout is gewijzigd van de *uitzonde ring. Fout* bij *uitzonde ring. Body. fout. Message*.
* Er zijn aanvullende gegevens verwijderd uit het fout bericht voor **[JsonExtensionData]**.

## <a name="constructors"></a>Constructors

* In plaats van een *httpclient maakt* te accepteren als een constructor-argument, accepteert de constructor alleen *HttpClientHandler* of *DelegatingHandler []*.

## <a name="downloaded-packages"></a>Gedownloade pakketten

Wanneer een client een Key Vault afhankelijkheid verwerkt, worden de volgende pakketten gedownload:

### <a name="previous-package-list"></a>Vorige pakket lijst

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Lijst met huidige pakketten

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Klassen wijzigingen

* De klasse **UnixEpoch** is verwijderd.
* De naam van de klasse **Base64UrlConverter** is gewijzigd in **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Andere wijzigingen

* Ondersteuning voor de configuratie van het beleid voor opnieuw proberen van KV-bewerkingen voor tijdelijke fouten is toegevoegd aan deze versie van de API.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Micro soft. Azure. Management. NuGet.

* Voor de bewerkingen die een *kluis* hebben geretourneerd, was het retour type een klasse die een **kluis** eigenschap bevat. Het retour type is nu *kluis*.
* *PermissionsToKeys* en *PermissionsToSecrets* zijn nu *machtigingen. sleutels* en *machtigingen. geheimen*
* Bepaalde typen wijzigingen van de retour nering zijn ook van toepassing op het besturings vlak.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Micro soft. Azure. NuGet extensies

* Het pakket is opgedeeld naar **micro soft. Azure. de sleutel kluis. Extensions** en **micro soft. Azure. de sleutel kluis. Cryptography** voor de cryptografische bewerkingen.

