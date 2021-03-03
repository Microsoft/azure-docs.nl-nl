---
title: Veelgestelde vragen-Azure Monitor voor SAP-oplossingen | Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Monitor voor SAP-oplossingen.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 3732189c1d2e09b648a2fba0a39e7e4113a76d48
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675945"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Veelgestelde vragen over de Azure Monitor voor SAP-oplossingen (preview-versie)
## <a name="frequently-asked-questions"></a>Veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Monitor voor SAP-oplossingen.  

 - **Moet ik betalen voor Azure Monitor voor SAP-oplossingen?**  
Er zijn geen licentie kosten voor Azure Monitor voor SAP-oplossingen.  
Klanten zijn echter verantwoordelijk voor de kosten van de onderdelen van de beheerde resource groep.  

 - **In welke regio's is deze service beschikbaar voor een open bare preview?**  
Voor een open bare preview is deze service beschikbaar in VS-Oost 2, VS-West 2, VS-Oost en Europa-west.  

 - **Moet ik machtigingen opgeven voor het toestaan van de implementatie van een beheerde resource groep in mijn abonnement?**  
Nee, expliciete machtigingen zijn niet vereist.  

 - **Waar bevindt de Collector-VM zich?**  
Op het moment van de implementatie van Azure Monitor voor SAP-oplossingen, raden we klanten aan om hetzelfde VNET te kiezen voor het bewaken van resources als hun SAP HANA server. Daarom wordt de virtuele machine van de Collector aanbevolen om zich te bevinden in hetzelfde VNET als SAP HANA-server. Als klanten een niet-HANA-Data Base gebruiken, bevinden de virtuele machine van de Collector zich in hetzelfde VNET als de niet-HANA-data base.  

 - **Welke versies van HANA worden ondersteund?**  
HANA 1,0 SPS 12 (Rev. 120 of hoger) en HANA 2,0 SPS03 of hoger  

 - **Welke HANA-implementatie configuraties worden ondersteund?**  
De volgende configuraties worden ondersteund:
   - Eén knoop punt (omhoog schalen) en meerdere knoop punten (uitschalen)  
   - Eén database container (HANA 1,0 SPS 12) en meerdere database containers (HANA 1,0 SPS 12 of HANA 2,0)  
   - Failover van automatische host (n + 1) en HSR  

 - **Welke SQL Server versies worden ondersteund?**  
SQL Server 2012 SP4 of hoger.  

 - **Welke SQL Server configuraties worden ondersteund?**  
De volgende configuraties worden ondersteund:
   - Standaard of benoemde zelfstandige instanties in een virtuele machine  
   - Geclusterde instanties of instanties in een AlwaysOn-configuratie wanneer u de virtuele naam van de geclusterde bron of de AlwaysOn-listener-naam gebruikt. Er zijn momenteel geen cluster-of AlwaysOn-specifieke metrische gegevens verzameld    
   - Azure SQL Database (PAAS) wordt momenteel niet ondersteund  

 - **Wat gebeurt er als ik de beheerde resource groep per ongeluk verwijder?**  
De beheerde resource groep is standaard vergrendeld. Daarom is de kans op onopzettelijke verwijdering van de beheerde resource groep door klanten minuscule.  
Als een klant de beheerde resource groep verwijdert, werkt Azure Monitor voor SAP-oplossingen niet meer. De klant moet een nieuwe Azure Monitor voor de SAP-oplossingen resource implementeren en opnieuw starten.  

 - **Welke rollen heb ik nodig in mijn Azure-abonnement om Azure Monitor voor SAP-oplossingen te implementeren?**  
Rol Inzender.  

 - **Wat is de SLA van dit product?**  
Previews worden uitgesloten van service overeenkomsten. Lees de volledige licentie termijn via Azure Monitor voor SAP Solutions Marketplace-installatie kopie.  

 - **Kan ik mijn hele landschap bewaken via deze oplossing?**  
U kunt de HANA-data base, de onderliggende infra structuur, het cluster met hoge Beschik baarheid en micro soft SQL Server op een open bare preview controleren.  

 - **Wordt SAP Solution Manager vervangen door deze service?**  
Nee. Klanten kunnen nog steeds SAP Solution Manager gebruiken voor de bewaking van bedrijfs processen.  

 - **Wat is de waarde van deze service via traditionele oplossingen, zoals SAP HANA cockpit/Studio?**  
Azure Monitor voor SAP-oplossingen is geen HANA-data base-specifiek. Azure Monitor voor SAP-oplossingen ondersteunt ook AnyDB.  

## <a name="next-steps"></a>Volgende stappen

- Maak uw eerste Azure Monitor voor SAP-oplossingen resource.
