---
title: Overzicht van beleidssleutels - Azure Active Directory B2C
description: Meer informatie over de typen versleutelingsbeleidssleutels die kunnen worden gebruikt in Azure Active Directory B2C voor het ondertekenen en valideren van tokens, clientgeheimen, certificaten en wachtwoorden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a41717e9be0918dead9f77a5f5472494d734b38a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717527"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Overzicht van beleidssleutels in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) slaat geheimen en certificaten op in de vorm van beleidssleutels om vertrouwen te krijgen in de services waarin het is geïntegreerd. Deze vertrouwensrelatie bestaat uit:

- Externe id-providers
- Verbinding maken met [REST API services](restful-technical-profile.md)
- Token-ondertekening en -versleuteling

 In dit artikel wordt beschreven wat u moet weten over de beleidssleutels die worden gebruikt door Azure AD B2C.

> [!NOTE]
> Momenteel is de configuratie van beleidssleutels beperkt tot [alleen aangepaste](./user-flow-overview.md) beleidsregels.

U kunt geheimen en certificaten configureren om een vertrouwensrelatie tussen services tot stand te Azure Portal onder het menu **Beleidssleutels.** Sleutels kunnen symmetrisch of asymmetrisch zijn. *Symmetrische* cryptografie, of cryptografie met persoonlijke sleutels, is waar een gedeeld geheim wordt gebruikt om de gegevens te versleutelen en ontsleutelen. *Asymmetrische* cryptografie, of openbare-sleutelcryptografie, is een cryptografisch systeem dat gebruikmaakt van sleutelsparen, bestaande uit openbare sleutels die worden gedeeld met de relying party-toepassing en persoonlijke sleutels die alleen bekend zijn bij Azure AD B2C.

## <a name="policy-keyset-and-keys"></a>Beleidssleutelset en -sleutels

De resource op het hoogste niveau voor beleidssleutels in Azure AD B2C is de **Keyset-container.** Elke keyset bevat ten minste één **sleutel**. Een sleutel heeft de volgende kenmerken:

| Kenmerk |  Vereist | Opmerkingen |
| --- | --- |--- |
| `use` | Yes | Gebruik: Identificeert het beoogde gebruik van de openbare sleutel. Het `enc` versleutelen van gegevens of het verifiëren van de handtekening op gegevens `sig` .|
| `nbf`| No | Activeringsdatum en -tijd. |
| `exp`| No | Vervaldatum en -tijd. |

We raden u aan de waarden voor sleutelactivering en vervaldatum in te stellen op basis van uw PKI-standaarden. Mogelijk moet u deze certificaten periodiek roteren om beveiligings- of beleidsredenen. U kunt bijvoorbeeld een beleid hebben om al uw certificaten elk jaar te roteren.

Als u een sleutel wilt maken, kunt u een van de volgende methoden kiezen:

- **Handmatig:** maak een geheim met een tekenreeks die u definieert. Het geheim is een symmetrische sleutel. U kunt de activerings- en vervaldatums instellen.
- **Gegenereerd:** automatisch een sleutel genereren. U kunt activerings- en vervaldatums instellen. Er zijn twee opties:
  - **Geheim:** genereert een symmetrische sleutel.
  - **RSA:** genereert een sleutelpaar (asymmetrische sleutels).
- **Uploaden:** een certificaat of een PKCS12-sleutel uploaden. Het certificaat moet de persoonlijke en openbare sleutels (asymmetrische sleutels) bevatten.

## <a name="key-rollover"></a>Sleuteloverdraaien

Uit veiligheidsdoeleinden kunnen Azure AD B2C sleutels periodiek of onmiddellijk in geval van nood overrollen. Elke toepassing, id-provider of REST API die met Azure AD B2C kan worden geïntegreerd, moet worden voorbereid voor het verwerken van een gebeurtenis voor het rolloveren van sleutels, ongeacht hoe vaak dit gebeurt. Als uw toepassing of Azure AD B2C een verlopen sleutel probeert te gebruiken om een cryptografische bewerking uit te voeren, mislukt de aanmeldingsaanvraag.

