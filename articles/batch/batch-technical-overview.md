---
title: Met Azure Batch worden grootschalige parallelle jobs uitgevoerd in de cloud
description: Meer informatie over het gebruik van de Azure Batch-service voor grootschalige parallelle en HPC-workloads
ms.topic: overview
ms.date: 07/30/2020
ms.openlocfilehash: dbd27dc1a00966a2d71952335cfb47c7ca55bc24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743097"
---
# <a name="what-is-azure-batch"></a>Wat is Azure Batch?

Met Azure Batch kunt u grootschalige parallelle en HPC-batchrekentaken (High Performance Computing) efficiënt uitvoeren in Azure. Azure Batch maakt en beheert een pool van rekenknooppunten (virtuele machines), installeert de toepassingen die u wilt uitvoeren, en plant het uitvoeren van taken op de knooppunten. Er is geen cluster- of taakplanner-software die u moet installeren, beheren of schalen. In plaats daarvan gebruikt u [Batch-API's en hulpprogramma's](batch-apis-tools.md), scripts voor de opdrachtregel of de Azure-portal voor het configureren, beheren en bewaken van uw taken.

Ontwikkelaars kunnen Batch gebruiken als een platformservice voor het bouwen van de SaaS-toepassingen of de client-apps waar grootschalige uitvoering is vereist. U kunt bijvoorbeeld een service met Batch bouwen die een Monte Carlo risicosimulatie uitvoert voor een financiële dienstverlener, of een service voor het verwerken van vele installatiekopieën.

Er worden geen extra kosten berekend voor het gebruik van Batch. U betaalt alleen voor bijbehorende verbruikte resources, zoals de virtuele machines, opslag en netwerken.

Zie [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/) voor een vergelijking tussen Batch en andere HPC-oplossingsopties in Azure.

## <a name="run-parallel-workloads"></a>Uitvoeren van parallelle workloads

Batch is bijzonder geschikt voor intrinsiek parallelle (ook wel bekend als ‘perfect parallelle’) workloads. Bij intrinsiek parallelle workloads kunnen de toepassingen onafhankelijk worden uitgevoerd, waarbij elke instantie een deel van het werk uitvoert. Wanneer de toepassingen worden uitgevoerd, kunnen ze toegang krijgen tot een aantal algemene gegevens, maar ze communiceren niet met andere instanties van de toepassing. Intrinsiek parallelle workloads kunnen daarom op grote schaal worden uitgevoerd. Dit wordt bepaald door de hoeveelheid rekenresources die beschikbaar zijn voor toepassingen die tegelijkertijd worden uitgevoerd.

Enkele voorbeelden van intrinsiek parallelle workloads die u kunt overbrengen naar Batch:

- Modellering van financiële risico's met behulp van Monte Carlo-simulaties
- Rendering van VFX en 3D-afbeeldingen
- Analyse en verwerking van beelden
- Transcodering van media
- Genetische sequentieanalyse
- Optische tekenherkenning (OCR)
- Gegevensopname, -verwerking en ETL-bewerkingen
- Software-testuitvoering

U kunt met Batch ook [nauw gekoppelde workloads uitvoeren](batch-mpi.md), waarbij de toepassingen die u uitvoert met elkaar moeten communiceren, in plaats van dat ze onafhankelijk van elkaar worden uitgevoerd. Nauw gekoppelde toepassingen gebruiken normaal gesproken de Message Passing Interface (MPI) API. U kunt uw nauw gekoppelde workloads met Batch uitvoeren met behulp van [Microsoft MPI](/message-passing-interface/microsoft-mpi) of Intel MPI. Toepassingsprestaties verbeteren met gespecialiseerde [HPC](../virtual-machines/sizes-hpc.md) en [GPU-geoptimaliseerde](../virtual-machines/sizes-gpu.md) VM-grootten.

Enkele voorbeelden van nauw gekoppelde workloads:

- Beperkte elementanalyse
- Vloeiende dynamics
- AI-training met meerdere knooppunten

Met behulp van Batch kunnen veel nauw gekoppelde taken parallel worden uitgevoerd. Voer bijvoorbeeld meerdere simulaties uit van een vloeistof die door een leiding met verschillende leidingbreedtes loopt.

## <a name="additional-batch-capabilities"></a>Aanvullende functies van Batch

Workload-specifieke mogelijkheden van een hoger niveau zijn voor Azure Batch ook beschikbaar:

