---
title: Toepassingen en gegevens kopiëren naar pool knooppunten
description: Meer informatie over het kopiëren van toepassingen en gegevens naar pool knooppunten.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: a5933a1c52e2848b6b414f1750bb24515fb9f28a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378499"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>Toepassingen en gegevens kopiëren naar pool knooppunten

Azure Batch ondersteunt verschillende manieren voor het ophalen van gegevens en toepassingen op reken knooppunten, zodat deze beschikbaar zijn voor gebruik door taken.

De methode die u kiest, kan afhankelijk zijn van het bereik van uw bestand of toepassing. Gegevens en toepassingen zijn mogelijk vereist voor het uitvoeren van de volledige taak en moeten dus op elk knoop punt worden geïnstalleerd. Sommige bestanden of toepassingen zijn mogelijk alleen vereist voor een specifieke taak. Andere moeten voor de taak worden geïnstalleerd, maar moeten niet op elk knoop punt aanwezig zijn. Batch bevat hulpprogram ma's voor elk van deze scenario's.

## <a name="determine-the-scope-required-of-a-file"></a>Het bereik bepalen dat vereist is voor een bestand

U moet het bereik van een bestand bepalen: het vereiste bestand voor een groep, een taak of een taak. Bestanden die aan de groep zijn toegewezen, moeten groeps toepassings pakketten of een begin taak gebruiken. Bestanden die aan de taak zijn toegewezen, moeten een taak voorbereidings taak gebruiken. Een goed voor beeld van bestanden binnen het bereik van de groep of het taak niveau zijn toepassingen. Bestanden die aan de taak zijn toegewezen, moeten taak bron bestanden gebruiken.

## <a name="pool-start-task-resource-files"></a>Resource bestanden voor taak groep starten

Voor toepassingen of gegevens die op elk knoop punt in de pool moeten worden geïnstalleerd, gebruikt u resource bestanden van de groep Start taken. Gebruik deze methode samen met een [toepassings pakket](batch-application-packages.md) of de bron bestands verzameling van de begin taak om een installatie opdracht uit te voeren.  

U kunt bijvoorbeeld de opdracht regel voor het starten van de taak gebruiken om toepassingen te verplaatsen of te installeren. U kunt ook een lijst met bestanden of containers in een Azure-opslag account opgeven. Zie [# resource file toevoegen in rest-documentatie](/rest/api/batchservice/pool/add#resourcefile)voor meer informatie.

Als elke taak die wordt uitgevoerd in de pool, een toepassing (. exe) uitvoert die eerst moet worden geïnstalleerd met een MSI-bestand, moet u de eigenschap **wachten op geslaagd** instellen op **True**. Zie [# StartTask toevoegen in rest-documentatie](/rest/api/batchservice/pool/add#starttask)voor meer informatie.

## <a name="application-package-references"></a>Toepassings pakket verwijzingen

Voor toepassingen of gegevens die op elk knoop punt in de pool moeten worden geïnstalleerd, kunt u gebruikmaken van [toepassings pakketten](batch-application-packages.md). Er is geen installatie opdracht gekoppeld aan een toepassings pakket, maar u kunt een begin taak gebruiken om elke installatie opdracht uit te voeren. Als voor uw toepassing geen installatie of een groot aantal bestanden is vereist, kunt u deze methode gebruiken.

Toepassings pakketten zijn handig wanneer u een groot aantal bestanden hebt, omdat ze veel bestands verwijzingen kunnen combi neren in een kleine nettolading. Als u meer dan 100 afzonderlijke bron bestanden wilt toevoegen aan één taak, kan de batch-service worden uitgevoerd op basis van interne systeem beperkingen voor één taak. Toepassings pakketten zijn ook handig wanneer u veel verschillende versies van dezelfde toepassing hebt en er een moet kiezen.

## <a name="extensions"></a>Uitbreidingen

[Uitbrei dingen](create-pool-extensions.md) zijn kleine toepassingen die de configuratie van het achteraf inrichten en instellen voor batch Compute-knoop punten vergemakkelijken. Wanneer u een pool maakt, kunt u een ondersteunde uitbrei ding selecteren die moet worden geïnstalleerd op de reken knooppunten wanneer deze worden ingericht. Daarna kan de uitbrei ding de beoogde bewerking uitvoeren.

## <a name="job-preparation-task-resource-files"></a>Resource bestanden voor taak voorbereidings taak

Voor toepassingen of gegevens die moeten worden geïnstalleerd om de taak uit te voeren, maar die niet moeten worden geïnstalleerd in de hele groep, kunt u overwegen [taak voorbereidings taak resource bestanden](./batch-job-prep-release.md)te gebruiken.

Als uw pool bijvoorbeeld veel verschillende typen taken heeft en slechts één taak type een. msi-bestand nodig heeft om uit te voeren, is het zinvol om de installatie Step Into een taak voorbereidings taak te plaatsen.

## <a name="task-resource-files"></a>Resource bestanden van taak

Resource bestanden van de taak zijn geschikt wanneer uw toepassing of gegevens alleen relevant zijn voor een afzonderlijke taak.

Zo kunt u vijf taken hebben, elke verwerking van een ander bestand en vervolgens de uitvoer naar Blob Storage in dit geval moet het invoer bestand worden opgegeven voor de verzameling taak bron bestanden, omdat elke taak een eigen invoer bestand heeft.

## <a name="additional-ways-to-get-data-onto-nodes"></a>Meer manieren om gegevens op knoop punten op te halen

Omdat u controle hebt over Azure Batch knoop punten en aangepaste uitvoer bare bestanden kunt uitvoeren, kunt u gegevens uit een wille keurig aantal aangepaste bronnen ophalen. Zorg ervoor dat het batch-knoop punt verbinding heeft met het doel en dat u referenties hebt voor die bron op het knoop punt.

Enkele voor beelden van manieren om gegevens over te dragen naar batch knooppunten:

- Gegevens downloaden van SQL
- Gegevens downloaden van andere webservices/aangepaste locaties
- Een netwerk share toewijzen

## <a name="azure-storage"></a>Azure Storage

Houd er bij het downloaden van Blob-opslag doel stellingen voor de schaal baarheid in. De schaalbaarheids doelen van de bestands share van Azure Storage zijn hetzelfde als voor één blob. De grootte is van invloed op het aantal knoop punten en groepen dat u nodig hebt.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van [toepassings pakketten met batch](batch-application-packages.md).
- Meer informatie over het [werken met knoop punten en groepen](nodes-and-pools.md).
