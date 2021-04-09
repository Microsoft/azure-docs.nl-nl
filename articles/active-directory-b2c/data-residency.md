---
title: Regiobeschikbaarheid en gegevenslocatie
titleSuffix: Azure AD B2C
description: Beschik baarheid van regio's, gegevens locatie en informatie over Azure Active Directory B2C preview-tenants.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: 9cb7a97b3f57ee7ac10babc53ee2263d51838777
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92309682"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Beschik baarheid van regio's & gegevens locatie

Beschik baarheid van regio's en gegevens locatie zijn twee verschillende concepten die verschillend van toepassing zijn op Azure AD B2C van de rest van Azure. In dit artikel vindt u informatie over de verschillen tussen deze twee concepten en vergelijkt u hoe deze van toepassing zijn op Azure versus Azure AD B2C.

Azure AD B2C is **over het algemeen wereld wijd beschikbaar** met de optie voor **gegevens locatie** in de **Verenigde Staten, Europa of Azië en Stille Oceaan**.

[Beschik baarheid van regio's](#region-availability) verwijst naar waar een service beschikbaar is voor gebruik.

De [gegevens locatie](#data-residency) verwijst naar de locatie waar de gebruikers gegevens worden opgeslagen.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

Azure AD B2C is wereld wijd beschikbaar via de open bare Azure-Cloud.

Dit wijkt af van het model, gevolgd door de meeste andere Azure-Services, die doorgaans *Beschik baarheid* met *gegevens locatie*. U kunt voor beelden van dit bekijken op de pagina [producten van Azure die beschikbaar zijn per regio](https://azure.microsoft.com/regions/services/) en de [Active Directory B2C prijs calculator](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Gegevenslocatie

Azure AD B2C gebruikers gegevens worden opgeslagen in Verenigde Staten, Europa of de regio van Azië en Stille Oceaan.

Data locatie wordt bepaald door het land/de regio die u selecteert bij het [maken van een Azure AD B2C-Tenant](tutorial-create-tenant.md):

![Scherm afbeelding van een Tenant formulier maken, land of regio kiezen.](./media/data-residency/data-residency-b2c-tenant.png)

De gegevens bevinden zich in de **Verenigde Staten** voor de volgende landen/regio's:

> Verenigde Staten (VS), Canada (CA), Costa Rica (CR), Dominicaanse Republiek (DO), El Salvador (SV), Guatemala (GT), Mexico (MX), Panama (PA), Puerto Rico (PR) en Trinidad & Tobago (TT)

Gegevens bevinden zich in **Europa** voor de volgende landen/regio's:

> Algerije (DZ), Oosten rijk (op), Azerbeidzjan (AZ), Bahrein (BH), Belarus (per), België (zijn), Bulgarije (BG), Kroatië (HR), Cyprus (CY), Tsjechië (CZ), Denemarken (DK), Egypte (bijvoorbeeld), Estland (EE), Finland (FT), Frank rijk (FR) Duitsland (DE), Grieken land (GR), Hongarije (HU), IJsland (IS), Ierland (IE), Israël (IL), Italië (IT), Jordanië (JO), Kazachstan (KZ), Kenia (KE), Koeweit (KW), Letland (LV), Libanon (LB), Liechtenstein (LI), Litouwen (LT) , Luxemburg (LU), North Macedonië (ML), Malta (MT), Montenegro (ME), Marokko (MA), Nederland (NL), Nigeria (aardgas), Noor wegen (NO), Oman (OM), Pakistan (PK), Polen (PL), Portugal (PT), Qatar (QA), Roemenië (RO), Rusland (RU), Saudi-Arabië (SA), Servië (RS), Slowakije (SK), Slovenië (ST), Zuid-Afrika (ZA), Spanje (ES), Zweden (SE), Zwitser land (CH), Tunesië (TN), Turkije (TR), Oekraïne (UA), Verenigde Arabische Emiraten (AE) en Verenigd Konink rijk (GB)

De gegevens bevinden zich in **Azië en Stille Oceaan** voor de volgende landen/regio's:

> Afghanistan (AF), Hong Kong SAR (HK), India (IN), Indonesië (ID), Japan (JP), Korea (KR), Maleisië (MY), Filip pijnen (PH), Singapore (AG), Sri Lanka (LK), Taiwan (TW) en Thai land (TH).

De volgende landen/regio's worden toegevoegd aan de lijst. U kunt nu nog steeds Azure AD B2C gebruiken door een van de bovenstaande landen/regio's te kiezen.

> Argentinië, Australië, Brazilië, Chili, Colombia, Ecuador, Irak, Nieuw-Zeeland, Paraguay, Peru, Uruguay en Venezuela.

## <a name="remote-profile-solution"></a>Oplossing voor externe profielen

Met Azure AD B2C [aangepast beleid](custom-policy-overview.md)kunt u integreren met [rest API-services](custom-policy-rest-api-intro.md), waarmee u gebruikers profielen opslaat en leest vanuit een externe data base (zoals een marketing database, een CRM-systeem of een line-of-Business-toepassing).  
- Tijdens het bewerken van de registratie en het profiel kunt Azure AD B2C een aangepaste REST API aanroepen om het gebruikers profiel te behouden voor de externe gegevens bron. De referenties van de gebruiker worden opgeslagen in Azure AD B2C Directory. 
- Bij het aanmelden, na de verificatie van referenties met een lokaal of sociaal account, roept Azure AD B2C de REST API aan, waarmee de unieke id van de gebruiker wordt verzonden als een primaire sleutel van de gebruiker (e-mail adres of objectId). Met de REST API worden de gegevens van de externe data base gelezen en wordt het gebruikers profiel geretourneerd.  

Nadat u zich hebt aangemeld, het profiel hebt bewerkt of het aanmelden is voltooid, bevat Azure AD B2C het gebruikers profiel in het toegangs token dat wordt geretourneerd naar de toepassing. Zie voor meer informatie de Azure AD B2C van de [voorbeeld oplossing voor externe profielen](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) in github.

## <a name="preview-tenant"></a>Preview-Tenant

Als u een B2C-Tenant hebt gemaakt tijdens de preview-periode van Azure AD B2C's, is het waarschijnlijk dat uw **Tenant type** **een preview-Tenant** bevat.

Als dit het geval is, moet u uw Tenant alleen gebruiken voor ontwikkelings-en test doeleinden. Gebruik geen voor beeld-Tenant voor productie toepassingen.

**Er is geen migratie-pad** van een preview B2C-Tenant naar een productie-Scale B2C-Tenant. U moet een nieuwe B2C-Tenant maken voor uw productie toepassingen.

Er zijn bekende problemen bij het verwijderen van een preview B2C-Tenant en het maken van een B2C-Tenant met dezelfde domein naam. *U moet een B2C-Tenant met een productie schaal maken met een andere domein naam*.

![Scherm opname van een Tenant type, als preview-Tenant.](./media/data-residency/preview-b2c-tenant.png)

## <a name="next-steps"></a>Volgende stappen

- [Een Azure AD B2C-Tenant maken](tutorial-create-tenant.md).
