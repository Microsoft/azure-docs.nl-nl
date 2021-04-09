---
title: azcopy-aanmelding | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de azcopy-aanmeld opdracht.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e4740870dd2d9748aad55150ce1946e3eb666619
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878355"
---
# <a name="azcopy-login"></a>azcopy login

Meldt zich aan bij Azure Active Directory om toegang te krijgen tot Azure Storage resources.

## <a name="synopsis"></a>Samen vatting

Meld u aan bij Azure Active Directory om toegang te krijgen tot Azure Storage resources.

Als u wilt worden gemachtigd voor uw Azure Storage-account, moet u de rol van BLOB-gegevens van de **opslag-blobopslag** toewijzen aan uw gebruikers account in de context van het opslag account, de bovenliggende resource groep of het bovenliggende abonnement.

Met deze opdracht worden de versleutelde aanmeldings gegevens voor de huidige gebruiker in de cache opgeslagen met de ingebouwde mechanismen van het besturings systeem.

> [!IMPORTANT]
> Als u een omgevings variabele instelt met behulp van de opdracht regel, wordt die variabele leesbaar in de opdracht regel geschiedenis. U kunt variabelen met referenties uit de opdracht regel geschiedenis wissen. Als u wilt voor komen dat variabelen in uw geschiedenis worden weer gegeven, kunt u een script gebruiken om de gebruiker om referenties te vragen en de omgevings variabele in te stellen.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [Configureren, optimaliseren en problemen oplossen in AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Voorbeelden

Interactief aanmelden met de standaard-Tenant-ID voor AAD die is ingesteld op algemeen:

```azcopy
azcopy login
```

Interactief aanmelden met een opgegeven Tenant-ID:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Meld u aan met de door het systeem toegewezen identiteit van een virtuele machine (VM):

```azcopy
azcopy login --identity
```

Meld u aan met de door de gebruiker toegewezen identiteit van een virtuele machine en een client-ID van de service-identiteit:
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
Meld u aan met de door de gebruiker toegewezen identiteit van een virtuele machine en een object-ID van de service-identiteit:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Meld u aan met de door de gebruiker toegewezen identiteit van een virtuele machine en een resource-ID van de service-identiteit:
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Meld u aan als service-principal met behulp van een client geheim: Stel de omgevings variabele AZCOPY_SPA_CLIENT_SECRET in op het client geheim voor de geheim verificatie van de Service-Principal.

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

Meld u aan als service-principal met behulp van een certificaat en het wacht woord:

Stel de omgevings variabele AZCOPY_SPA_CERT_PASSWORD in op het wacht woord van het certificaat voor Service-Principal auth op basis van certificaten:

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

Behandelen `/path/to/my/cert` als een pad naar een PEM-of pkcs12/pfx-profiel-bestand. AzCopy is niet bereikbaar in het systeem certificaat archief om uw certificaat te verkrijgen.

`--certificate-path` is verplicht bij het uitvoeren van de Service-Principal auth op basis van certificaten.

## <a name="options"></a>Opties

**--Aad-eind punt** teken reeks het Azure Active Directory-eind punt dat moet worden gebruikt. De standaard waarde ( https://login.microsoftonline.com) is juist voor de algemene Azure-Cloud. Stel deze para meter in wanneer u een verificatie uitvoert in een nationale Cloud. Niet nodig voor Managed Service Identity.

**--** toepassings-id teken reeks toepassings-id van de door de gebruiker toegewezen identiteit. Vereist voor Service Principal auth.

**--** pad naar het pad naar het certificaat voor SPN-verificatie. Vereist voor verificatie op basis van een service-principal voor certificaten.

**--Help**   voor de `azcopy login` opdracht.

**--identiteit**   Meld u aan met de identiteit van de virtuele machine, ook wel Managed Service Identity (MSI) genoemd.

**--identiteit-client-id** teken reeks-id van de door de gebruiker toegewezen identiteit.

**--Identity-object-id** teken reeks object-id van door de gebruiker toegewezen identiteit.

**--** id van de bron teken reeks van de identiteit van de gebruiker toegewezen identiteit.

**--Service-Principal**   Meld u aan via service principal name (SPN) met behulp van een certificaat of een geheim. Het client geheim of het certificaat wachtwoord moet in de juiste omgevings variabele worden geplaatst. Typ AzCopy env om namen en beschrijvingen van omgevings variabelen weer te geven.

**--Tenant-id** teken reeks de Azure Active Directory Tenant-id die moet worden gebruikt voor de interactieve aanmelding van OAuth-apparaten.

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--Cap-Mbps-float|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|
|--vertrouwd-micro soft-achtervoegsels teken reeks   |Hiermee geeft u aanvullende domein achtervoegsels op waar Azure Active Directory aanmeldings tokens kunnen worden verzonden.  De standaard waarde is *. core.Windows.net;*. core.chinacloudapi.cn; *. core.cloudapi.de;*. core.usgovcloudapi.net '. Alle hier vermelde waarden worden toegevoegd aan de standaard instelling. Voor beveiliging moet u Microsoft Azure domeinen hier alleen plaatsen. Scheid meerdere vermeldingen met een punt komma.|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
