---
title: Azure Active Directory activiteiten Logboeken in Azure Monitor | Microsoft Docs
description: Inleiding tot Azure Active Directory activiteiten Logboeken in Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/09/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73130c906d4d9f0da51db1b666e8562570cce40f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100571262"
---
# <a name="azure-ad-activity-logs-in-azure-monitor"></a>Azure AD-activiteitenlogboeken in Azure Monitor

U kunt de activiteiten logboeken van Azure Active Directory (Azure AD) naar verschillende eind punten routeren voor lange termijn retentie en gegevens inzichten. Met deze functie kunt u het volgende doen:

* Archiveer Azure AD-activiteiten logboeken naar een Azure-opslag account om de gegevens lange tijd te bewaren.
* Stream Azure AD-activiteiten logboeken naar een Azure Event Hub voor analyse, met behulp van populaire Security Information and Event Management (SIEM)-hulpprogram ma's, zoals Splunk en QRadar.
* Integreer Azure AD-activiteiten logboeken met uw eigen aangepaste logboek oplossingen door ze te streamen naar een Event Hub.
* Verzend Azure AD-activiteiten logboeken naar Azure Monitor-Logboeken om uitgebreide visualisaties, bewaking en waarschuwingen voor de verbonden gegevens mogelijk te maken.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>Ondersteunde rapporten

U kunt Azure AD-controle logboeken en aanmeldings logboeken door sturen naar uw Azure Storage-account, Event Hub, Azure Monitor Logboeken of aangepaste oplossing met behulp van deze functie. 

* **Auditlogboeken**: het [activiteitenrapport voor auditlogboeken](concept-audit-logs.md) biedt u toegang tot de geschiedenis van elke taak die in uw tenant is uitgevoerd.
* **Aanmeldingslogboeken**: met het [activiteitenrapport voor aanmeldingen](concept-sign-ins.md) kunt u bepalen wie de taken heeft uitgevoerd die in het auditlogboek zijn gerapporteerd.

