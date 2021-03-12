---
title: De Azure Active Directory-apps registreren voor de Azure-API voor FHIR
description: In deze zelfstudie wordt uitgelegd welke toepassingen moeten worden geregistreerd voor de Azure-API voor FHIR en de FHIR-server voor Azure.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: b01f65b6be31007cebc7a5b03a1c57f6b901fe53
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018242"
---
# <a name="register-the-azure-active-directory-apps-for-azure-api-for-fhir"></a>De Azure Active Directory-apps registreren voor de Azure-API voor FHIR

U kunt uit verschillende configuratieopties kiezen wanneer u de Azure-API voor FHIR of de FHIR-server voor Azure (OSS) instelt. Voor open source moet u uw eigen resourcetoepassingsregistratie maken. Voor de Azure-API voor FHIR wordt deze resourcetoepassing automatisch gemaakt.

## <a name="application-registrations"></a>Toepassingsregistraties

Een toepassing moet geregistreerd zijn om met Azure AD te kunnen werken. In de context van de FHIR-server zijn er twee soorten toepassingsregistraties die besproken moeten worden:

1. Resourcetoepassingsregistraties.
1. Clienttoepassingsregistraties.

**Resourcetoepassingen** zijn weergaven in Azure AD van een API of resource die wordt beveiligd met Azure AD, in het bijzonder de Azure-API voor FHIR. Een resourcetoepassing voor de Azure-API voor FHIR wordt automatisch gemaakt wanneer u de service inricht, maar als u de open source server gebruikt, moet u [een resourcetoepassing registreren](register-resource-azure-ad-client-app.md) in Azure AD. Deze resourcetoepassing zal een id-URI hebben. Het wordt aanbevolen dat deze URI hetzelfde is als de URI van de FHIR-server. Deze URI moet worden gebruikt als de `Audience` voor de FHIR-server. Een clienttoepassing kan om toegang tot deze FHIR-server vragen wanneer deze een token opvraagt.

*Clienttoepassingen* zijn registraties van de clients die tokens zullen opvragen. In OAuth 2.0 maken we vaak onderscheid tussen ten minste drie verschillende soorten toepassingen:

1. **Vertrouwelijke clients**, ook wel bekend als web-apps in Azure AD. Vertrouwelijke clients zijn toepassingen die gebruikmaken van de [verificatiecodestroom](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) om een token op te halen namens een aangemelde gebruiker die geldige referenties opgeeft. Ze worden vertrouwelijke clients genoemd omdat ze een geheim kunnen bewaren en dit geheim aan Azure AD zullen weergeven wanneer de verificatiecode wordt omgeruild voor een token. Aangezien vertrouwelijke clients zichzelf kunnen verifiëren via het clientgeheim, worden ze meer vertrouwd dan openbare clients en kunnen ze tokens met een langere levensduur hebben en een vernieuwingstoken krijgen. Meer informatie over hoe u [een vertrouwelijke client registreert](register-confidential-azure-ad-client-app.md). Het is belangrijk de antwoord-URL te registreren waarop de client de verificatiecode zal ontvangen.
1. **Openbare clients**. Dit zijn clients die geen geheim kunnen bewaren. Meestal is dit een toepassing op een mobiel apparaat of een JavaScript-toepassing met één pagina, waar een geheim in de client zou kunnen worden ontdekt door een gebruiker. Openbare clients maken ook gebruik van de verificatiecodestroom, maar ze mogen een geheim niet weergeven wanneer ze een token ophalen en ze hebben mogelijk tokens met een kortere levensduur en geen vernieuwingstoken. Meer informatie over hoe u [een openbare client registreert](register-public-azure-ad-client-app.md).
1. Serviceclients. Deze clients halen tokens op namens zichzelf (niet namens een gebruiker) met behulp van de [clientreferentiestroom](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md). Dit zijn meestal toepassingen die op een niet-interactieve manier toegang krijgen tot de FHIR-server. Een voorbeeld hiervan is een gegevensopnameproces. Wanneer u een serviceclient gebruikt, is het niet nodig het proces te starten voor het ophalen van een token met een aanroep naar het `/authorize`-eindpunt. Een serviceclient kan rechtstreeks naar het `/token`-eindpunt gaan en de client-id en het clientgeheim weergeven om een token op te halen. Meer informatie over hoe u [een serviceclient registreert](register-service-azure-ad-client-app.md).

## <a name="next-steps"></a>Volgende stappen

In dit overzicht hebt u de soorten toepassingsregistraties doorlopen die u mogelijk nodig hebt om met een FHIR-API te kunnen werken.

Bekijk een van de instructiegidsen (op basis van uw installatie) om uw toepassingen te registreren

* [Een resourcetoepassing registreren](register-resource-azure-ad-client-app.md)
* [Een vertrouwelijke clienttoepassing registreren](register-confidential-azure-ad-client-app.md)
* [Een openbare clienttoepassing registreren](register-public-azure-ad-client-app.md)
* [Een servicetoepassing registreren](register-service-azure-ad-client-app.md)

Zodra u uw toepassingen hebt geregistreerd, kunt u de Azure-API voor FHIR implementeren.

>[!div class="nextstepaction"]
>[De Azure-API voor FHIR implementeren](fhir-paas-powershell-quickstart.md)