---
title: Aangepaste beleids regels Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over Azure Active Directory B2C aangepast beleid.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2019
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 043cf83d804803e1b5b47d3ac51bbccaa06e4e87
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87116411"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Aangepaste beleidsregels zijn configuratiebestanden die het gedrag van uw Azure AD B2C-tenant (Azure Active Directory B2C) definiëren. Gebruikersstromen zijn vooraf gedefinieerd in de Azure AD B2C-portal voor de meest voorkomende identiteitstaken. Aangepast beleid kan volledig worden bewerkt door een identiteitsontwikkelaar om veel verschillende taken te voltooien.

## <a name="comparing-user-flows-and-custom-policies"></a>Gebruikers stromen en aangepaste beleids regels vergelijken

| Context | Gebruikersstromen | Aangepast beleid |
|-|-------------------|-----------------|
| Doel gebruikers | Alle toepassings ontwikkelaars met of zonder identiteits expertise. | Identiteits-professionals, systeem integrators, consultants en interne identiteits teams. Ze zijn vertrouwd met OpenID Connect Connect flows en begrijpen identiteits providers en verificatie op basis van claims. |
| Configuratie methode | Azure Portal met een gebruikers vriendelijke gebruikers interface (UI). | Rechtstreeks bewerken van XML-bestanden en vervolgens uploaden naar de Azure Portal. |
| UI-aanpassing | Volledige UI-aanpassing, inclusief HTML, CSS en Java script.<br><br>Meertalige ondersteuning met aangepaste teken reeksen. | Hetzelfde |
| Kenmerk aanpassing | Standaard-en aangepaste kenmerken. | Hetzelfde |
| Token-en sessie beheer | Aangepaste tokens en opties voor meerdere sessies. | Hetzelfde |
| Id-providers | Vooraf gedefinieerde lokale of sociale provider en de meeste OIDC-id-providers, zoals Federatie met Azure Active Directory-tenants. | Op standaarden gebaseerde OIDC, OAUTH en SAML.  Verificatie is ook mogelijk met behulp van integratie met REST Api's. |
| Identiteits taken | Meld u aan of Meld u aan met lokale of veel sociale accounts.<br><br>Self-service voor wacht woord opnieuw instellen.<br><br>Profiel bewerken.<br><br>Multi-Factor Authentication.<br><br>Tokens en sessies aanpassen.<br><br>Toegangs token stromen. | Voer dezelfde taken uit als voor gebruikers stromen met aangepaste ID-providers of gebruik aangepaste bereiken.<br><br>Een gebruikers account inrichten in een ander systeem op het moment van registratie.<br><br>Stuur een welkomst-e-mail met uw eigen e-mailservice provider.<br><br>Gebruik een gebruikers archief buiten Azure AD B2C.<br><br>Door de gebruiker verstrekte informatie met een vertrouwd systeem valideren met behulp van een API. |

## <a name="policy-files"></a>Beleids bestanden

Deze drie typen beleids bestanden worden gebruikt:

- **Basis bestand** : bevat de meeste definities. Het is raadzaam om een minimum aantal wijzigingen in dit bestand aan te brengen om te helpen bij het oplossen van problemen en het onderhoud op lange termijn van uw beleid.
- **Extensies bestand** : bevat de unieke configuratie wijzigingen voor uw Tenant.
- **RP-bestand (Relying Party)** : het bestand met de taak focus dat rechtstreeks wordt aangeroepen door de toepassing of service (ook bekend als een Relying Party). Elke unieke taak vereist een eigen RP en afhankelijk van de huismerk vereisten, kan het aantal toepassingen x het totale aantal use cases zijn.

Gebruikers stromen in Azure AD B2C volgen het bestands patroon dat hierboven wordt weer gegeven, maar de ontwikkelaar ziet alleen het RP-bestand, terwijl de Azure Portal wijzigingen op de achtergrond aanbrengt in het bestand extensies.

Hoewel er drie soorten beleids bestanden zijn, bent u niet beperkt tot drie bestanden. Mogelijk hebt u meerdere bestanden van elk bestands type. Als u bijvoorbeeld geen wijzigingen wilt aanbrengen in uw extensies-bestand, kunt u een Extensions2-bestand maken om het extensie bestand verder uit te breiden.

## <a name="custom-policy-core-concepts"></a>Basis concepten voor aangepast beleid

De CIAM-service (Customer Identity and Access Management) in azure omvat:

- Een gebruikers lijst die toegankelijk is via Microsoft Graph en die gebruikers gegevens voor lokale accounts en federatieve accounts bevat.
- Toegang tot het **Framework voor identiteits ervaring** dat de vertrouwens relatie tussen gebruikers en entiteiten vertrouwt en waarmee claims ertussen worden verzonden om een identiteits-of toegangs beheer taak te volt ooien.
- Een beveiligings token service (STS) die ID-tokens uitgeeft, tokens vernieuwt en toegangs tokens (en gelijkwaardige SAML-verklaringen) maakt en valideert om resources te beveiligen.

Azure AD B2C interactie met id-providers, gebruikers, andere systemen en met de lokale gebruikers lijst in volg orde om een identiteits taak te kunnen uitvoeren. U kunt bijvoorbeeld een gebruiker aanmelden, een nieuwe gebruiker registreren of een wacht woord opnieuw instellen. Met het Framework voor identiteits ervaring en een beleid (ook wel een gebruikers traject of een beleid voor vertrouwens raamwerk genoemd) wordt een vertrouwens relatie met meerdere partijen vastgelegd en worden expliciet de actors, de acties, de protocollen en de volg orde van de stappen gedefinieerd.

Het Framework voor identiteits ervaring is een volledig configureerbaar, op de cloud gebaseerd Azure-platform dat de vertrouwens relatie tussen entiteiten in standaard protocol indelingen, zoals OpenID Connect Connect, OAuth, SAML en enkele niet-standaard, vertrouwt, bijvoorbeeld REST API systeem claims uitwisseling. Het Framework maakt gebruikers vriendelijke, met witte labels ondervindt ervaringen die ondersteuning bieden voor HTML en CSS.

Een aangepast beleid bestaat uit een of meer XML-bestanden die in een hiërarchische keten naar elkaar verwijzen. De XML-elementen definiëren het claim schema, claim transformaties, inhouds definities, claim providers, technische profielen en de stappen voor het door geven van de gebruikers, onder andere elementen. Een aangepast beleid is toegankelijk als een of meer XML-bestanden die worden uitgevoerd door het Framework voor identiteits ervaring wanneer het wordt aangeroepen door een Relying Party. Ontwikkelaars die aangepaste beleidsregels configureren, moeten de vertrouwde relaties zeer zorgvuldig definiëren en rekening houden met zaken zoals eindpunten van metagegevens, exacte definities voor claimuitwisseling, en het configureren van geheimen, sleutels en certificaten die nodig zijn voor elke id-provider.

### <a name="inheritance-model"></a>Overname model

Wanneer een toepassing het bestand met de RP-beleids regel aanroept, voegt het Framework voor identiteits ervaring in Azure AD B2C alle elementen uit het basis bestand, van het extensie bestand en vervolgens uit het RP-beleids bestand toe om het huidige beleid in werking te stellen.  Elementen van hetzelfde type en dezelfde naam in het RP-bestand overschrijven die in de uitbrei dingen en de basis van uitbrei dingen voor extensies.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met aangepaste beleidsregels](custom-policy-get-started.md)