> [!NOTE]
> Auditlogboeken en aanmeldingslogboeken met betrekking tot B2C worden momenteel niet ondersteund.
>

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze functie te gebruiken:

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, kunt u zich registreren voor een [gratis proefversie](https://azure.microsoft.com/free/).
* Een [licentie](https://azure.microsoft.com/pricing/details/active-directory/) voor Azure AD Free, Basic, Premium 1 of Premium 2 voor toegang tot de Azure AD-auditlogboeken in de Azure-portal. 
* Een Azure AD-tenant.
* Een gebruiker die een **globale beheerder** of **beveiligingsbeheerder** voor de Azure-tenant is.
* Een [licentie](https://azure.microsoft.com/pricing/details/active-directory/) voor Azure AD Premium 1 of Premium 2 voor toegang tot de Azure AD-aanmeldingslogboeken in de Azure-portal. 

Afhankelijk van waarnaar u uw auditlogboekgegevens wilt doorsturen, hebt u het volgende nodig:

* Een Azure-opslag account waarvoor u *listkeys ophalen* -machtigingen hebt. We raden u aan om een algemeen opslagaccount te gebruiken en geen Blob Storage-account. Zie de [Prijscalculator voor Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage) voor prijsinformatie over opslag. 
* Een Azure Event Hubs-naamruimte om te integreren met oplossingen van derden.
* Een Azure Log Analytics-werkruimte om logboeken naar Azure Monitor-logboeken te verzenden.

## <a name="cost-considerations"></a>Kostenoverwegingen

Als u al een Azure AD-licentie hebt, moet u een Azure-abonnement hebben om het opslagaccount en de Event Hub op te zetten. Het Azure-abonnement is gratis, maar u moet betalen om Azure-resources te gebruiken, waaronder het opslagaccount dat u gebruikt om te archiveren en de Event Hub die u gebruikt om te streamen. De hoeveelheid gegevens, en dus de in rekening gebrachte kosten, variëren sterk, afhankelijk van de grootte van de tenant. 

### <a name="storage-size-for-activity-logs"></a>Opslaggrootte voor activiteitenlogboeken

Elke gebeurtenis in een auditlogboek gebruikt ongeveer 2 kB gegevensopslag. Gebeurtenis logboeken voor aanmelden zijn ongeveer 4 KB aan gegevens opslag. Zo hebt u voor een tenant met 100.000 gebruikers, wat neerkomt op ongeveer 1,5 miljoen gebeurtenissen per dag, ongeveer 3 GB aan gegevensopslag per dag nodig. Aangezien schrijfbewerkingen in batches van ongeveer 5 minuten voorkomen, kunt u om en nabij 9000 schrijfbewerkingen per maand verwachten. 


De volgende tabel bevat een kostenraming, afhankelijk van de grootte van de tenant, voor een opslagaccount voor algemeen gebruik v2 in VS - west met een bewaringstermijn van ten minste één jaar. Gebruik de [Azure Storage-prijscalculator](https://azure.microsoft.com/pricing/details/storage/blobs/) om een meer nauwkeurige inschatting te maken van het gegevensvolume dat u denkt te gebruiken voor uw toepassing.


| Logboekcategorie | Aantal gebruikers | Gebeurtenissen per dag | Gegevensvolume per maand (geschat) | Kosten per maand (geschat) | Kosten per jaar (geschat) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Controleren | 100.000 | 1,5&nbsp;miljoen | 90 GB | $ 1,93 | $ 23,12 |
| Controleren | 1000 | 15.000 | 900 MB | $ 0,02 | $ 0,24 |
| Aanmeldingen | 1000 | 34.800 | 4 GB | $ 0,13 | $ 1,56 |
| Aanmeldingen | 100.000 | 15&nbsp;miljoen | 1,7 TB | $ 35,41 | $ 424,92 |
 









### <a name="event-hub-messages-for-activity-logs"></a>Event Hub-berichten voor activiteitenlogboeken

Gebeurtenissen worden ongeveer vijf minuten lang gebatched en vervolgens als één bericht met alle gebeurtenissen in die vijf minuten verstuurd. Een bericht in de Event Hub heeft een maximale grootte van 256 kB en als de totale grootte van alle berichten binnen de vijf minuten die grootte overstijgt, worden er meerdere berichten gestuurd. 

Zo vinden er voor een grote tenant met meer dan 100.000 gebruikers bijvoorbeeld doorgaans 18 gebeurtenissen per seconde plaats, een aantal dat overeenkomt met 5400 gebeurtenissen per vijf minuten. Omdat auditlogboeken ongeveer 2 kB per gebeurtenis groot zijn, komt dit overeen met 10,8 MB aan gegevens. Dat is de reden waarom er met die interval van 5 minuten 43 berichten naar de Event Hub worden gestuurd. 

De volgende tabel bevat de geschatte kosten per maand voor een Basic-Event Hub in het VS-West, afhankelijk van het volume van de gebeurtenis gegevens dat kan variëren van de Tenant naar de Tenant met een groot aantal factoren zoals aanmeldings gedrag van gebruikers, enzovoort. Gebruik de [Event hubs prijs calculator](https://azure.microsoft.com/pricing/details/event-hubs/)om een nauw keurige schatting te berekenen van het gegevens volume dat u verwacht voor uw toepassing.

| Logboekcategorie | Aantal gebruikers | Gebeurtenissen per seconde | Gebeurtenissen met een interval van vijf minuten | Volume per interval | Berichten per interval | Berichten per maand | Kosten per maand (geschat) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Controleren | 100.000 | 18 | 5400 | 10,8 MB | 43 | 371.520 | $ 10,83 |
| Controleren | 1000 | 0,1 | 52 | 104 kB | 1 | 8640 | $ 10,80 |
| Aanmeldingen | 100.000 | 18000 | 5.400.000 | 10,8 GB | 42188 | 364.504.320 | $23,9 |  
| Aanmeldingen | 1000 | 178 | 53.400 | 106,8&nbsp;MB | 418 | 3.611.520 | $ 11,06 |  

### <a name="azure-monitor-logs-cost-considerations"></a>Kosten overwegingen voor Azure Monitor-logboeken



| Logboekcategorie | Aantal gebruikers | Gebeurtenissen per dag | Gebeurtenissen per maand (30 dagen) | Kosten per maand in USD (EST.) |
|:-|--|--|--|-:|
| Controle en aanmeldingen | 100.000 | 16.500.000 | 495.000.000 | $1093,00 |
| Controleren | 100.000 | 1.500.000 | 45.000.000 | $246,66 |
| Aanmeldingen | 100.000 | 15.000.000 | 450.000.000 | $847,28 |










Als u de kosten voor het beheren van de Azure Monitor logboeken wilt bekijken, raadpleegt u [kosten beheren door het gegevens volume en de retentie in azure monitor logboeken](../../azure-monitor/logs/manage-cost-storage.md)te beheren.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

Deze sectie bevat antwoorden op veelgestelde vragen en bekende problemen met betrekking tot Azure AD-logboeken in Azure Monitor.

**V: welke logboeken zijn opgenomen?**

**A:** zowel de aanmeldings- als de auditlogboeken zijn beschikbaar om via deze functie doorgestuurd te worden. Gebeurtenissen met betrekking tot B2C worden momenteel niet ondersteund. Raadpleeg het [auditlogboekschema](reference-azure-monitor-audit-log-schema.md) en het [aanmeldingslogboekschema](reference-azure-monitor-sign-ins-log-schema.md) om uit te vinden welke typen logboeken en welke op functie gebaseerde logboeken momenteel worden ondersteund. 

---

**V: hoe snel na een actie de bijbehorende logboeken worden weer gegeven in mijn Event Hub?**

**A:**: de logboeken zouden binnen twee tot vijf minuten nadat de actie is uitgevoerd zichtbaar moeten zijn in uw Event Hub. Raadpleeg [Wat is Azure Event Hubs?](../../event-hubs/event-hubs-about.md) voor meer informatie over Event Hubs.

---

**V: hoe snel na een actie de bijbehorende logboeken worden weer gegeven in mijn opslag account?**

**A:** voor Azure-opslagaccounts ligt de latentie tussen de 5 en 15 minuten na het uitvoeren van een actie.

---

**V: wat gebeurt er als een beheerder de Bewaar periode van een diagnostische instelling wijzigt?**

**A**: het nieuwe Bewaar beleid wordt toegepast op Logboeken die na de wijziging zijn verzameld. Logboeken die worden verzameld voordat het beleid wordt gewijzigd, worden niet beïnvloed.

---

**V: hoeveel kost het om mijn gegevens op te slaan?**

**A:** de opslagkosten zijn afhankelijk van de grootte van uw logboeken en de door u gekozen bewaarperiode. Raadpleeg de sectie [Opslaggrootte voor activiteitenlogboeken](#storage-size-for-activity-logs) voor een lijst van de geschatte kosten voor tenants. De kosten zijn afhankelijk van het aantal logboeken dat wordt gegenereerd.

---

**V: hoeveel kost het om mijn gegevens naar een Event Hub te streamen?**

**A:** de kosten voor het streamen zijn afhankelijk van het aantal berichten dat u per minuut ontvangt. In dit artikel wordt beschreven hoe de kosten worden berekend en vindt u een lijst met geschatte kosten, die zijn gebaseerd op het aantal berichten. 

---

**V: Hoe kan ik de Azure Active Directory-activiteitenlogboeken integreren in mijn SIEM-systeem?**

**A**: U kunt dit op twee manieren doen:

- Azure Monitor gebruiken met Event Hubs om logboeken naar uw SIEM-systeem te streamen. [Stream de logboeken naar een event hub](tutorial-azure-monitor-stream-logs-to-event-hub.md) en [stel vervolgens uw SIEM-hulpprogramma](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub) in met de geconfigureerde event hub. 

- Gebruik de [Reporting Graph API](concept-reporting-api.md) voor toegang tot de gegevens. Push de gegevens daarna naar uw SIEM-systeem met uw eigen scripts.

---

**V: welke SIEM-hulpprogramma's worden momenteel ondersteund?** 

**A**: **op** dit moment wordt Azure monitor ondersteund door [Splunk](./howto-integrate-activity-logs-with-splunk.md), IBM QRadar, [Sumo Logic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory), [ArcSight](./howto-integrate-activity-logs-with-arcsight.md), LogRhythm en Logz.io. Raadpleeg [Azure-bewakingsgegevens streamen naar een Event Hub voor gebruik door een extern hulpprogramma](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md) voor meer informatie over hoe de connectors werken.

---

**V: Hoe kan ik de Azure Active Directory-activiteitenlogboeken integreren in mijn Splunk-exemplaar?**

**A**: [Routeer eerst de Azure Active Directory-activiteitenlogboeken naar een event hub](./tutorial-azure-monitor-stream-logs-to-event-hub.md) en volg daarna de stappen in [Activiteitenlogboeken integreren met Splunk](./howto-integrate-activity-logs-with-splunk.md).

---

**V: Hoe kan ik de Azure Active Directory-activiteitenlogboeken integreren met Sumo Logic?** 

**A**: [Routeer eerst de Azure Active Directory-activiteitenlogboeken naar een event hub](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory) en volg daarna de stappen in [De Azure Active Directory-toepassing installeren en de dashboards bekijken in SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards).

---

**V: heb ik toegang tot de gegevens van Event Hub zonder een extern SIEM-hulpprogramma?** 

**A**: Ja. U kunt de [Event Hubs-API](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) gebruiken om de logboeken vanuit uw eigen aangepaste toepassing te bekijken. 

---


## <a name="next-steps"></a>Volgende stappen

* [Activiteitenlogboeken in een opslagaccount archiveren](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Activiteitenlogboeken doorsturen naar een Event Hub](./tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Activiteiten logboeken integreren met Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md)