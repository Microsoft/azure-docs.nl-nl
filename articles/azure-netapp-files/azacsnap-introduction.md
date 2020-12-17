---
title: Wat is Azure-toepassing consistent momentopname hulpmiddel voor Azure NetApp Files | Microsoft Docs
description: Biedt een inleiding tot het Azure-toepassing consistente momentopname hulpmiddel dat u kunt gebruiken met Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: b168167ce4f44d87c396746cca3f271f95f83163
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632683"
---
# <a name="what-is-azure-application-consistent-snapshot-tool-preview"></a>Wat is Azure-toepassing consistent momentopname programma (preview-versie)

Azure-toepassing consistent snap shot tool (AzAcSnap) is een opdracht regel programma waarmee u de gegevens beveiliging voor data bases van derden (SAP HANA) in Linux-omgevingen (bijvoorbeeld SUSE en RHEL) kunt vereenvoudigen.  

## <a name="benefits-of-using-azacsnap"></a>Voor delen van het gebruik van AzAcSnap

AzAcSnap maakt gebruik van de moment opname van de volume-en replicatie functionaliteit in Azure NetApp Files en een grote Azure-instantie.  Het biedt de volgende voor delen:

- **Toepassings consistente gegevens beveiliging** AzAcSnap is een gecentraliseerde oplossing voor het maken van back-ups van essentiële database bestanden. Het zorgt voor consistentie van de Data Base voordat u een moment opname van een opslag volume uitvoert. Als gevolg hiervan zorgt u ervoor dat de moment opname van het opslag volume kan worden gebruikt voor het herstellen van de data base.
- **Database catalogus beheer** Wanneer u AzAcSnap gebruikt met een Data Base met een ingebouwde back-upcatalogus, worden de records in de catalogus actueel gehouden met opslag momentopnamen.  Met deze mogelijkheid kan een database beheerder de back-upactiviteit bekijken.
- **Ad-hoc volume beveiliging** Deze mogelijkheid is handig voor niet-database volumes waarvoor stil leggen van toepassingen niet nodig is voordat een opslag momentopname wordt gemaakt.  Voor beelden zijn SAP HANA logboek back-upvolumes of SAPTRANS-volumes.
- **Klonen van opslag volumes** Deze mogelijkheid biedt ruimte-efficiënte opslag volume klonen voor ontwikkelings-en test doeleinden.
- **Ondersteuning voor herstel na nood gevallen** AzAcSnap maakt gebruik van opslag volume replicatie om opties te bieden voor het herstellen van gerepliceerde toepassings consistente moment opnamen op een externe site.

AzAcSnap is één binair element.  Er zijn geen extra agents of invoeg toepassingen nodig om te communiceren met de data base of de opslag (Azure NetApp Files via Azure Resource Manager en Azure large instance via SSH).  AzAcSnap moet worden geïnstalleerd op een systeem dat verbinding heeft met de data base en de opslag.  Met de flexibiliteit van installatie en configuratie kan echter één gecentraliseerde installatie of een volledig gedistribueerde installatie worden uitgevoerd, waarbij kopieën zijn geïnstalleerd op elke database installatie.

## <a name="architecture-overview"></a>Overzicht van de architectuur

AzAcSnap kan worden geïnstalleerd op dezelfde host als de data base (SAP HANA) of kan worden geïnstalleerd op een gecentraliseerd systeem.  Maar er moet een netwerk verbinding zijn met de database servers en de back-end van de opslag (Azure Resource Manager voor Azure NetApp Files of SSH voor een grote Azure-instantie).

AzAcSnap is een licht gewicht toepassing die meestal wordt uitgevoerd vanuit een externe planner.  Op de meeste Linux-systemen is deze bewerking `cron` de nadruk op de documentatie.  De scheduler kan echter ook een alternatief hulp programma zijn, zolang het `azacsnap` Shell Profiel van de gebruiker kan worden geïmporteerd.  Het importeren van de omgevings instellingen van de gebruiker zorgt ervoor dat bestands paden en machtigingen correct worden geïnitialiseerd.

## <a name="command-synopsis"></a>Opdracht-samen vatting

