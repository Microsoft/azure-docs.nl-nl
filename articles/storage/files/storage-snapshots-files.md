---
title: Overzicht van moment opnamen van shares voor Azure Files | Microsoft Docs
description: Een moment opname van een share is een alleen-lezen versie van een Azure Files share die op een bepaald moment wordt gemaakt, als een manier om een back-up van de share te maken.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d415ef165da18312a458d7d14fba18acd1bf44cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84235610"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Overzicht van share-momentopnamen voor Azure Files

Azure Files biedt de mogelijkheid om share-momentopnamen van bestandsshares te maken. Met share-momentopnamen kan de status van de share op dat moment worden vastgelegd. In dit artikel wordt beschreven welke mogelijkheden share-momentopnamen bieden en hoe u deze kunt gebruiken in uw aangepaste scenario.

## <a name="when-to-use-share-snapshots"></a>Wanneer moet u share-momentopnamen gebruiken?

### <a name="protection-against-application-error-and-data-corruption"></a>Bescherming tegen toepassingsfouten en gegevensbeschadiging

Toepassingen die gebruikmaken van bestandsshares, voeren bewerkingen uit als schrijven, lezen, opslag, verzending en verwerking. Als een toepassing niet goed wordt geconfigureerd of als er een onbedoelde fout wordt geïntroduceerd, kan er in enkele blokken een onbedoelde overschrijving of beschadiging optreden. Om u te helpen bij het beveiligen voor deze scenario's, kunt u een share-momentopname maken voordat u nieuwe toepassingscode implementeert. Als er een bug of toepassingsfout is geïntroduceerd tijdens de nieuwe implementatie, kunt u teruggaan naar een eerdere versie van uw gegevens in die bestandsshare. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Bescherming tegen onbedoeld verwijderingen of onbedoelde wijzigingen

Stel dat u werkt aan een tekstbestand in een bestandsshare. Wanneer het tekstbestand wordt gesloten, is het niet langer mogelijk uw wijzigingen ongedaan te maken. In deze gevallen moet u een vorige versie van het bestand herstellen. U kunt moment share-momentopnamen gebruiken om eerdere versies van het bestand te herstellen als de naam per ongeluk is gewijzigd of als het bestand is verwijderd.

### <a name="general-backup-purposes"></a>Algemene doeleinden voor back-up

Nadat u een bestandsshare hebt gemaakt, kunt u af en toe een share-momentopname van de bestandsshare maken en deze gebruiken voor back-up van gegevens. Als u regelmatig een share-momentopname maakt, kunt u de vorige versies van gegevens behouden. Deze kunnen worden gebruikt voor toekomstige auditvereisten of herstel na noodgeval. U kunt het beste [Azure file share backup](../../backup/azure-file-share-backup-overview.md) gebruiken als back-upoplossing voor het maken en beheren van moment opnamen. U kunt moment opnamen ook zelf maken en beheren met CLI of Power shell.

## <a name="capabilities"></a>Functies

Een moment opname van een share is een alleen-lezen kopie van uw gegevens. U kunt moment opnamen maken, verwijderen en beheren met behulp van de REST API. Dezelfde mogelijkheden zijn ook beschikbaar in de client bibliotheek, Azure CLI en Azure Portal. 

