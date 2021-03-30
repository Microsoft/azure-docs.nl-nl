---
title: Fout afhandeling en detectie in Azure Batch
description: Meer informatie over het afhandelen van fouten in batch service werk stromen vanuit een ontwikkelings oogpunt.
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: 3bd460598dae08fa18415e1c9865249f3ca4c9c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85964274"
---
# <a name="error-handling-and-detection-in-azure-batch"></a>Fout afhandeling en detectie in Azure Batch

Soms is het nood zakelijk dat u zowel taak-als toepassings fouten in uw batch-oplossing kunt verwerken. Dit artikel spreekt over soorten fouten en hoe u deze kunt oplossen.

## <a name="error-codes"></a>Foutcodes

Algemene typen fouten zijn onder andere:

- Netwerk storingen voor aanvragen die geen batch hebben geduurd of wanneer het batch-antwoord de client niet in de tijd heeft bereikt.
- Interne server fouten (HTTP-antwoord standaard 5xx-status code).
- Beperkings fouten, zoals 429 of 503 status code HTTP-antwoorden met de header opnieuw proberen na.
- 4xx-fouten zoals bestaat al en InvalidOperation. Dit betekent dat de resource niet de juiste status heeft voor de status overgang.

Zie [batch-status en fout codes](/rest/api/batchservice/batch-status-and-error-codes)voor meer informatie over specifieke fout codes, waaronder fout codes voor rest API, batch-service en taak/planning van taken.

## <a name="application-failures"></a>Toepassingsfouten

Bij het uitvoeren van een toepassing kan deze een diagnostische uitvoer produceren die handig is voor het oplossen van problemen. Zoals beschreven in [bestanden en mappen](files-and-directories.md), schrijft de batch-service standaard uitvoer en de standaard uitvoer van fouten naar `stdout.txt` en `stderr.txt` bestanden in de taakmap op het reken knooppunt.

U kunt Azure Portal of een van de Batch-SDK's gebruiken om deze bestanden te downloaden. U kunt deze en andere bestanden bijvoorbeeld ophalen om problemen op te lossen met behulp van [ComputeNode.GetNodeFile](/dotnet/api/microsoft.azure.batch.computenode) en [CloudTask.GetNodeFile](/dotnet/api/microsoft.azure.batch.cloudtask) in de .NET-bibliotheek van Batch.

## <a name="task-errors"></a>Taak fouten

Taak fouten vallen in verschillende categorieën.

### <a name="pre-processing-errors"></a>Fouten vóór verwerking

Als een taak niet kan worden gestart, wordt een voorverwerkingsfout voor de taak ingesteld.  

Er kunnen vooraf verwerkings fouten optreden als de bron bestanden van de taak zijn verplaatst, het opslag account niet langer beschikbaar is of een ander probleem is aangetroffen waardoor bestanden niet naar het knoop punt kunnen worden gekopieerd.

### <a name="file-upload-errors"></a>Fouten bij uploaden van bestand

Als bestanden die voor een taak zijn opgegeven om een of andere reden niet worden geüpload, wordt een fout bij het uploaden van het bestand voor de taak ingesteld.

Fouten bij uploaden van bestand kunnen optreden als de SAS die is opgegeven om toegang te krijgen tot Azure Storage ongeldig is of geen schrijfmachtigingen biedt, als het opslagaccount niet meer beschikbaar is of als er een ander probleem is opgetreden waardoor bestanden niet van het knooppunt kunnen worden gekopieerd.

### <a name="application-errors"></a>Toepassingsfouten

Het proces dat door de opdrachtregel van de taak wordt opgegeven, kan ook mislukken. Het proces wordt als mislukt beschouwd wanneer het proces dat door de taak wordt uitgevoerd, een andere afsluitcode dan nul retourneert (zie *Taakafsluitcodes* in de volgende sectie).

Voor toepassings fouten kunt u batch configureren om de taak automatisch een bepaald aantal keren opnieuw uit te voeren.

### <a name="constraint-errors"></a>Beperkings fouten

