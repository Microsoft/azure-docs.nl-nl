---
title: Overzicht van beleids sleutels-Azure Active Directory B2C
description: Meer informatie over de typen versleutelings beleids sleutels die kunnen worden gebruikt in Azure Active Directory B2C voor het ondertekenen en valideren van tokens, client geheimen, certificaten en wacht woorden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30348d7ca12ded2d1f4b0522a7cabeadf0553a07
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953352"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Overzicht van beleids sleutels in Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) slaat geheimen en certificaten op in de vorm van beleids sleutels om een vertrouwens relatie tot stand te brengen met de services waarmee deze worden geïntegreerd. Deze vertrouwens relaties bestaan uit:

- Externe id-providers
- Verbinding maken met [rest API services](restful-technical-profile.md)
- Ondertekening en versleuteling van tokens

 In dit artikel wordt beschreven wat u moet weten over de beleids sleutels die worden gebruikt door Azure AD B2C.

> [!NOTE]
> De configuratie van beleids sleutels is momenteel beperkt tot [aangepast beleid](./custom-policy-get-started.md) .

U kunt geheimen en certificaten configureren voor het tot stand brengen van een vertrouwens relatie tussen services in de Azure Portal onder het menu **beleids sleutels** . Sleutels kunnen symmetrisch of asymmetrisch zijn. *Symmetrische* crypto grafie of persoonlijke sleutel cryptografie is de plaats waar een gedeeld geheim wordt gebruikt om de gegevens te versleutelen en ontsleutelen. *Asymmetrische* crypto grafie of open bare-sleutel cryptografie is een cryptografisch systeem dat paren sleutels gebruikt, bestaande uit open bare sleutels die worden gedeeld met de Relying Party toepassing en persoonlijke sleutels die alleen bekend zijn bij Azure AD B2C.

## <a name="policy-keyset-and-keys"></a>Sleutel sets en sleutels voor beleid

De resource op het hoogste niveau voor beleids sleutels in Azure AD B2C is de container voor **sleutel sets** . Elke sleutelset bevat ten minste één **sleutel**. Een sleutel heeft de volgende kenmerken:

| Kenmerk |  Vereist | Opmerkingen |
| --- | --- |--- |
| `use` | Ja | Gebruik: geeft het beoogde gebruik van de open bare sleutel aan. Gegevens versleutelen `enc` of de hand tekening op gegevens verifiëren `sig` .|
| `nbf`| Nee | Datum en tijd van de activering. |
| `exp`| Nee | Verval datum en-tijd. |

We raden u aan om de sleutel activering en verloop waarden in te stellen op basis van uw PKI-standaarden. Mogelijk moet u deze certificaten regel matig draaien om beveiligings-of beleids redenen. Zo kunt u bijvoorbeeld een beleid hebben om elk jaar al uw certificaten te draaien.

Als u een sleutel wilt maken, kunt u een van de volgende methoden kiezen:

- **Hand matig** : Maak een geheim met een teken reeks die u definieert. Het geheim is een symmetrische sleutel. U kunt de activerings-en verval datums instellen.
- **Gegenereerd** -automatisch een sleutel genereren. U kunt activerings-en verval datums instellen. Er zijn twee opties:
  - **Geheim** : Hiermee wordt een symmetrische sleutel gegenereerd.
  - **RSA** : Hiermee wordt een sleutel paar (asymmetrische sleutels) gegenereerd.
- **Upload** : Upload een certificaat of een pkcs12/pfx-profiel-sleutel. Het certificaat moet de persoonlijke en open bare sleutels (asymmetrische sleutels) bevatten.

## <a name="key-rollover"></a>Rollover van sleutel

Uit veiligheids overwegingen kan Azure AD B2C regel matig of onmiddellijk in het geval van een nood sleutel worden overgeschakeld. Elke toepassing, ID-provider of REST API die met Azure AD B2C wordt geïntegreerd, moet worden voor bereid om een sleutel rollover gebeurtenis te verwerken, ongeacht hoe vaak deze kan optreden. Als uw toepassing of Azure AD B2C probeert om een verlopen sleutel te gebruiken om een cryptografische bewerking uit te voeren, mislukt de aanmeldings aanvraag.