U kunt moment opnamen van een share weer geven door zowel de REST API als SMB te gebruiken. U kunt de lijst met versies van de map of het bestand ophalen en u kunt een specifieke versie rechtstreeks koppelen als een station (alleen beschikbaar voor Windows-Zie [limieten](#limits)). 

Nadat een moment opname van een share is gemaakt, kan deze worden gelezen, gekopieerd of verwijderd, maar niet worden gewijzigd. U kunt een volledige share-moment opname niet kopiëren naar een ander opslag account. U moet dat bestand doen op bestand met behulp van AzCopy of andere Kopieer mechanismen.

De functionaliteit van de moment opname van shares wordt op bestands share niveau gegeven. Ophalen wordt op afzonderlijke bestands niveau gegeven, zodat afzonderlijke bestanden kunnen worden teruggezet. U kunt een volledige bestands share herstellen met behulp van SMB, de REST API, de portal, de client bibliotheek of het Power shell/CLI-hulp programma.

Een moment opname van een share van een bestands share is identiek aan de basis bestands share. Het enige verschil is dat een **datum/tijd** -waarde wordt toegevoegd aan de share-URI om het tijdstip aan te geven waarop de moment opname van de share is gemaakt. Als een bestands share-URI bijvoorbeeld http: \/ /storagesample.core.file.Windows.net/MyShare is, is de URI van de moment opname van de share gelijk aan:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Moment opnamen delen die blijven bestaan totdat ze expliciet worden verwijderd. Een moment opname van de share kan de basis bestands share niet uitmaken. U kunt de moment opnamen die zijn gekoppeld aan de basis bestands share opsommen om uw huidige moment opnamen bij te houden. 

Wanneer u een moment opname van een share van een bestands share maakt, worden de bestanden in de systeem eigenschappen van de share gekopieerd naar de moment opname van de share met dezelfde waarden. De basis bestanden en de meta gegevens van de bestands share worden ook gekopieerd naar de moment opname van de share, tenzij u afzonderlijke meta gegevens voor de moment opname van de share opgeeft wanneer u deze maakt.

U kunt een share met share-moment opnamen delen alleen verwijderen als u eerst alle moment opnamen van shares verwijdert.

## <a name="space-usage"></a>Ruimte gebruik

Moment opnamen van shares zijn incrementeel. Alleen de gegevens die zijn gewijzigd na de moment opname van de meest recente share, worden opgeslagen. Dit minimaliseert de tijd die nodig is om de moment opname van de share te maken en bespaart opslag kosten. Elke schrijf bewerking naar het object of de update bewerking van de meta gegevens wordt geteld bij ' gewijzigde inhoud ' en wordt opgeslagen in de moment opname van de share. 

Als u ruimte wilt besparen, kunt u de moment opname van de share verwijderen voor de periode waarin het verloop het hoogste is.

Hoewel share-moment opnamen incrementeel worden opgeslagen, moet u alleen de meest recente moment opname van de share behouden om de share te herstellen. Wanneer u een moment opname van een share verwijdert, worden alleen de gegevens verwijderd die uniek zijn voor die share-moment opname. Actieve moment opnamen bevatten alle informatie die u nodig hebt om uw gegevens te zoeken en te herstellen (vanaf het moment dat de moment opname van de share is gemaakt) naar de oorspronkelijke locatie of een alternatieve locatie. U kunt herstellen op item niveau.

Moment opnamen tellen niet mee voor uw share limiet van 5 TB. Er is geen limiet voor het totale aantal moment opnamen van de ruimte share. De limieten van het opslag account zijn nog steeds van toepassing.

## <a name="limits"></a>Limieten

Het maximum aantal moment opnamen van shares dat Azure Files vandaag toestaat, is 200. Na 200 moment opnamen delen, moet u oudere moment opnamen van shares verwijderen om nieuwe te kunnen maken. 

Er is geen limiet voor de gelijktijdige aanroepen voor het maken van moment opnamen van shares. Er is geen limiet voor de hoeveelheid ruimte die moment opnamen van een bepaalde bestands share delen kan gebruiken. 

Momenteel is het niet mogelijk om moment opnamen van shares te koppelen aan linux. Dit komt doordat de Linux SMB-client geen ondersteuning biedt voor het koppelen van moment opnamen zoals Windows wel.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Gegevens terug kopiëren naar een share vanuit een share-momentopname

Voor kopieerbewerkingen die betrekking hebben op bestanden en share-momentopnamen, gelden deze regels:

U kunt afzonderlijke bestanden in een bestandsshare-momentopname kopiëren naar de bijbehorende basisshare of een andere locatie. U kunt een eerdere versie van een bestand herstellen of de volledige bestandsshare herstellen door bestand voor bestand vanaf de share-momentopname te kopiëren. De share-momentopname wordt niet gepromoveerd naar het niveau van de basisshare. 

De share-momentopname blijft na kopiëren intact, maar de basisbestandsshare wordt overschreven met een kopie van de gegevens die beschikbaar waren in de share-momentopname. Alle herstelde bestanden tellen mee met 'gewijzigde inhoud'.

U kunt een bestand in een share-momentopname kopiëren naar een ander doel met een andere naam. Het resulterende doelbestand is een schrijfbaar bestand en geen share-momentopname. In dit geval blijft uw basisbestandsshare intact.

Wanneer een doelbestand met een kopie wordt overschreven, blijven de share-momentopnamen die zijn gekoppeld aan het oorspronkelijke doelbestand, intact.

## <a name="general-best-practices"></a>Algemene best practices

U kunt het beste [Azure file share backup](../../backup/azure-file-share-backup-overview.md) gebruiken als back-upoplossing voor het automatiseren van moment opnamen, en het beheren van moment opnamen. Wanneer u een infra structuur uitvoert op Azure, moet u, indien mogelijk, back-ups automatiseren voor gegevens herstel. Geautomatiseerde acties zijn betrouwbaarder dan hand matige processen, waardoor gegevens bescherming en herstel baarheid worden verbeterd. U kunt de back-up van de Azure-bestands share, de REST API, de client-SDK of scripts voor automatisering gebruiken.

Voordat u de planner voor delen van snap shots implementeert, moet u de instellingen voor moment opnamen van delen en de Bewaar periode zorgvuldig overwegen om onnodige kosten te vermijden.

Moment opnamen delen bieden alleen beveiliging op bestands niveau. Moment opnamen delen verhinderen niet dat FAT-Finger wordt verwijderd op een bestands share of opslag account. Als u een opslag account wilt beveiligen tegen onbedoeld verwijderen, kunt u [zacht verwijderen inschakelen](storage-files-prevent-file-share-deletion.md)of het opslag account en/of de resource groep vergren delen.

## <a name="next-steps"></a>Volgende stappen
- Werken met moment opnamen van shares in:
    - [Back-up van Azure-bestands share](../../backup/azure-file-share-backup-overview.md)
    - [PowerShell](storage-how-to-use-files-powershell.md)
    - [CLI](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Veelgestelde vragen over delen van moment opnamen](storage-files-faq.md#share-snapshots)