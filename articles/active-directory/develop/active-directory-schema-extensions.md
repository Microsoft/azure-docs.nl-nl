---
title: Kenmerken van Azure AD-schema-uitbrei ding in claims gebruiken
titleSuffix: Microsoft identity platform
description: Hierin wordt beschreven hoe u de kenmerken van Directory schema-extensies gebruikt voor het verzenden van gebruikers gegevens naar toepassingen in token claims.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 0127c8d796126d1e99b1fa38a9506df477c7eb49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755731"
---
# <a name="using-directory-schema-extension-attributes-in-claims"></a>Kenmerken van Directory-schema-extensies in claims gebruiken

Kenmerken van Directory-schema-extensies bieden een manier om aanvullende gegevens op te slaan in Azure Active Directory op gebruikers objecten en andere Directory-objecten, zoals groepen, Tenant Details, service-principals.  Alleen extensie kenmerken voor gebruikers objecten kunnen worden gebruikt voor het verzenden van claims naar toepassingen. In dit artikel wordt beschreven hoe u de kenmerken van Directory schema-extensies gebruikt voor het verzenden van gebruikers gegevens naar toepassingen in token claims.

> [!NOTE]
> Microsoft Graph biedt twee andere uitbreidings mechanismen om grafiek objecten aan te passen. Deze zijn bekend als Microsoft Graph open extensies en Microsoft Graph schema-uitbrei dingen. Raadpleeg de [Microsoft Graph-documentatie](/graph/extensibility-overview) voor meer informatie. Gegevens die zijn opgeslagen op Microsoft Graph objecten die gebruikmaken van deze mogelijkheden, zijn niet beschikbaar als bronnen voor claims in tokens.

Kenmerken van de extensie van het Directory-schema zijn altijd gekoppeld aan een toepassing in de Tenant en ernaar wordt verwezen door de *applicationId* van de toepassing in hun naam.

De id voor een Directory schema-extensie kenmerk is van het formulier *Extension_xxxxxxxxx_AttributeName*.  Waarbij *xxxxxxxxx* de *applicationId* is van de toepassing waarvoor de extensie is gedefinieerd.

## <a name="registering-and-using-directory-schema-extensions"></a>Active Directory-schema-uitbrei dingen registreren en gebruiken
De extensie kenmerken van het Directory-schema kunnen op een van de volgende twee manieren worden geregistreerd en ingevuld:

