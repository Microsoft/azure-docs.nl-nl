---
title: Overzicht van Azure Stream Analytics clusters
description: Meer informatie over de speciale aanbieding voor één tenant van Stream Analytics Cluster.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: e5157a02f97370b20db85bf5e3e8aae98a2d8668
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709288"
---
# <a name="overview-of-azure-stream-analytics-cluster"></a>Overzicht van Azure Stream Analytics cluster

Azure Stream Analytics Cluster is een implementatie met één tenant die kan worden gebruikt voor complexe en veeleisende streaming-scenario's. Op de volledige schaal kunnen Stream Analytics-clusters in realtime meer dan 200 MB/seconde verwerken. Stream Analytics-taken die worden uitgevoerd op toegewezen clusters kunnen gebruikmaken van alle functies in de standaardaanbieding en biedt ondersteuning voor de connectiviteit van particuliere koppelingen met uw invoer en uitvoer.

Stream Analytics-clusters worden gefactureerd op basis van streaming-eenheden (SU’s) die de hoeveelheid CPU- en geheugenresources vertegenwoordigen die aan uw cluster zijn toegewezen. Een streaming-eenheid is hetzelfde voor standaard- en speciale aanbiedingen. U kunt voor elk cluster 36, 72, 108, 144, 180 of 216 SU’s kopen. Een Stream Analytics-cluster kan fungeren als het streaming-platform voor uw organisatie en kan worden gedeeld door verschillende teams die aan verschillende use cases werken.

## <a name="what-are-stream-analytics-clusters"></a>Wat zijn Stream Analytics-clusters

Stream Analytics-clusters worden aangestuurd door dezelfde engine die Stream Analytics-taken uitvoert die worden uitgevoerd in een omgeving met meerdere tenants. Het toegewezen cluster met één tenant heeft de volgende kenmerken:

* Één tenant-hosting zonder ruis van andere tenants. Uw resources zijn echt geïsoleerd en worden verbeterd wanneer er sprake is van burst-verkeer.

* Schaal uw cluster in een periode van 36 tot 216 SU’s naarmate uw streaming-gebruik in de loop van de tijd toeneemt.

* VNet-ondersteuning waarmee uw Stream Analytics-taken veilig verbinding kunnen maken met andere bronnen met behulp van privé-eindpunten.

* De mogelijkheid om door de C#-gebruiker gedefinieerde functies en aangepaste deserialisatie te schrijven in elke regio.

* Er zijn geen onderhoudskosten, zodat u zich kunt richten op het bouwen van real-time analyseoplossingen.

## <a name="how-to-get-started"></a>Hoe gaat u aan de slag

U kunt [een Stream Analytics-cluster maken](create-cluster.md) via de [Azure Portal](https://aka.ms/asaclustercreateportal). Als u vragen hebt of hulp nodig hebt bij het onboarden, kunt u contact opnemen met het [Stream Analytics-team](mailto:askasa@microsoft.com).

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="how-do-i-choose-between-a-stream-analytics-cluster-and-a-stream-analytics-job"></a>Hoe kan ik kiezen tussen een Stream Analytics-cluster en een Stream Analytics-taak?

De eenvoudigste manier om aan de slag te gaan is het maken en ontwikkelen van een Stream Analytics-taak om vertrouwd te raken met de service en te zien hoe deze kan voldoen aan uw analysevereisten.

Stream Analytics-taken op zich bieden geen ondersteuning voor VNets. Als uw invoer of uitvoer is beveiligd achter een firewall of een Azure Virtual Network, hebt u de volgende twee opties:

* Als uw lokale machine toegang heeft tot de invoer-en uitvoerbronnen die worden beveiligd door een VNet (bijvoorbeeld Azure Event Hubs of Azure SQL Database), kunt u [Azure Stream Analytics-hulpprogramma's voor Visual Studio](stream-analytics-tools-for-visual-studio-install.md) installeren op uw lokale computer. U kunt [Stream Analytics-taken lokaal op uw apparaat testen](stream-analytics-live-data-local-testing.md), zonder dat er kosten in rekening worden gebracht. Wanneer u klaar bent om Stream Analytics in uw architectuur te gebruiken, kunt u vervolgens een Stream Analytics-cluster maken, privé-eindpunten configureren en uw taken op schaal uitvoeren.

* U kunt een Stream Analytics-cluster maken, het cluster configureren met de privé-eindpunten die nodig zijn voor de pijplijn en uw Stream Analytics-taken uitvoeren op het cluster.

### <a name="what-performance-can-i-expect"></a>Welke prestaties kan ik verwachten?

Een SU is hetzelfde voor standaard- en speciale aanbiedingen. Een enkele taak die gebruikmaakt van een volledig 36 SU-cluster kan een doorvoer van ongeveer 36 MB/seconde behalen met een milliseconde-latentie. Het exacte aantal is afhankelijk van de indeling van gebeurtenissen en het type analyse. Omdat het toegewezen is, biedt het Stream Analytics-cluster betrouwbaardere prestatiegaranties. Alle taken die in uw cluster worden uitgevoerd, horen alleen bij u.

### <a name="can-i-scale-my-cluster"></a>Kan ik mijn cluster schalen?

Ja. U kunt eenvoudig de capaciteit van uw cluster configureren, zodat u naar wens [omhoog of omlaag kunt schalen](scale-cluster.md) om te voldoen aan de veranderende vraag.

### <a name="can-i-run-my-existing-jobs-on-these-new-clusters-ive-created"></a>Kan ik mijn bestaande taken uitvoeren op deze nieuwe clusters die ik heb gemaakt?

Ja. U kunt uw bestaande taken koppelen aan uw nieuw gemaakte Stream Analytics-cluster en dit op de gebruikelijke manier uitvoeren. U hoeft uw bestaande Stream Analytics-taken niet helemaal opnieuw te maken.

### <a name="how-much-will-these-clusters-cost-me"></a>Wat zijn mijn kosten voor deze clusters?

Uw Stream Analytics-clusters worden in rekening gebracht op basis van de gekozen SU-capaciteit. Clusters worden elk uur gefactureerd en er zijn geen extra kosten per taak die in deze clusters wordt uitgevoerd. Zie de [Pagina met prijzen van de Private Link-service](https://azure.microsoft.com/pricing/details/private-link/) voor factureringsupdates van het privé-eindpunt.

### <a name="which-inputs-and-outputs-can-i-privately-connect-to-from-my-stream-analytics-cluster"></a>Met welke invoer en uitvoer kan ik privé verbinding maken vanuit mijn Stream Analytics-cluster?

Stream Analytics ondersteunt diverse typen invoer en uitvoer. U kunt [persoonlijke eindpunten maken](private-endpoints.md) in uw cluster waarmee taken toegang kunnen krijgen tot de invoer- en uitvoerbronnen. Momenteel zijn de ondersteunde services Azure SQL Database, Azure Storage, Azure Data Lake Storage Gen2, Azure Event Hub en Azure Service Bus en andere typen worden binnenkort toegevoegd. 

## <a name="next-steps"></a>Volgende stappen

U hebt nu een overzicht van Azure Stream Analytics Cluster. Vervolgens kunt u uw cluster maken en uw Stream Analytics-taak uitvoeren: 

* [Een Stream Analytics-cluster maken](create-cluster.md)
* [Privé-eindpunten maken en verwijderen in een Azure Stream Analytics-cluster](private-endpoints.md)
* [Stream Analytics-taken beheren in een Stream Analytics-cluster](manage-jobs-cluster.md)
* [Een Stream Analytics-taak maken](stream-analytics-quick-create-portal.md)
