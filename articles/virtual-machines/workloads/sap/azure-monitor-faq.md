---
title: Veelgestelde vragen - Azure Monitor for SAP Solutions | Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Monitor voor SAP-oplossingen.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 4b84f07f637b0a8925dec96c8c609101247ffd64
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377121"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Azure Monitor veelgestelde vragen over SAP-oplossingen (preview)
## <a name="frequently-asked-questions"></a>Veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Monitor voor SAP-oplossingen.  

 - **Moet ik betalen voor Azure Monitor for SAP Solutions?**  
Er zijn geen licentiekosten voor Azure Monitor for SAP Solutions.  
Klanten zijn echter verantwoordelijk voor de kosten van beheerde resourcegroeponderdelen.  

 - **In welke regio's is deze service beschikbaar voor openbare preview?**  
Voor de openbare preview is deze service beschikbaar in VS - oost 2, VS - west 2, VS - oost en Europa - west.  

 - **Moet ik machtigingen geven om de implementatie van een beheerde resourcegroep in mijn abonnement toe te staan?**  
Nee, expliciete machtigingen zijn niet vereist.  

 - **Waar bevindt de collector-VM zich?**  
Op het moment van de implementatie van Azure Monitor for SAP Solutions resource raden we klanten aan hetzelfde VNET te kiezen voor het bewaken van de resource als hun SAP HANA server. Daarom wordt aanbevolen collector-VM zich in hetzelfde VNET als de SAP HANA bevinden. Als klanten een niet-HANA-database gebruiken, bevindt de collector-VM zich in hetzelfde VNET als de niet-HANA-database.  

 - **Welke versies van HANA worden ondersteund?**  
HANA 1.0 SPS 12 (Rev. 120 of hoger) en HANA 2.0 SPS03 of hoger  

 - **Welke HANA-implementatieconfiguraties worden ondersteund?**  
De volgende configuraties worden ondersteund:
   - Eén knooppunt (omhoog schalen) en meerdere knooppunt (uitschalen)  
   - Individuele databasecontainer (HANA 1.0 SPS 12) en meerdere databasecontainers (HANA 1.0 SPS 12 of HANA 2.0)  
   - Automatische host-failover (n+1) en HSR  

 - **Welke SQL Server worden ondersteund?**  
SQL Server 2012 SP4 of hoger.  

 - **Welke SQL Server worden ondersteund?**  
De volgende configuraties worden ondersteund:
   - Standaard of benoemde zelfstandige exemplaren in een virtuele machine  
   - Geclusterde exemplaren of exemplaren in een AlwaysOn-configuratie wanneer u de virtuele naam van de geclusterde resource of de Naam van de AlwaysOn-listener gebruikt. Er worden momenteel geen specifieke metrische gegevens voor clusters of AlwaysOn verzameld    
   - Azure SQL Database (PAAS) wordt momenteel niet ondersteund  

 - **Wat gebeurt er als ik per ongeluk de beheerde resourcegroep verwijder?**  
De beheerde resourcegroep is standaard vergrendeld. Daarom is de kans op onbedoeld verwijderen van de beheerde resourcegroep door klanten minuscul.  
Als een klant de beheerde resourcegroep verwijdert, Azure Monitor for SAP Solutions niet meer werken. De klant moet een nieuwe resource Azure Monitor for SAP Solutions implementeren en opnieuw beginnen.  

 - **Welke rollen heb ik nodig in mijn Azure-abonnement om de resource Azure Monitor for SAP Solutions implementeren?**  
Rol inzender.  

 - **Wat is de SLA voor dit product?**  
Previews worden uitgesloten van serviceovereenkomsten. Lees de volledige licentietermijn via Azure Monitor for SAP Solutions marketplace-afbeelding.  

 - **Kan ik mijn hele landschap bewaken via deze oplossing?**  
U kunt momenteel de HANA-database, de onderliggende infrastructuur, het cluster met hoge beschikbaarheid, Microsoft SQL Server, SAP Netweaver-beschikbaarheid en beschikbaarheidsgegevens van SAP Application Instance bewaken in de openbare preview.  

 - **Vervangt deze service SAP Solution Manager?**  
Nee. Klanten kunnen nog steeds SAP Solution Manager gebruiken voor de bewaking van bedrijfsproces.  

 - **Wat is de waarde van deze service boven traditionele oplossingen zoals SAP HANA Cockpit/Studio?**  
Azure Monitor for SAP Solutions is niet specifiek voor de HANA-database. Azure Monitor for SAP Solutions ondersteunt ook AnyDB.  

- **Welke SAP NetWeaver-versies worden ondersteund?**  
SAP NetWeaver 7.0 of hoger.  

- **Welke SAP NetWeaver-configuraties worden ondersteund?**  
Ondersteunt ABAP-, Java- en dual-stack SAP NetWeaver-toepassingsserverconfiguraties.

- **Waarom moet ik de beveiliging van methoden voor sap NetWeaver-toepassingsbewaking opheffen?**  
In SAP-releases >= 7.3 worden de meeste webservicemethoden standaard beveiligd. Als u metrische gegevens over beschikbaarheid en prestaties wilt ophalen door deze methoden aan te roepen, moet u de beveiliging van de volgende methoden opheffen: GetQueueStatistic, ABAPGetWPTable, GetProcessList, EnqGetStatistic en GetSystemInstancelist.

- **Is er een risico bij het opheffen van de beveiliging van SAPCONTROL-webmethods?**  
Over het algemeen vormt het opheffen van SAPCONTROL-webmethods geen beveiligingsrisico als [zodanig.](https://launchpad.support.sap.com/#/notes/1439348)Als klanten echter de toegang tot de niet-beveiligde webmethods willen beperken/verbieden via serverpoorten (5XX13/ 5XX14) van sapstartsrv, kunt u dit doen door een filter toe te voegen in SAP Access Control List (ACL), in de [OSS-opmerking](https://service.sap.com/sap/support/notes/1495075) wordt de vereiste configuratie beschreven om dit te bereiken. 

- **Moet ik mijn SAP-exemplaren opnieuw opstarten na het uitvoeren van systeemconfiguraties voor het instellen van de SAP NetWeaver-provider?**  
Ja, zodra u onbeveiligde methoden via SAP-configuratiewijzigingen hebt, moet u de respectieve SAP-systemen opnieuw opstarten om ervoor te zorgen dat de configuratiewijzigingen worden bijgewerkt.  

## <a name="next-steps"></a>Volgende stappen

- Maak uw eerste Azure Monitor resource voor SAP-oplossingen.
