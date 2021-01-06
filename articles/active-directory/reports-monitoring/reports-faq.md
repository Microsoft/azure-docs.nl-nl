---
title: Veelgestelde vragen over Azure Active Directory rapporten | Microsoft Docs
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
ms.openlocfilehash: 45b6b182d030c267dcec7ed57c0c0dd1901b0cb1
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935084"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Veelgestelde vragen over Azure Active Directory rapporten

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Active Directory-rapportage (Azure AD). Zie [Azure Active Directory-rapportage](overview-reports.md) voor meer informatie. 

## <a name="getting-started"></a>Aan de slag 

**V: Ik gebruik momenteel de `https://graph.windows.net/<tenant-name>/reports/` api's van het eind punt om Azure AD-controle en rapporten over het gebruik van toepassingen te verzamelen in onze rapportage systemen via een programma. Waar moet ik naar overschakelen?**

**A:** Zoek de [API-verwijzing](https://developer.microsoft.com/graph/) om te zien hoe u [de api's kunt gebruiken voor toegang tot activiteiten rapporten](concept-reporting-api.md). Dit eind punt heeft twee rapporten (**controle** en **aanmeldingen**) die alle gegevens leveren die u hebt ontvangen in het oude API-eind punt. Dit nieuwe eind punt heeft ook een aanmeldings rapport met de Azure AD Premium licentie die u kunt gebruiken om het gebruik van de app, het gebruik van het apparaat en de aanmeldings gegevens van gebruikers te verkrijgen.

---

**V: Ik gebruik momenteel de `https://graph.windows.net/<tenant-name>/reports/` endpoint api's om Azure AD-beveiligings rapporten (specifieke typen detecties, zoals gelekte referenties of aanmeldingen vanaf anonieme IP-adressen), via programma code te halen in onze rapportage systemen. Waar moet ik naar overschakelen?**

**A:** U kunt de [API voor risico detectie van identiteits beveiliging](../identity-protection/howto-identity-protection-graph-api.md) gebruiken voor toegang tot beveiligings detecties via Microsoft Graph. Deze nieuwe indeling biedt meer flexibiliteit in de manier waarop u gegevens kunt opvragen, met geavanceerde filters, veld selectie en meer, en waarmee risico detecties in één type worden gestandaardiseerd, zodat integratie gemakkelijker kan worden geïntegreerd in Siem's en andere hulpprogram ma's voor gegevens verzameling. Omdat de gegevens een andere indeling hebben, kunt u geen nieuwe query vervangen door uw oude query's. [De nieuwe API maakt echter gebruik van Microsoft Graph](/graph/api/resources/identityriskevent?view=graph-rest-beta). Dit is de micro soft-norm voor dergelijke api's als Microsoft 365 of Azure AD. Het werk vereist dus dat uw huidige Microsoft Graph investeringen kunnen worden uitgebreid of dat u de overgang naar dit nieuwe standaard platform kunt starten.

---

**V: Hoe kan ik een Premium-licentie verkrijgen?**

**A:** Zie aan de slag [met Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) om uw Azure Active Directory-editie bij te werken.

---

**V: hoe binnenkort moeten de activiteiten gegevens na het verkrijgen van een Premium-licentie worden weer gegeven?**

**A:** Als u al activiteiten gegevens als een gratis licentie hebt, kunt u deze direct weer geven. Als u geen gegevens hebt, duurt het Maxi maal drie dagen voordat de gegevens in de rapporten worden weer gegeven.

---

**V: kan ik de gegevens van de afgelopen maand bekijken na het ophalen van een Azure AD Premium-licentie?**

**A:** Als u onlangs hebt overgeschakeld naar een Premium-versie (met inbegrip van een proef versie), kunt u in eerste instantie de gegevens weer geven tot 7 dagen. Wanneer gegevens worden verzameld, kunt u de gegevens weer geven voor de afgelopen 30 dagen.

---

**V: moet ik een globale beheerder zijn om de aanmeldingen van de activiteit te bekijken voor de Azure Portal of om gegevens op te halen via de API?**

**A:** Nee, u kunt ook toegang krijgen tot de rapport gegevens via de portal of via de API als u een **beveiligings lezer** of **beveiligings beheerder** voor de Tenant bent. **Globale beheerders** hebben natuurlijk ook toegang tot deze gegevens.

---


## <a name="activity-logs"></a>Activiteitenlogboeken


**V: wat is de gegevens retentie voor activiteiten Logboeken (controle en aanmeldingen) in het Azure Portal?** 

**A:** Zie voor meer informatie [beleid voor gegevens retentie voor Azure AD-rapporten](reference-reports-data-retention.md).

---

**V: hoe lang duurt het voordat ik de activiteit gegevens kan zien nadat ik mijn taak heb voltooid?**

**A:** Audit logboeken hebben een latentie van 15 minuten tot een uur. Logboeken voor aanmeldings activiteiten kunnen vijf tien minuten tot Maxi maal 2 uur duren voor sommige records.

---

**V: kan ik Microsoft 365 informatie over het activiteiten logboek verkrijgen via de Azure Portal?**

**A:** Hoewel Microsoft 365 activiteit en activiteiten logboeken van Azure AD een groot aantal Directory bronnen delen. Als u een volledige weer gave van de Microsoft 365 activiteiten logboeken wilt, gaat u naar het [Microsoft 365-beheer centrum](https://admin.microsoft.com) om informatie over het activiteiten logboek van Office 365 te krijgen.

---

**V: welke Api's moet ik gebruiken om informatie te krijgen over Microsoft 365 activiteiten logboeken?**

**A:** Gebruik de [Microsoft 365-beheer-api's](/office/office-365-management-api/office-365-management-apis-overview) om toegang te krijgen tot de Microsoft 365 activiteiten logboeken via een API.

---

**V: hoeveel records ik kan downloaden van Azure Portal?**

**A:** U kunt Maxi maal 5000 records downloaden van de Azure Portal. De records worden gesorteerd op de *meest recente* en standaard worden de meest recente 5000 records weer geven.

---

## <a name="risky-sign-ins"></a>Riskante aanmeldingen

**V: er is sprake van een risico detectie in identiteits beveiliging, maar ik zie geen corresponderende aanmelding in het rapport met aanmeldingen. Wordt dit verwacht?**

**A:** Ja, identiteits beveiliging evalueert Risico's voor alle verificatie stromen, ongeacht of deze interactief of niet-interactief zijn. Het rapport alle aanmeldingen alleen bevat alleen de interactieve aanmeldingen.

---

**V: Hoe kan ik weet ik waarom een aanmelding of een gebruiker een risico op de Azure Portal heeft gevlagd?**

**A:** Als u een **Azure AD Premium** -abonnement hebt, kunt u meer te weten komen over de onderliggende risico detecties door de gebruiker te selecteren in gebruikers die zijn **gemarkeerd voor risico** of door een record te selecteren in het rapport **Risk ante aanmeldingen** . Als u een **gratis** of **basis** abonnement hebt, kunt u de gebruikers die risico lopen en rapporten met Risk ante aanmeldingen weer geven, maar u kunt de informatie over de onderliggende risico detectie niet zien.

---

**V: hoe worden IP-adressen berekend in het rapport aanmeldingen en Risk ante aanmeldingen?**

**A:** IP-adressen worden zodanig uitgegeven dat er geen definitieve verbinding is tussen een IP-adres en waar de computer met dat adres zich fysiek bevindt. Het toewijzen van IP-adressen is nog complexer door factoren zoals mobiele providers en Vpn's die IP-adressen uit centrale Pools uitgeven, vaak van belang voor de daad werkelijke gebruik van het client apparaat. Op dit moment in azure AD-rapporten is het converteren van een IP-adres naar een fysieke locatie een beste werk op basis van traceringen, register gegevens, het terugdraaien van de ups en andere informatie. 

---

**V: wat doet de risico detectie ' Aanmelden met extra risico '?**

**A:** Om u inzicht te geven in alle Risk ante aanmeldingen in uw omgeving, wordt ' Aanmelden met extra risico ' functions als tijdelijke aanduiding voor aanmeldingen voor detecties die exclusief zijn voor Azure AD Identity Protection-abonnees.

---

## <a name="conditional-access"></a>Voorwaardelijke toegang

**V: wat is er nieuw in deze functie?**

**A:** Klanten kunnen nu het beleid voor voorwaardelijke toegang in het rapport van alle aanmeldingen oplossen. Klanten kunnen de status van de voorwaardelijke toegang controleren en de Details bekijken van de beleids regels die zijn toegepast op de aanmelding en het resultaat van elk beleid.

**V: Hoe kan ik aan de slag?**

**A:** Om aan de slag te gaan:

* Navigeer naar het rapport aanmeldingen in de [Azure Portal](https://portal.azure.com).
* Klik op de aanmelding die u wilt oplossen.
* Navigeer naar het tabblad **voorwaardelijke toegang** . Hier kunt u alle beleids regels weer geven die van invloed zijn op de aanmelding en het resultaat van elk beleid. 
    
**V: wat zijn de mogelijke waarden voor de status van voorwaardelijke toegang?**

**A:** De status van voorwaardelijke toegang kan de volgende waarden hebben:

* **Niet toegepast**: Dit betekent dat er geen beleid voor voorwaardelijke toegang is met de gebruiker en de app in het bereik. 
* **Geslaagd**: Dit betekent dat er een beleid voor voorwaardelijke toegang is met de gebruiker en de app in het bereik en het beleid voor voorwaardelijke toegang is voldaan. 
* **Probleem**: bij de aanmelding is voldaan aan de gebruikers-en toepassings voorwaarde van ten minste één beleid voor voorwaardelijke toegang en granting Controls is niet voldaan of ingesteld om de toegang te blok keren.
    
**V: wat zijn de mogelijke waarden voor het resultaat van het beleid voor voorwaardelijke toegang?**

**A:** Een beleid voor voorwaardelijke toegang kan de volgende resultaten hebben:

* **Geslaagd**: er is met het beleid voldaan.
* **Fout**: er is niet voldaan aan het beleid.
* **Niet toegepast**: Dit komt mogelijk doordat de beleids voorwaarden niet voldoen aan het beleid.
* **Niet ingeschakeld**: dit wordt veroorzaakt door het beleid in een uitgeschakelde status. 
    
**V: de beleids naam in het rapport alle aanmeldingen komt niet overeen met de beleids naam in CA. waarom?**

**A:** De naam van het beleid in het rapport alle aanmeldingen is gebaseerd op de naam van het beleid voor voorwaardelijke toegang op het moment van de aanmelding. Dit kan inconsistent zijn met de beleids naam in CA als u de beleids naam later hebt bijgewerkt, dat wil zeggen, na het aanmelden.

**V: mijn aanmelding is geblokkeerd vanwege een beleid voor voorwaardelijke toegang, maar in het rapport voor de aanmeldings activiteit is aangegeven dat de aanmelding is geslaagd. Waarom?**

**A:** Het aanmeldings rapport geeft momenteel mogelijk geen accurate resultaten weer voor Exchange ActiveSync-scenario's wanneer voorwaardelijke toegang wordt toegepast. Er kunnen gevallen zijn wanneer het aanmeldings resultaat in het rapport een geslaagde aanmelding bevat, maar de aanmelding is mislukt als gevolg van een beleid voor voorwaardelijke toegang.