Als een Azure AD B2C meerdere sleutels heeft, is slechts één van de sleutels tegelijk actief, op basis van de volgende criteria:

- De activering van de sleutel is gebaseerd op de **activeringsdatum.**
  - De sleutels worden gesorteerd op activeringsdatum in oplopende volgorde. Sleutels met activeringsdatums verderop in de toekomst worden lager in de lijst weergegeven. Sleutels zonder een activeringsdatum bevinden zich onderaan de lijst.
  - Wanneer de huidige datum en tijd groter is dan de activeringsdatum van een sleutel, Azure AD B2C de sleutel geactiveerd en wordt de vorige actieve sleutel niet meer gebruikt.
- Wanneer de verlooptijd van de huidige sleutel is verstreken en de  sleutelcontainer  een nieuwe sleutel bevat met een geldige sleutel die niet vóór en vervaltijden geldig is, wordt de nieuwe sleutel automatisch actief.
- Wanneer de verlooptijd van de huidige sleutel is  verstreken en de sleutelcontainer geen  nieuwe sleutel bevat met een geldige sleutel die niet vóór en vervaltijden geldig is, kunnen Azure AD B2C de verlopen sleutel niet gebruiken.  Azure AD B2C wordt een foutbericht binnen een afhankelijk onderdeel van uw aangepaste beleid weergegeven. U kunt dit probleem voorkomen door een standaardsleutel te maken zonder activerings- en vervaldatums als veiligheidsnet.
- Het eindpunt van de sleutel (JWKS URI) van het bekende configuratie-eindpunt van OpenId Connect weerspiegelt de sleutels die zijn geconfigureerd in de sleutelcontainer, wanneer naar de sleutel wordt verwezen in het technische profiel [JwtIssuer](./jwt-issuer-technical-profile.md). Een toepassing die een OIDC-bibliotheek gebruikt, haalt deze metagegevens automatisch op om ervoor te zorgen dat deze de juiste sleutels gebruikt om tokens te valideren. Meer informatie over het gebruik van [Microsoft Authentication Library](../active-directory/develop/msal-b2c-overview.md), waarmee altijd automatisch de nieuwste token-ondertekeningssleutels worden opgehaald.

## <a name="policy-key-management"></a>Beheer van beleidssleutels

Als u de huidige actieve sleutel in een sleutelcontainer wilt op halen, gebruikt u Microsoft Graph API [getActiveKey-eindpunt.](/graph/api/trustframeworkkeyset-getactivekey)

Ondertekenings- en versleutelingssleutels toevoegen of verwijderen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer op de overzichtspagina **onder Beleid** de **optie Identity Experience Framework.**
1. **Beleidssleutels selecteren** 
    1. Selecteer Toevoegen om een nieuwe sleutel **toe te voegen.**
    1. Als u een nieuwe sleutel wilt verwijderen, selecteert u de sleutel en selecteert u **vervolgens Verwijderen.** Als u de sleutel wilt verwijderen, typt u de naam van de sleutelcontainer die u wilt verwijderen. Azure AD B2C verwijdert u de sleutel en maakt u een kopie van de sleutel met het achtervoegsel .bak.

### <a name="replace-a-key"></a>Een sleutel vervangen

De sleutels in een sleutelset zijn niet vervangbaar of verwisselbaar. Als u een bestaande sleutel wilt wijzigen:

- We raden u aan een nieuwe sleutel toe te voegen met de **activeringsdatum** ingesteld op de huidige datum en tijd. Azure AD B2C activeert de nieuwe sleutel en stopt met het gebruik van de vorige actieve sleutel.
- U kunt ook een nieuwe sleutelset met de juiste sleutels maken. Werk uw beleid bij om de nieuwe keyset te gebruiken en verwijder vervolgens de oude keyset. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik Microsoft Graph voor het automatiseren van de implementatie van [een keyset](microsoft-graph-operations.md#trust-framework-policy-keyset) [en beleidssleutels.](microsoft-graph-operations.md#trust-framework-policy-key)

::: zone-end
