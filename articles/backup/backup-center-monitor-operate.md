---
title: Back-ups bewaken en gebruiken in het Backup-centrum
description: In dit artikel wordt uitgelegd hoe u back-ups op schaal kunt bewaken en gebruiken met behulp van Back-upcentrum
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 86b81110d6abeb1425e18ee45dfe65a96f69687d
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506119"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>Back-ups bewaken en gebruiken met Back-upcentrum

Als back-upbeheerder kunt u het Back-upcentrum gebruiken als een enkel glas venster om uw taken en de inventaris van de back-up dagelijks te bewaken. U kunt Back-upcentrum ook gebruiken om uw normale bewerkingen uit te voeren, zoals het reageren op aanvragen voor back-ups op aanvraag, het herstellen van back-ups, het maken van back-upbeleid, enzovoort.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

* Backup Center wordt momenteel ondersteund voor Azure VM backup, SQL in azure VM backup, SAP HANA in azure VM backup, Azure Files backup, Azure blobs backup, Azure Managed Disks backup en Azure Database for PostgreSQL Server Backup.
* Raadpleeg de [ondersteunings matrix](backup-center-support-matrix.md) voor een gedetailleerde lijst met ondersteunde en niet-ondersteunde scenario's.

## <a name="backup-instances"></a>Back-upinstanties

Back-upcentrum biedt u de mogelijkheid om back-upinstanties in back-ups te zoeken en te detecteren.

Als u het tabblad **back-Upinstanties** in Back-upcentrum selecteert, kunt u de details van alle back-upinstanties bekijken waartoe u toegang hebt.

 U kunt de volgende informatie over elk van uw back-upinstanties bekijken:

* Data Source-abonnement
* Resource groep gegevens bron
* Laatste herstel punt
* De kluis die is gekoppeld aan het back-upexemplaar
* Beveiligingsstatus

 U kunt ook de lijst met back-upinstanties filteren op de volgende para meters:

* Data Source-abonnement
* Resource groep gegevens bron
* Locatie van gegevens bron
* Gegevens bron type
* Kluis
* Beveiligingsstatus
* Data Source-Tags

Met de rechter muisknop op een van de items in het raster kunt u acties uitvoeren op het gegeven back-upexemplaar, zoals het navigeren naar de resource, het activeren van back-ups op aanvraag en het herstellen of het stoppen van de back-up.

![Back-upcentrum-instanties](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>Back-uptaken

In Back-upcentrum kunt u gedetailleerde informatie weer geven over alle taken die zijn gemaakt in uw back-up en de juiste actie ondernemen voor het mislukken van taken.

Als u het menu **-item back-uptaken** in Back-upcentrum selecteert, ziet u een overzicht van al uw taken. Elke taak bevat de volgende informatie:

* Het back-upexemplaar dat aan de taak is gekoppeld
* Data Source-abonnement
* Resource groep gegevens bron
* Locatie van gegevens bron
* Taak bewerking
* Taak status
* Begintijd van de taak
* Taak duur

Als u een item in het raster selecteert, kunt u meer informatie over de opgegeven taak bekijken. Als u met de rechter muisknop op een item klikt, kunt u naar de resource navigeren om de benodigde actie uit te voeren.

![Back-upcentrum-taken](./media/backup-center-monitor-operate/backup-center-jobs.png)

Met behulp van het tabblad **back-uptaken** kunt u taken weer geven tot de afgelopen zeven dagen. Gebruik [back-uprapporten](backup-center-obtain-insights.md)om oudere taken weer te geven.

## <a name="vaults"></a>Kluizen

Als u het menu-item **kluizen** selecteert in Back-upcentrum, kunt u een lijst weer geven met alle [Recovery Services kluizen](backup-azure-recovery-services-vault-overview.md) en [back-upkluizen](backup-vault-overview.md) waartoe u toegang hebt. U kunt de lijst filteren met de volgende para meters:

* Kluis abonnement
* Kluis resource groep
* Kluisnaam
* Gegevens bron type dat is gekoppeld aan het beleid

Als u een item in de lijst selecteert, kunt u naar een bepaalde kluis navigeren.

![Back-upcentrum-kluizen](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>Back-upbeleid

In Back-upcentrum kunt u belang rijke informatie voor elk van uw back-upbeleid weer geven en bewerken.

Als u het menu **-item back-upbeleid** selecteert, kunt u alle beleids regels weer geven die u hebt gemaakt in back-ups. U kunt de lijst filteren op kluis abonnement, resource groep, gegevens bron type en kluis. Als u met de rechter muisknop op een item in het raster klikt, kunt u de bijbehorende items voor dat beleid weer geven, het beleid bewerken of zelfs verwijderen als dat nodig is.

![Back-upcentrum-beleids regels](./media/backup-center-monitor-operate/backup-center-policies.png)

## <a name="next-steps"></a>Volgende stappen

* [Uw back-up maken](backup-center-govern-environment.md)
* [Acties uitvoeren met behulp van Back-upcentrum](backup-center-actions.md)
* [Inzichten op uw back-ups verkrijgen](backup-center-obtain-insights.md)