- Batch ondersteunt grootschalige [rendering-workloads](batch-rendering-service.md) met hulpprogramma's voor rendering inclusief Autodesk Maya, 3ds Max, Arnold en V-Ray. 
- R-gebruikers kunnen het [doAzureParallel R-pakket](https://github.com/Azure/doAzureParallel) installeren om eenvoudig de uitvoering van R-algoritmen voor Batch-pools uit te schalen.

U kunt ook Batch-taken uitvoeren als onderdeel van een grotere Azure-werkstroom, beheerd door hulpprogramma's zoals [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md).

## <a name="how-it-works"></a>Hoe werkt het?

Een gangbare oplossing voor Batch, bestaat uit het uitschalen van intrinsiek parallel werk, zoals de rendering van beelden voor 3D-scènes, op een pool van rekenknooppunten. Deze pool kan uw 'render farm' zijn, die tientallen, honderden of zelfs duizenden kernen aan uw renderingjob beschikbaar stelt.

Het volgende diagram toont een algemene Batch-werkstroom, met een clienttoepassing of gehoste service die gebruikmaakt van Batch om een parallelle workload uit te voeren.

![Diagram van de stappen in een batch-oplossing.](./media/batch-technical-overview/tech_overview_03.png)

|Stap  |Beschrijving  |
|---------|---------|
|1.  Upload de **invoerbestanden** en de **toepassingen** die deze bestanden zullen verwerken naar uw Azure Storage-account.     |De invoerbestanden kunnen alle gegevens zijn die uw toepassing zal verwerken, zoals de modellering van financiële gegevens of te transcoderen videobestanden. De toepassingsbestanden kunnen scripts of toepassingen bevatten die de gegevens verwerken, zoals een mediatranscoder.|
|2.  Maak een Batch-**pool** van rekenknooppunten in uw Batch-account, een **taak** voor het uitvoeren van de workload op de groep, en **opdrachten** binnen de taak.     | [Rekenknooppunten](nodes-and-pools.md) zijn de virtuele machines die uw [taken](jobs-and-tasks.md) uitvoeren. Geef eigenschappen voor de pool op, zoals het aantal en de grootte van de knooppunten, een Windows- of Linux-VM-installatiekopie, en een toepassing die wordt geïnstalleerd wanneer de knooppunten aan de pool worden toegevoegd. Beheer de kosten en de grootte van de pool met behulp van [lage-prioriteit VM's](batch-low-pri-vms.md) of door het [automatisch schalen](batch-automatic-scaling.md) van het aantal knooppunten als de workload wordt gewijzigd. <br/><br/>Wanneer u taken aan een taak toevoegt, plant de Batch-service automatisch de taken voor uitvoering op de rekenknooppunten in de pool. Elke taak maakt gebruik van de toepassing die u hebt geüpload om de invoerbestanden te verwerken. |
|3.  Download **invoerbestanden** en de **toepassingen** in Batch     |Voordat elke taak wordt uitgevoerd, kan deze de invoerbestanden naar het toegewezen knooppunt downloaden. Als de toepassing nog niet op de pool-knooppunten is geïnstalleerd, kan deze in plaats daarvan hier worden gedownload. Als het downloaden vanuit Azure Storage is voltooid, wordt de taak wordt uitgevoerd op het toegewezen knooppunt.|
|4.  **Taakuitvoering** controleren     |Wanneer de taken worden uitgevoerd, kunt u in Batch een query uitvoeren om de voortgang van de taak en de bijbehorende opdrachten te controleren. Uw clienttoepassing of -service communiceert met de Batch-service via HTTPS. Omdat u mogelijk duizenden taken bewaakt die worden uitgevoerd op duizenden rekenknooppunten, moet u ervoor zorgen dat u [een query op de Batch-service op efficiënte wijze uitvoert](batch-efficient-list-queries.md).|
|5.  **Taakuitvoer** uploaden     |Nadat de taken zijn voltooid, kunnen ze hun resultaatgegevens uploaden naar Azure Storage. U kunt ook bestanden rechtstreeks ophalen van het bestandssysteem op een rekenknooppunt.|
|6.  **Uitvoerbestanden** downloaden     |Wanneer u bij uw controle detecteert dat de taken in uw job zijn voltooid, kan uw clienttoepassing of service de uitvoergegevens downloaden voor verdere verwerking.|

De hierboven beschreven werkstroom is slechts één manier waarop Batch kan worden gebruikt, maar Batch biedt nog veel meer functies en mogelijkheden. Bijvoorbeeld, kunt u [meerdere taken parallel](batch-parallel-node-tasks.md) op elk rekenknooppunt uitvoeren. U kunt ook [taakvoorbereiding en -voltooiingsopdrachten](batch-job-prep-release.md) gebruiken om de knooppunten voor te bereiden op uw taken, en ze daarna opschonen.

Zie [Werkstroom van de Batch-service en resources](batch-service-workflow-features.md) voor een overzicht van functies als pools, knooppunten, jobs en taken. Zie ook de meest recente [Batch-service-updates](https://azure.microsoft.com/updates/?product=batch).

## <a name="in-region-data-residency"></a>Gegevenslocatie in uw regio
Azure Batch verplaatst of slaat klant gegevens niet uit de regio waarin deze is geïmplementeerd. 

## <a name="next-steps"></a>Volgende stappen

Lees een van deze snelstartgidsen om snel aan de slag te gaan met Azure Batch:
- [Uw eerste Batch-taak uitvoeren met Azure CLI](quick-create-cli.md)
- [Uw eerste Batch-taak uitvoeren met de Azure-portal](quick-create-portal.md)
- [Uw eerste Batch-taak uitvoeren met de .NET-API](quick-run-dotnet.md)
- [Uw eerste Batch-taak uitvoeren met de Python-API](quick-run-python.md)