Als een Azure AD B2C sleutelset meerdere sleutels heeft, is slechts één van de sleutels tegelijk actief, op basis van de volgende criteria:

- De sleutel activering is gebaseerd op de **activerings datum**.
  - De sleutels worden gesorteerd op de activerings datum in oplopende volg orde. Sleutels met een meer activerings datum in de toekomst worden lager in de lijst weer gegeven. Sleutels zonder een activerings datum bevinden zich onder aan de lijst.
  - Wanneer de huidige datum en tijd groter is dan de activerings datum van een sleutel, wordt de sleutel door Azure AD B2C geactiveerd en wordt de vorige actieve sleutel niet meer gebruikt.
- Wanneer de verval tijd van de huidige sleutel is verstreken en de sleutel container een nieuwe sleutel bevat met de tijden geldig en *niet vóór* en *verloop* tijd, wordt de nieuwe sleutel automatisch actief.
- Als de verval tijd van de huidige sleutel is verstreken en de sleutel container bevat *geen* nieuwe sleutel met de tijden geldig en *niet vóór* en *verloop* tijd, kan Azure AD B2C de verlopen sleutel niet gebruiken. Azure AD B2C wordt een fout bericht weer gegeven binnen een afhankelijk onderdeel van uw aangepaste beleid. Om dit probleem te voor komen, kunt u een standaard sleutel maken zonder de activerings-en verloop datums als een veiligheids-net.
- Het eind punt van de sleutel (JWKS-URI) van het OpenID Connect Connect-bekende configuratie-eind punt toont de sleutels die in de sleutel container zijn geconfigureerd, wanneer in het [technische profiel van JwtIssuer](./jwt-issuer-technical-profile.md)naar de sleutel wordt verwezen. Een toepassing die gebruikmaakt van een OIDC-bibliotheek, haalt deze meta gegevens automatisch op om ervoor te zorgen dat de juiste sleutels worden gebruikt voor het valideren van tokens. Meer informatie over het gebruik van [micro soft-verificatie bibliotheek](../active-directory/develop/msal-b2c-overview.md), waarmee altijd automatisch de meest recente token handtekening sleutels worden opgehaald.

## <a name="policy-key-management"></a>Beheer van beleids sleutels

Als u de huidige actieve sleutel binnen een sleutel container wilt ophalen, gebruikt u het [getActiveKey](/graph/api/trustframeworkkeyset-getactivekey) -eind punt van de Microsoft Graph-API.

Voor het toevoegen of verwijderen van ondertekenings-en versleutelings sleutels:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer op de pagina overzicht onder **beleids regels** het **Framework identiteits ervaring**.
1. **Beleids sleutels** selecteren 
    1. Selecteer **toevoegen** om een nieuwe sleutel toe te voegen.
    1. Als u een nieuwe sleutel wilt verwijderen, selecteert u de sleutel en selecteert u vervolgens **verwijderen**. Als u de sleutel wilt verwijderen, typt u de naam van de sleutel container die u wilt verwijderen. De sleutel wordt door Azure AD B2C verwijderd en er wordt een kopie van de sleutel gemaakt met het achtervoegsel. bak.

### <a name="replace-a-key"></a>Een sleutel vervangen

De sleutels in een sleutelset zijn niet vervangbaar of verwisselbaar. Als u een bestaande sleutel wilt wijzigen:

- U kunt het beste een nieuwe sleutel toevoegen waarbij de **activerings datum** is ingesteld op de huidige datum en tijd. Azure AD B2C wordt de nieuwe sleutel geactiveerd en stopt met het gebruik van de vorige actieve sleutel.
- U kunt ook een nieuwe sleutelset maken met de juiste sleutels. Werk uw beleid bij voor het gebruik van de nieuwe sleutelset en verwijder vervolgens de oude sleutelset. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van Microsoft Graph voor het automatiseren van de implementatie van [sleutel sets](microsoft-graph-operations.md#trust-framework-policy-keyset) en [beleids sleutels](microsoft-graph-operations.md#trust-framework-policy-key) .