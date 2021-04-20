---
title: Key Vault opmerkingen bij de release van de .NET 2.x-API| Microsoft Docs
description: Informatie over het bijwerken van apps die zijn geschreven voor eerdere versies van Azure Key Vault om te werken met de 2.0-versie van de Azure Key Vault-bibliotheek voor C# en .NET.
services: key-vault
author: msmbaldwin
editor: bryanla
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: b923cc289dc0229f18eba144b09ceb34e0edea4e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751882"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2.0 - Opmerkingen bij de release en migratiehandleiding
De volgende informatie helpt bij het migreren naar de 2.0-versie van de Azure Key Vault-bibliotheek voor C# en .NET.  Apps die zijn geschreven voor eerdere versies, moeten worden bijgewerkt om de nieuwste versie te ondersteunen.  Deze wijzigingen zijn nodig om nieuwe en verbeterde functies, zoals de Key Vault **ondersteunen.**

## <a name="key-vault-certificates"></a>Key Vault certificaten

Key Vault certificaten beheren x509-certificaten en ondersteunen het volgende gedrag:  

* Certificaten maken via een Key Vault maken of bestaand certificaat importeren. Dit omvat zowel zelfonder ondertekende als door de certificeringsinstantie (CA) gegenereerde certificaten.
* Opslag van x509-certificaten veilig opslaan en beheren zonder interactie met behulp van persoonlijk sleutelmateriaal.  
* Definieer beleidsregels die Key Vault voor het beheren van de levenscyclus van het certificaat.  
* Geef contactgegevens op voor levenscyclusgebeurtenissen, zoals waarschuwingen over verlopen en verlengingsmeldingen.  
* Automatisch certificaten vernieuwen met geselecteerde verleners (Key Vault partner X509-certificaatproviders en certificeringsinstanties).* Ondersteuningscertificaat van alternatieve (niet-partner) biedt en certificeringsinstanties (biedt geen ondersteuning voor automatisch verlengen).  

## <a name="net-support"></a>.NET-ondersteuning

* **.NET 4.0** wordt niet ondersteund door de 2.0-versie van de Azure Key Vault .NET-bibliotheek
* **.NET Framework versie 4.5.2** wordt ondersteund door de 2.0-versie van de Azure Key Vault .NET-bibliotheek
* **.NET Standard 1.4** wordt ondersteund door de 2.0-versie van de Azure Key Vault .NET-bibliotheek

## <a name="namespaces"></a>Naamruimten

* De naamruimte voor **modellen** is gewijzigd van **Microsoft.Azure.KeyVault** in **Microsoft.Azure.KeyVault.Models.**
* De **naamruimte Microsoft.Azure.KeyVault.Internal** is uitgevallen.
* De volgende Azure SDK-afhankelijkheden hebben 

    - **Hyak.Common** is nu **Microsoft.Rest.**
    - **Hyak.Common.Internals** is nu **Microsoft.Rest.Serialization.**

## <a name="type-changes"></a>Typewijzigingen

* *Geheim* gewijzigd in *SecretBundle*
* *Woordenlijst* gewijzigd in *IDictionary*
* *Lijst \<T> , tekenreeks []* gewijzigd in *IList \<T>*
* *NextList* gewijzigd in  *NextPageLink*

## <a name="return-types"></a>Retourtypen

* **KeyList** en **SecretList** retourneren nu *IPage \<T>* in plaats *van ListKeysResponseMessage*
* De **gegenereerde BackupKeyAsync** retourneert nu *BackupKeyResult,* dat *Value* (back-up-blob) bevat. Voorheen werd de methode verpakt en alleen de waarde geretourneerd.

## <a name="exceptions"></a>Uitzonderingen

* *KeyVaultClientException* is gewijzigd in *KeyVaultErrorException*
* De servicefout is gewijzigd van *uitzondering. Fout bij* *uitzondering. Body.Error.Message.*
* Aanvullende informatie verwijderd uit het foutbericht **voor [JsonExtensionData]**.

## <a name="constructors"></a>Constructors

* In plaats van een *HttpClient* als constructorargument te accepteren, accepteert de constructor alleen *HttpClientHandler* of *DelegatingHandler[]*.

## <a name="downloaded-packages"></a>Gedownloade pakketten

Wanneer een client een Key Vault verwerkt, worden de volgende pakketten gedownload:

### <a name="previous-package-list"></a>Vorige pakketlijst

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Huidige pakketlijst

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Klassewijzigingen

* **De UnixEpoch-klasse** is verwijderd.
* De naam van de klasse **Base64UrlConverter** is gewijzigd in **Base64UrlJsonConverter.**

## <a name="other-changes"></a>Andere wijzigingen

* Ondersteuning voor de configuratie van het beleid voor opnieuw proberen van KV-bewerking bij tijdelijke fouten is toegevoegd aan deze versie van de API.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Voor de bewerkingen die een kluis hebben *geretourneerd,* is het retourtype een klasse die een **kluis-eigenschap** bevat. Het retourtype is nu *Kluis*.
* *PermissionsToKeys* en *PermissionsToSecrets* zijn nu *Permissions.Keys* en *Permissions.Secrets*
* Bepaalde wijzigingen in retourtypen zijn ook van toepassing op het besturingsvlak.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* Het pakket wordt opgesplitst in **Microsoft.Azure.KeyVault.Extensions** en **Microsoft.Azure.KeyVault.Cryptography** voor de cryptografiebewerkingen.