- Door AD Connect te configureren om ze te maken en gegevens te synchroniseren vanuit on-premises AD. Zie [Azure AD Connect Directory-extensies voor synchronisatie](../hybrid/how-to-connect-sync-feature-directory-extensions.md).
- Als u Microsoft Graph wilt registreren, stelt u de waarden in en leest u uit [schema-uitbrei dingen](/graph/extensibility-overview). [Power shell-cmdlets](/powershell/azure/active-directory/using-extension-attributes-sample) zijn ook beschikbaar.

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-with-ad-connect"></a>Claims verzenden met gegevens uit Directory-schema extensie kenmerken die zijn gemaakt met AD Connect
Kenmerken van Directory-schema-extensies die met AD Connect zijn gemaakt en gesynchroniseerd, worden altijd gekoppeld aan de toepassings-ID die wordt gebruikt door AD Connect. Ze kunnen worden gebruikt als bron voor claims, door ze te configureren als claims in de configuratie van de **bedrijfs toepassingen** in de portal-gebruikers interface voor SAML-toepassingen die zijn geregistreerd met de galerie of de toepassings configuratie van de niet-galerie onder **bedrijfs toepassingen**, en via een claim toewijzings beleid voor toepassingen die zijn geregistreerd via de registratie-ervaring van de toepassing.  Zodra een directory-extensie kenmerk dat is gemaakt via AD Connect zich in de map bevindt, wordt het weer gegeven in de gebruikers interface voor de configuratie van SAML SSO-claims.

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-for-an-application-using-graph-or-powershell"></a>Claims verzenden met gegevens van Directory-schema extensie kenmerken die zijn gemaakt voor een toepassing met behulp van Graph of Power shell
Als een Directory schema-extensie kenmerk is geregistreerd voor een toepassing met behulp van Microsoft Graph of Power shell (via de eerste installatie of het inrichten van een toepassing), kan dezelfde toepassing in Azure Active Directory worden geconfigureerd om gegevens in dat kenmerk te ontvangen van een gebruikers object in een claim wanneer de gebruiker zich aanmeldt.  De toepassing kan worden geconfigureerd voor het ontvangen van gegevens in Directory-schema-uitbrei dingen die zijn geregistreerd op dezelfde toepassing met behulp van [optionele claims](active-directory-optional-claims.md#configuring-directory-extension-optional-claims).  Deze kunnen worden ingesteld in het toepassings manifest.  Hierdoor kan een multi tenant-toepassing de kenmerken van de Directory-schema-extensie registreren voor eigen gebruik. Wanneer de toepassing wordt ingericht in een Tenant, worden de bijbehorende Directory-schema-uitbrei dingen beschikbaar om te worden ingesteld voor gebruikers in die Tenant en worden ze gebruikt.  Zodra de app is geconfigureerd in de Tenant en toestemming is verleend, kan deze worden gebruikt om gegevens op te slaan en op te halen via Graph en te koppelen aan claims in tokens het micro soft Identity-platform voor toepassingen.

De extensie kenmerken van het Directory schema kunnen worden geregistreerd en ingevuld voor elke toepassing.

Als een toepassing claims moet verzenden met gegevens van een extensie kenmerk dat is geregistreerd voor een andere toepassing, moet een [claim toewijzings beleid](active-directory-claims-mapping.md) worden gebruikt om het uitbreidings kenmerk toe te wijzen aan de claim.  Een gemeen schappelijk patroon voor het beheren van extensie kenmerken van Directory schema's is het maken van een toepassing die specifiek het registratie punt moet zijn voor alle schema-uitbrei dingen die u nodig hebt.  Het hoeft geen echte toepassing te zijn en deze techniek houdt in dat alle uitbrei dingen dezelfde toepassings-ID in hun naam hebben.

Dit is bijvoorbeeld een claim toewijzings beleid voor het verzenden van één claim van een uitbreidings kenmerk van een Directory-schema in een OAuth/OIDC-token:

```json
{
    "ClaimsMappingPolicy": {
        "Version": 1,
        "IncludeBasicClaimSet": "false",
        "ClaimsSchema": [{
                "Source": "User",
                "ExtensionID": "extension_xxxxxxx_test",
                "JWTClaimType": "http://schemas.contoso.com/identity/claims/exampleclaim"
            },
        ]
    }
}
```

Waarbij *XXXXXXX* de toepassings-id is waarmee de extensie is geregistreerd.

> [!TIP]
> Hoofdletter consistentie is belang rijk bij het instellen van Directory-extensie kenmerken voor objecten.  Uitbreidings kenmerk namen zijn niet hoofdletter gevoelig wanneer ze worden ingesteld, maar zijn hoofdletter gevoelig wanneer ze door de token service worden gelezen uit de Directory.  Als een uitbreidings kenmerk is ingesteld voor een gebruikers object met de naam LegacyId en een ander gebruikers object met de naam LegacyId, wanneer het kenmerk wordt toegewezen aan een claim met de naam ' LegacyId ', worden de gegevens opgehaald en de claim die is opgenomen in het token voor de eerste gebruiker, maar niet de tweede.
>
> De id-para meter in het claim schema dat wordt gebruikt voor ingebouwde adreslijst kenmerken is ' ExtensionID ' voor Directory-extensie kenmerken.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [toevoegen van aangepaste of extra claims aan de JWT-tokens (SAML 2,0 en JSON Web tokens)](active-directory-optional-claims.md).
- Meer informatie over het aanpassen van claims die worden [verzonden in tokens voor een specifieke app](active-directory-claims-mapping.md).