U kunt de beperking *maxWallClockTime* instellen om te bepalen hoelang een job of taak maximaal kan worden uitgevoerd. Deze maximale kloktijd kan handig zijn om vastgelopen taken te beëindigen.

Wanneer de maximale hoeveelheid tijd wordt overschreden, wordt de taak als *voltooid* gemarkeerd, maar wordt de afsluitcode ingesteld op `0xC000013A` en wordt het veld *schedulingError* gemarkeerd als `{ category:"ServerError", code="TaskEnded"}`.

## <a name="task-exit-codes"></a>Taakafsluitcodes

Zoals eerder vermeld, wordt een taak door de Batch-service gemarkeerd als mislukt als het proces dat door de taak wordt uitgevoerd, een afsluitcode retourneert die niet nul is. Wanneer een taak een proces uitvoert, vult Batch de retourcode van het proces als afsluitcode-eigenschap van de taak in.

Houd voor ogen dat de afsluitcode van een taak niet wordt bepaald door de Batch-service. De afsluitcode van een taak wordt bepaald door het proces zelf of door het besturingssysteem waarop het proces wordt uitgevoerd.

## <a name="task-failures-or-interruptions"></a>Mislukte taken of onderbrekingen

Van tijd tot tijd kunnen taken mislukken of worden onderbroken. De taak toepassing zelf kan mislukken. het knoop punt waarop de taak wordt uitgevoerd, wordt mogelijk opnieuw gestart of het knoop punt wordt uit de groep verwijderd tijdens het wijzigen van de grootte (als het toewijzings beleid van de groep is ingesteld op knoop punten onmiddellijk verwijderen zonder te wachten tot de taken zijn voltooid). In alle gevallen moet de taak door Batch automatisch opnieuw in de wachtrij worden geplaatst voor uitvoering in een ander knooppunt.

Het is ook mogelijk dat er een regel matig probleem optreedt waardoor een taak niet meer reageert of te lang duurt om uit te voeren. U kunt de maximale uitvoeringsinterval voor een taak instellen. Als de maximale uitvoeringsinterval wordt overschreden, onderbreekt de Batch-service de taaktoepassing.

## <a name="connect-to-compute-nodes"></a>Verbinding maken met reken knooppunten

U kunt extra foutopsporing en probleemoplossing uitvoeren door u op afstand aan te melden bij een rekenknooppunt. U kunt via de Azure Portal een RDP-bestand (Remote Desktop Protocol) downloaden voor Windows-knooppunten en SSH-verbindingsinformatie (Secure Shell) verkrijgen voor Linux-knooppunten. U kunt dit ook doen met behulp van de batch-Api's, zoals met [batch .net](/dotnet/api/microsoft.azure.batch.computenode) of [batch python](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh).

> [!IMPORTANT]
> Als u via RDP of SSH verbinding wilt maken met een knooppunt, moet u in het knooppunt eerst een gebruiker maken. Hiervoor kunt u Azure Portal gebruiken, [een gebruikersaccount toevoegen aan een knooppunt](/rest/api/batchservice/computenode/adduser) met behulp van de Batch REST-API, de methode [ComputeNode.CreateComputeNodeUser](/dotnet/api/microsoft.azure.batch.computenode) aanroepen in Batch .NET of de methode [add_user](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh) aanroepen in de Batch Python-module.

Zie [Configure or disable remote access to compute nodes in an Azure Batch pool](pool-endpoint-configuration.md) (Externe toegang tot rekenknooppunten in een Azure Batch-pool configureren of uitschakelen) als u RDP- of SSH-toegang tot rekenknooppunten wilt beperken of uitschakelen.

## <a name="troubleshoot-problem-nodes"></a>Problemen met probleem knooppunten oplossen

In situaties waarin een aantal taken mislukken, kan uw Batch-clienttoepassing of -service de metagegevens van de mislukte taken onderzoeken om een knooppunt te identificeren dat zich niet normaal gedraagt. Elk knooppunt in een pool krijgt een unieke id en het knooppunt waarin een taak wordt uitgevoerd, is opgenomen in de metagegevens van de taak. Als u eenmaal een probleemknooppunt hebt geïdentificeerd, kunt u verschillende acties uitvoeren:

