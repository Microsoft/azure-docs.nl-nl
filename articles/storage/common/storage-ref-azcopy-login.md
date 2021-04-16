---
title: azcopy login | Microsoft Docs
description: Dit artikel bevat naslaginformatie voor de opdracht azcopy login.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e4861749d66e466bc3302553af8b3ed4919a72e0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503232"
---
# <a name="azcopy-login"></a>azcopy login

Meldt u aan bij Azure Active Directory toegang tot Azure Storage resources.

## <a name="synopsis"></a>Synopsis

Meld u aan bij Azure Active Directory toegang tot Azure Storage resources.

Als u wilt worden geautoriseerd voor uw Azure Storage-account, moet u de rol Bijdrager voor opslagblobgegevens toewijzen aan uw gebruikersaccount in de context van het opslagaccount, de bovenliggende resourcegroep of het bovenliggende abonnement. 

Met deze opdracht worden versleutelde aanmeldingsgegevens voor de huidige gebruiker in de cache opgeslagen met behulp van de ingebouwde mechanismen van het besturingssysteem.

> [!IMPORTANT]
> Als u een omgevingsvariabele in stelt met behulp van de opdrachtregel, kan die variabele worden gelezen in de opdrachtregelgeschiedenis. U kunt variabelen wissen die referenties uit de opdrachtregelgeschiedenis bevatten. Als u wilt voorkomen dat variabelen in uw geschiedenis worden weergegeven, kunt u een script gebruiken om de gebruiker om referenties te vragen en de omgevingsvariabele in te stellen.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Zelfstudie: On-premises gegevens naar de cloudopslag migreren met AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)

## <a name="examples"></a>Voorbeelden

Interactief aanmelden met de standaard-id van de AAD-tenant ingesteld op algemeen:

```azcopy
azcopy login
```

Interactief aanmelden met een opgegeven tenant-id:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Meld u aan met de door het systeem toegewezen identiteit van een virtuele machine (VM):

```azcopy
azcopy login --identity
```

Meld u aan met de door de gebruiker toegewezen identiteit van een VM en een client-id van de service-identiteit:
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
Meld u aan met behulp van de door de gebruiker toegewezen identiteit van een VM en een object-id van de service-identiteit:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Meld u aan met de door de gebruiker toegewezen identiteit van een VM en een resource-id van de service-identiteit:
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Meld u aan als een service-principal met behulp van een clientgeheim: stel de omgevingsvariabele AZCOPY_SPA_CLIENT_SECRET in op het clientgeheim voor de service-principalverlening op basis van geheimen.

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

Meld u aan als service-principal met behulp van een certificaat en het wachtwoord:

Stel de omgevingsvariabele AZCOPY_SPA_CERT_PASSWORD in op het wachtwoord van het certificaat voor op certificaten gebaseerde service-principal-auth:

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

Behandelen `/path/to/my/cert` als een pad naar een PEM- of PKCS12-bestand. AzCopy bereikt het systeemcertificaatopslag niet om uw certificaat te verkrijgen.

`--certificate-path` is verplicht bij het gebruik van een op certificaten gebaseerde service-principal-auth.

## <a name="options"></a>Opties

**--aad-endpoint string** The Azure Active Directory endpoint to use. De standaardwaarde https://login.microsoftonline.com) ( is juist voor de wereldwijde Azure-cloud. Stel deze parameter in bij het authenticeren in een nationale cloud. Niet nodig voor Managed Service Identity.

**--application-id** string Application ID of user-assigned identity. Vereist voor service-principal-auth.

**--certificaat-pad tekenreeks** pad naar certificaat voor SPN-verificatie. Vereist voor op certificaten gebaseerde service-principal-auth.

**--help**   help voor de `azcopy login` opdracht .

**--identity**   Meld u aan met behulp van de identiteit van de virtuele machine, ook wel managed service identity (MSI) genoemd.

**--identity-client-id** string Client ID of user-assigned identity.

**--identity-object-id** string Object ID of user-assigned identity.

**--identity-resource-id** string Resource ID of user-assigned identity.

**--service-principal**   Meld u aan via Service Principal Name (SPN) met behulp van een certificaat of een geheim. Het clientgeheim of certificaatwachtwoord moet in de juiste omgevingsvariabele worden geplaatst. Typ AzCopy env om de namen en beschrijvingen van omgevingsvariabelen te bekijken.

**--tenant-id string** The Azure Active Directory tenant ID to use for OAuth device interactive login.

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps float|Beperk de overdrachtssnelheid, in megabits per seconde. De doorvoer per moment kan enigszins afwijken van de limiet. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgekapt.|
|--output-type string|Indeling van de uitvoer van de opdracht. De keuzes zijn onder andere: text, json. De standaardwaarde is 'text'.|
|--trusted-microsoft-achtervoegsels tekenreeks   |Hiermee geeft u extra domeinachtervoegsels op waar Azure Active Directory aanmeldingstokens kunnen worden verzonden.  De standaardwaarde is '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Alle die hier worden vermeld, worden toegevoegd aan de standaardinstelling. Voor beveiliging moet u hier alleen Microsoft Azure zetten. Scheid meerdere vermeldingen met punt-dubbele punts.|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