De algemene indeling van de opdrachten is als volgt: `azacsnap -c [command] --[command] [sub-command] --[flag-name] [flag-value]` .

## <a name="command-options"></a>Opdracht Opties

De opdracht opties zijn als volgt met de opdrachten als de belangrijkste opsommings tekens en de bijbehorende subopdrachten als Inge sprongen opsommings tekens:

- **`-h`** biedt uitgebreide informatie over de opdracht regel met voor beelden van het gebruik van AzAcSnap.
- **`-c configure`** Met deze opdracht geeft u een interactieve Q&een stijl interface voor het maken of wijzigen van het `azacsnap` configuratie bestand (standaard = `azacsnap.json` ).
  - **`--configuration new`** Er wordt een nieuw configuratie bestand gemaakt.
  - **`--configuration edit`** een bestaand configuratie bestand wordt bewerkt.
  - Raadpleeg [Configure Command Reference](azacsnap-cmd-ref-configure.md).
- **`-c test`** valideert het configuratie bestand en test de verbinding.
  - **`--test hana`**  Test de verbinding met het SAP HANA-exemplaar.
  - **`--test storage`** Test de communicatie met de onderliggende opslag interface door een tijdelijke opslag momentopname te maken op alle geconfigureerde `data` volumes en deze vervolgens te verwijderen.
  - **`--test all`** zowel de als- `hana` tests worden uitgevoerd `storage` .
  - Raadpleeg de [Naslag informatie voor testen](azacsnap-cmd-ref-test.md).
- **`-c backup`** is de primaire opdracht voor het uitvoeren van database consistente opslag momentopnamen voor gegevens (SAP HANA gegevens volumes) & andere (bijvoorbeeld gedeelde, logboek back-ups of opstart) volumes.
  - **`--volume data`** Als u een moment opname wilt maken van alle volumes in de `dataVolume` stanza van het configuratie bestand.
  - **`--volume other`** Als u een moment opname wilt maken van alle volumes in de `otherVolume` stanza van het configuratie bestand.
  - Raadpleeg de [Naslag informatie voor back-upopdrachten](azacsnap-cmd-ref-backup.md).
- **`-c details`** bevat informatie over moment opnamen of replicatie.
  - **`--details snapshots`** Hier vindt u een lijst met basis gegevens over de moment opnamen voor elk volume dat is geconfigureerd.
  - **`--details replication`** Biedt basis Details over de replicatie status van de productie site naar de site voor nood herstel.
  - Raadpleeg de [Naslag informatie over de opdracht](azacsnap-cmd-ref-details.md).
- **`-c delete`** Met deze opdracht wordt een opslag momentopname of een set met moment opnamen verwijderd. U kunt de SAP HANA back-upid gebruiken zoals gevonden in HANA Studio of de naam van de opslag momentopname. De back-upid is alleen gekoppeld aan de `hana` moment opnamen die zijn gemaakt voor de gegevens en gedeelde volumes. Als de naam van de moment opname niet is ingevoerd, wordt gezocht naar alle moment opnamen die overeenkomen met de opgegeven naam van de moment opname.
  - Zie de [verwijderen](azacsnap-cmd-ref-delete.md).
- **`-c restore`** biedt twee methoden voor het herstellen van een moment opname naar een volume door een nieuw volume te maken op basis van de moment opname of een volume terug te draaien naar een preview-status.
  - **`--restore snaptovol`** Hiermee wordt een nieuw volume gemaakt op basis van de laatste moment opname op het doel volume.
  - **`-c restore --restore revertvolume`** Hiermee wordt het doel volume teruggezet naar een eerdere status op basis van de meest recente moment opname.
  - Raadpleeg de [Naslag informatie over het herstellen](azacsnap-cmd-ref-restore.md)van de opdracht.
- **`[--configfile <configfilename>]`** De optionele opdracht regel parameter voor het opgeven van een andere bestands naam voor de JSON-configuratie.  Dit is met name handig voor het maken van een afzonderlijk configuratie bestand per SID (bijvoorbeeld `--configfile H80.json` ).

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met Azure-toepassing consistent momentopname programma](azacsnap-get-started.md)