- **Het knoop punt opnieuw opstarten** ([rest](/rest/api/batchservice/computenode/reboot)  |  [.net](/dotnet/api/microsoft.azure.batch.computenode.reboot))

    Soms kunnen latente problemen zoals vastgelopen of gecrashte processen worden opgelost door het knooppunt opnieuw op te starten. Als uw pool een begin taak gebruikt of als uw taak een taak voorbereidings taak gebruikt, worden deze uitgevoerd wanneer het knoop punt opnieuw wordt opgestart.
- **De installatiekopie van het knooppunt terugzetten** ([REST](/rest/api/batchservice/computenode/reimage) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.reimage))

    Hiermee wordt het besturingssysteem opnieuw geïnstalleerd in het knooppunt. Net als bij het opnieuw opstarten van een knooppunt worden begintaken en jobvoorbereidingstaken opnieuw uitgevoerd nadat de installatiekopie van het knooppunt is teruggezet.
- **Het knooppunt uit de pool verwijderen** ([REST](/rest/api/batchservice/pool/removenodes) | [.NET](/dotnet/api/microsoft.azure.batch.pooloperations))

    Soms is het nodig om het knooppunt volledig uit de pool te verwijderen.
- **Het plannen van taken uitschakelen voor het knooppunt** ([REST](/rest/api/batchservice/computenode/disablescheduling) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.disablescheduling))

    Hierdoor wordt het knooppunt effectief offline geplaatst, zodat er geen taken meer aan worden toegewezen, maar het knooppunt wel actief en in de pool blijft. Zo kunt u verder onderzoek verrichten naar de oorzaak van de fouten zonder verlies van gegevens van de mislukte taak en zonder dat het knooppunt extra taakfouten veroorzaakt. U kunt bijvoorbeeld de taakplanning in het knooppunt uitschakelen en u vervolgens extern aanmelden om de gebeurtenislogboeken van het knooppunt te onderzoeken of een andere probleemoplossing uitvoeren. Nadat u klaar bent met het onderzoek, kunt u het knoop punt weer online plaatsen door de taak planning in te scha kelen ([rest](/rest/api/batchservice/computenode/enablescheduling)  |  [.net](/dotnet/api/microsoft.azure.batch.computenode.enablescheduling)of een van de andere acties uit te voeren die eerder zijn besproken.

> [!IMPORTANT]
> Met de acties die hierboven worden beschreven, kan youc opgeven hoe taken die momenteel op het knoop punt worden uitgevoerd, worden afgehandeld wanneer u de actie uitvoert. Wanneer u bijvoorbeeld de taakplanning uitschakelt op een knooppunt met de clientbibliotheek Batch .NET, kunt u een enum-waarde [DisableComputeNodeSchedulingOption](/dotnet/api/microsoft.azure.batch.common.disablecomputenodeschedulingoption) opgeven om aan te geven of u actieve taken wilt **beëindigen**, **opnieuw in de wachtrij wilt plaatsen** voor planning op andere knooppunten of wilt toestaan dat taken worden voltooid voordat de actie wordt uitgevoerd (**TaskCompletion**).

## <a name="retry-after-errors"></a>Opnieuw proberen na fouten

De batch-Api's melden u als er een fout is opgetreden. Ze kunnen allemaal opnieuw worden geprobeerd en ze bevatten allemaal een algemene nieuwe handler voor opnieuw proberen. Het is het beste om dit ingebouwde mechanisme te gebruiken.

Na een fout moet u even wachten (enkele seconden tussen nieuwe pogingen) voordat u het opnieuw probeert. Als u te vaak of te snel opnieuw probeert te proberen, wordt de handler voor opnieuw proberen beperkt.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [controleren op groeps-en knooppunt fouten](batch-pool-node-error-checking.md).
- Meer informatie over het [controleren op taak-en taak fouten](batch-job-task-error-checking.md).
- Bekijk de lijst met [batch-en fout codes](/rest/api/batchservice/batch-status-and-error-codes).
