---
title: Azure Active Directory veelgestelde vragen over rapporten | Microsoft Docs
description: Veelgestelde vragen over Azure Active Directory rapporten.
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 05/12/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4da0083a236900037b388798d825515e94613c20
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533703"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Veelgestelde vragen over Azure Active Directory rapporten

Dit artikel bevat antwoorden op veelgestelde vragen over Azure Active Directory (Azure AD)-rapportage. Zie [Azure Active Directory-rapportage](overview-reports.md) voor meer informatie. 

## <a name="getting-started"></a>Aan de slag 

**V: Ik gebruik momenteel de eindpunt-API's om programmatisch Azure AD-audit- en geïntegreerde toepassingsgebruiksrapporten in onze `https://graph.windows.net/<tenant-name>/reports/` rapportagesystemen op te halen. Waar moet ik naar overschakelen?**

**A:** Zoek de [API-verwijzing op om](https://developer.microsoft.com/graph/) te zien hoe u de API's kunt gebruiken om toegang te krijgen tot [activiteitenrapporten.](concept-reporting-api.md) Dit eindpunt heeft twee rapporten (**Controle** en aanmeldingen ) die alle gegevens bevatten die u in het oude **API-eindpunt** hebt verzameld. Dit nieuwe eindpunt bevat ook een aanmeldingsrapport met de Azure AD Premium-licentie die u kunt gebruiken om app-gebruik, apparaatgebruik en aanmeldingsgegevens van gebruikers op te halen.

---

**V: Ik gebruik momenteel de eindpunt-API's om programmatisch Azure AD-beveiligingsrapporten (specifieke typen detecties, zoals gelekte referenties of aanmeldingen van anonieme IP-adressen) in onze rapportagesystemen te `https://graph.windows.net/<tenant-name>/reports/` halen. Waar moet ik naar overschakelen?**

**A:** U kunt de [API voor risicodetectie van Identiteitsbeveiliging gebruiken](../identity-protection/howto-identity-protection-graph-api.md) om toegang te krijgen tot beveiligingsdetecties via Microsoft Graph. Deze nieuwe indeling biedt meer flexibiliteit in de manier waarop u query's kunt uitvoeren op gegevens, met geavanceerde filters, selectie van velden en meer, en standaardiseer risicodetecties in één type voor eenvoudigere integratie met SIEM's en andere hulpprogramma's voor gegevensverzameling. Omdat de gegevens een andere indeling hebben, kunt u een nieuwe query niet vervangen door uw oude query's. De nieuwe [API maakt echter gebruik Microsoft Graph](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true). Dit is de Microsoft-standaard voor API's zoals Microsoft 365 of Azure AD. Het vereiste werk kan dus uw huidige investeringen Microsoft Graph of u helpen te beginnen met de overgang naar dit nieuwe standaardplatform.

---

**V: Hoe kan ik een Premium-licentie?**

**A:** Zie [Aan de slag met Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) om uw editie Azure Active Directory upgraden.

---

**V: Hoe snel moet ik activiteitengegevens zien na het verkrijgen van een Premium-licentie?**

**A:** Als u al activiteitengegevens als gratis licentie hebt, kunt u deze onmiddellijk zien. Als u geen gegevens hebt, duurt het maximaal drie dagen voordat de gegevens in de rapporten worden weer geven.

---

**V: Kan ik de gegevens van de afgelopen maand zien na het verkrijgen van een Azure AD Premium-licentie?**

**A:** Als u onlangs bent overgeschakeld naar een Premium-versie (inclusief een proefversie), ziet u in eerste instantie gegevens van maximaal zeven dagen. Wanneer de gegevens zijn verzameld, kunt u de gegevens van de afgelopen 30 dagen bekijken.

---

**V: Moet ik een globale beheerder zijn om de aanmeldingen voor activiteiten bij de Azure Portal of om gegevens op te halen via de API?**

**A:** Nee, u hebt ook toegang tot de rapportagegegevens via de portal of via de API als u een **beveiligingslezer** of **beveiligingsbeheerder voor** de tenant bent. Globale beheerders **hebben** natuurlijk ook toegang tot deze gegevens.

---


## <a name="activity-logs"></a>Activiteitenlogboeken


**V: Wat is de gegevensretentie voor activiteitenlogboeken (audit en aanmeldingen) in de Azure Portal?** 

**A:** Zie Beleid voor gegevensretentie [voor Azure AD-rapporten voor meer informatie.](reference-reports-data-retention.md)

---

**V: Hoe lang duurt het voordat ik de activiteitsgegevens kan zien nadat ik mijn taak heb voltooid?**

**A:** Auditlogboeken hebben een latentie van 15 minuten tot een uur. Voor sommige records kan het aanmelden van 15 minuten tot maximaal 2 uur duren.

---

**V: Kan ik informatie over Microsoft 365 activiteitenlogboek via de Azure Portal?**

**A:** Hoewel Microsoft 365 activiteitenlogboeken en Azure AD-activiteitenlogboeken veel van de directory-resources delen, moet u naar de [Microsoft 365-beheercentrum](https://admin.microsoft.com) gaan om office 365-activiteitenlogboekgegevens op te halen als u een volledig overzicht van de activiteitenlogboeken van Microsoft 365 wilt.

---

**V: Welke API's moet ik gebruiken om informatie over Microsoft 365 activiteitenlogboeken op te halen?**

**A:** Gebruik de [Microsoft 365 Management-API's om](/office/office-365-management-api/office-365-management-apis-overview) toegang te krijgen tot Microsoft 365 activiteitenlogboeken via een API.

---

**V: Hoeveel records kan ik downloaden van Azure Portal?**

**A:** U kunt maximaal 5000 records downloaden van de Azure Portal. De records worden gesorteerd op *meest recent* en standaard krijgt u de meest recente 5000 records.

---

## <a name="risky-sign-ins"></a>Riskante aanmeldingen

**V: Er is een risicodetectie in Identity Protection, maar ik zie geen bijbehorende aanmelding in het aanmeldingsrapport. Is dit verwacht?**

**A:** Ja, Identity Protection evalueert risico's voor alle verificatiestromen, ongeacht of deze interactief of niet-interactief zijn. Bij alle aanmeldingen worden echter alleen de interactieve aanmeldingen in het rapport weer geven.

---

**V: Hoe kan ik weten waarom een aanmelding of een gebruiker als riskant is gemarkeerd in de Azure Portal?**

**A:** Als u een **Azure AD Premium-abonnement** hebt, kunt u meer informatie krijgen over de onderliggende risicodetecties door de gebruiker te selecteren **in** Gebruikers voor wie wordt gemarkeerd voor risico of door een record te selecteren in het rapport **Riskante** aanmeldingen. Als u een **gratis** of **basic-abonnement** hebt, kunt u de rapporten over gebruikers die risico lopen en riskante aanmeldingen weergeven, maar u kunt de onderliggende informatie over risicodetectie niet zien.

---

**V: Hoe worden IP-adressen berekend in het rapport over aanmeldingen en riskante aanmeldingen?**

**A:** IP-adressen worden zodanig uitgegeven dat er geen definitieve verbinding is tussen een IP-adres en waar de computer met dat adres zich fysiek bevindt. Het toewijzen van IP-adressen is nog complexer door factoren zoals mobiele providers en VPN's die IP-adressen uitgeven van centrale pools, vaak heel ver van waar het clientapparaat daadwerkelijk wordt gebruikt. Momenteel is het converteren van IP-adressen naar een fysieke locatie in Azure AD-rapporten een best effort op basis van traceringen, registergegevens, reverse look ups en andere informatie. 

---

**V: Wat betekent de risicodetectie 'Aanmelden met aanvullend risico gedetecteerd'?**

**A:** Om u inzicht te geven in alle riskante aanmeldingen in uw omgeving, fungeert 'Aanmelden met extra risico gedetecteerd' als tijdelijke aanduiding voor aanmeldingen voor detecties die exclusief zijn voor Azure AD Identity Protection-abonnees.

---

## <a name="conditional-access"></a>Voorwaardelijke toegang

**V: Wat is er nieuw in deze functie?**

**A:** Klanten kunnen nu problemen met beleid voor voorwaardelijke toegang oplossen via alle aanmeldingen. Klanten kunnen de status van voorwaardelijke toegang bekijken en de details bekijken van de beleidsregels die van toepassing zijn op de aanmelding en het resultaat voor elk beleid.

**V: Hoe kan ik aan de slag?**

**A:** Ga als volgende te werk om aan de slag te gaan:

* Navigeer naar het aanmeldingsrapport in [de Azure Portal](https://portal.azure.com).
* Klik op de aanmelding die u wilt oplossen.
* Navigeer naar **het tabblad Voorwaardelijke** toegang. Hier kunt u alle beleidsregels bekijken die van invloed waren op de aanmelding en het resultaat voor elk beleid. 
    
**V: Wat zijn alle mogelijke waarden voor de status van voorwaardelijke toegang?**

**A:** De status van voorwaardelijke toegang kan de volgende waarden hebben:

* **Niet toegepast:** dit betekent dat er geen beleid voor voorwaardelijke toegang is met de gebruiker en app binnen het bereik. 
* **Geslaagd:** dit betekent dat er is voldaan aan een beleid voor voorwaardelijke toegang met de gebruiker en app binnen het bereik en het beleid voor voorwaardelijke toegang. 
* **Fout:** De aanmelding voldoet aan de gebruikers- en toepassingsvoorwaarde van ten minste één beleid voor voorwaardelijke toegang en het verlenen van besturingselementen is niet voldaan of de toegang wordt geblokkeerd.
    
**V: Wat zijn alle mogelijke waarden voor het resultaat van het beleid voor voorwaardelijke toegang?**

**A:** Een beleid voor voorwaardelijke toegang kan de volgende resultaten hebben:

* **Geslaagd:** er is aan het beleid voldaan.
* **Fout:** er is niet aan het beleid voldaan.
* **Niet toegepast:** dit kan zijn omdat niet aan de voorwaarden van het beleid is voldaan.
* **Niet ingeschakeld:** dit komt doordat het beleid de status Uitgeschakeld heeft. 
    
**V: De naam van het beleid in het rapport voor alle aanmeldingen komt niet overeen met de naam van het beleid in CA. Waarom?**

**A:** De naam van het beleid in het rapport voor alle aanmeldingen is gebaseerd op de naam van het beleid voor voorwaardelijke toegang op het moment van aanmelding. Dit kan inconsistent zijn met de naam van het beleid in ca als u de beleidsnaam later, dat wil zeggen, na de aanmelding hebt bijgewerkt.

**V: Mijn aanmelding is geblokkeerd vanwege een beleid voor voorwaardelijke toegang, maar in het rapport met aanmeldingsactiviteiten ziet u dat de aanmelding is geslaagd. Waarom?**

**A:** Op dit moment geeft het aanmeldingsrapport mogelijk geen nauwkeurige resultaten weer voor Exchange ActiveSync-scenario's wanneer voorwaardelijke toegang wordt toegepast. Het kan zijn dat het aanmeldingsresultaat in het rapport een geslaagde aanmelding toont, maar de aanmelding daadwerkelijk is mislukt vanwege een beleid voor voorwaardelijke toegang.
