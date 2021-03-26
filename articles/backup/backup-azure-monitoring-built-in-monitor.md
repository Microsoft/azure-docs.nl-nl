---
title: Azure Backup beveiligde werk belastingen bewaken
description: In dit artikel vindt u informatie over de bewakings-en meldings mogelijkheden voor Azure Backup werk belastingen met behulp van de Azure Portal.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 83ed5af00bb61d7a8929e710b52e60c33c0f479b
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559210"
---
# <a name="monitoring-azure-backup-workloads"></a>Bewaking Azure Backup werk belastingen

Azure Backup biedt meerdere back-upoplossingen op basis van de back-upvereisten en infrastructuur topologie (on-premises versus Azure). Elke back-upgebruiker of beheerder moet zien wat er gebeurt met alle oplossingen en kan worden geïnformeerd over belang rijke scenario's. In dit artikel vindt u meer informatie over de bewakings-en meldings mogelijkheden van Azure Backup service.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="backup-items-in-recovery-services-vault"></a>Back-upitems in Recovery Services kluis

U kunt al uw back-upitems bewaken via een Recovery Services kluis. Als u naar de sectie **Back-upitems** in de kluis navigeert, wordt een weer gave geopend met het aantal back-upitems van elk type werk belasting dat is gekoppeld aan de kluis. Als u op een rij klikt, wordt een gedetailleerde weer gave geopend met alle back-upitems van het opgegeven type werk belasting, met informatie over de laatste back-upstatus voor elk item, het laatste herstel punt dat beschikbaar is, enzovoort.

![RS-kluis back-upitems](media/backup-azure-monitoring-laworkspace/backup-items-view.png)

> [!NOTE]
> Voor items waarvan een back-up is gemaakt met behulp van DPM, worden in de lijst alle gegevens bronnen weer gegeven die zijn beveiligd (zowel schijf als online) via de DPM-server. Als de beveiliging is gestopt voor de gegevens bron met de back-upgegevens die worden bewaard, wordt de gegevens bron nog steeds weer gegeven in de portal. U kunt naar de details van de gegevens bron gaan om te zien of de herstel punten aanwezig zijn op de schijf, online of beide. Ook gegevens bronnen waarvoor de online beveiliging is gestopt, maar die wel worden bewaard, worden in rekening gebracht voor de online herstel punten totdat de gegevens volledig worden verwijderd.
>
> De DPM-versie moet DPM 1807 (5.1.378.0) of DPM 2019 (versie 10.19.58.0 of hoger) zijn, zodat de back-upitems zichtbaar zijn in de Recovery Services kluis Portal.

## <a name="backup-jobs-in-recovery-services-vault"></a>Back-uptaken in Recovery Services kluis

Azure Backup biedt ingebouwde bewakings-en waarschuwings functies voor werk belastingen die worden beveiligd door Azure Backup. In de Recovery Services kluis instellingen bevat de sectie **bewaking** ingebouwde taken en waarschuwingen.

![Ingebouwde RS-kluis bewaking](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Taken worden gegenereerd wanneer bewerkingen, zoals het configureren van back-ups, back-ups maken, herstellen, verwijderen van back-ups, enzovoort, worden uitgevoerd.

Taken van de volgende Azure Backup oplossingen worden hier weer gegeven:

- Azure VM Backup
- Back-up van Azure-bestand
- Azure-workload back-up, zoals SQL en SAP HANA
- Microsoft Azure Recovery Services-agent (MARS)

Taken van System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup-Server (MABS) worden niet weer gegeven.

> [!NOTE]
> Azure-werk belastingen, zoals SQL en SAP HANA back-ups in azure Vm's, hebben een groot aantal back-uptaken. Logboek back-ups kunnen bijvoorbeeld elke 15 minuten worden uitgevoerd. Voor dergelijke DB-workloads worden alleen door de gebruiker geactiveerde bewerkingen weer gegeven. Geplande back-upbewerkingen worden niet weer gegeven.

## <a name="backup-alerts-in-recovery-services-vault"></a>Back-upwaarschuwingen in Recovery Services kluis

> [!NOTE]
> Het weer geven van waarschuwingen over kluizen wordt momenteel niet ondersteund in Back-upcentrum. U moet naar een afzonderlijke kluis navigeren om waarschuwingen voor die kluis weer te geven.

Waarschuwingen zijn voornamelijk scenario's waarin gebruikers een melding ontvangen dat ze relevante actie kunnen ondernemen. In het gedeelte **back-upwaarschuwingen** worden waarschuwingen weer gegeven die zijn gegenereerd door Azure backup service. Deze waarschuwingen worden gedefinieerd door de service en de gebruiker kan geen aangepaste waarschuwingen maken.

### <a name="alert-scenarios"></a>Waarschuwings scenario's

De volgende scenario's worden door de service gedefinieerd als scenario's met waarschuwingen.

- Back-up-/herstelfouten
- Back-up geslaagd met waarschuwingen voor Microsoft Azure Recovery Services MARS-agent
- Beveiliging stoppen met het bewaren van gegevens of beveiliging tegen stoppen met gegevens verwijderen

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Waarschuwingen van de volgende Azure Backup oplossingen worden hier weer gegeven

- Back-ups van Azure-VM's
- Azure-bestandsback-ups
- Back-ups van Azure-workloads, zoals SQL, SAP HANA
- Microsoft Azure Recovery Services-agent (MARS)

> [!NOTE]
> Waarschuwingen van System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup-Server (MABS) worden hier niet weer gegeven.

### <a name="consolidated-alerts"></a>Geconsolideerde waarschuwingen

Voor back-upoplossingen van Azure, zoals SQL en SAP HANA, kunnen logboek back-ups zeer vaak worden gegenereerd (Maxi maal elke 15 minuten volgens het beleid). Het is dus ook mogelijk dat de back-upfouten van het logboek ook heel vaak worden (Maxi maal elke 15 minuten). In dit scenario wordt de eind gebruiker overbelast als er een waarschuwing wordt gegenereerd voor elk fout voorval. Er wordt dus een waarschuwing verzonden voor het eerste exemplaar en als de latere fouten worden veroorzaakt door dezelfde hoofd oorzaak, worden er geen verdere waarschuwingen gegenereerd. De eerste waarschuwing wordt bijgewerkt met het aantal fouten. Maar als de waarschuwing wordt gedeactiveerd door de gebruiker, wordt door de volgende keer dat er een waarschuwing wordt gegenereerd een andere melding weer en wordt dit beschouwd als de eerste waarschuwing voor die gebeurtenis. Zo wordt de consolidatie van waarschuwingen voor SQL-en SAP HANA back-ups door Azure Backup uitgevoerd.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Uitzonde ringen wanneer een waarschuwing niet wordt gegenereerd

Er zijn enkele uitzonde ringen wanneer een waarschuwing niet wordt gegenereerd bij een fout. Dit zijn:

- De gebruiker heeft de actieve taak expliciet geannuleerd
- De taak is mislukt omdat er een andere back-uptaak wordt uitgevoerd (er is niets om te handelen omdat we er gewoon op moeten wachten tot de vorige taak is voltooid)
- De back-uptaak van de VM is mislukt omdat de back-up van de virtuele machine van Azure niet meer bestaat
- [Geconsolideerde waarschuwingen](#consolidated-alerts)

De bovenstaande uitzonde ringen zijn ontworpen op basis van de uitleg dat het resultaat van deze bewerkingen (voornamelijk door de gebruiker geactiveerd) direct wordt weer gegeven op de portal-of PS/CLI-clients. De gebruiker is dus onmiddellijk op de hoogte en heeft geen melding nodig.

### <a name="alert-types"></a>Waarschuwingstypen

Waarschuwingen zijn gebaseerd op ernst van waarschuwingen en kunnen worden gedefinieerd in drie typen:

- **Kritiek**: in principe zou elke back-up-of herstel fout (gepland of door de gebruiker geactiveerd) leiden tot het genereren van een waarschuwing en worden weer gegeven als een kritieke waarschuwing en ook destructieve bewerkingen, zoals het verwijderen van back-ups.
- **Waarschuwing**: als de back-upbewerking is geslaagd, maar met weinig waarschuwingen, worden ze weer gegeven als waarschuwings meldingen. Waarschuwingen zijn momenteel alleen beschikbaar voor back-ups van Azure Backup Agent.
- **Ter informatie**: er wordt momenteel geen informatieve waarschuwing gegenereerd door Azure backup service.

## <a name="notification-for-backup-alerts"></a>Melding voor back-upwaarschuwingen

> [!NOTE]
> De configuratie van de melding kan alleen worden gedaan via de Azure Portal. Ondersteuning voor PS/CLI/REST API/Azure Resource Manager-sjabloon wordt niet ondersteund.

Zodra een waarschuwing is gegenereerd, worden gebruikers hiervan op de hoogte gebracht. Azure Backup biedt een ingebouwd meldings mechanisme via e-mail. Een kan afzonderlijke e-mail adressen of distributie lijsten opgeven die moeten worden gewaarschuwd wanneer er een waarschuwing wordt gegenereerd. U kunt ook kiezen of u wilt worden gewaarschuwd voor elke afzonderlijke waarschuwing of ze wilt groeperen in een samen vatting per uur en vervolgens een melding ontvangen.

![E-mail melding door RS-kluis inbouwen](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Wanneer de melding is geconfigureerd, ontvangt u een welkomst-of inkomend e-mail bericht. Hiermee wordt bevestigd dat Azure Backup e-mail berichten naar deze adressen kunt verzenden wanneer er een waarschuwing wordt gegenereerd.<br>

Als de frequentie is ingesteld op een samen vatting per uur en er binnen een uur een waarschuwing is gegenereerd en opgelost, maakt het geen deel uit van het komende uur overzicht.

> [!NOTE]
>
> - Als er een destructieve bewerking wordt uitgevoerd, zoals het **stoppen van de beveiliging bij het verwijderen van gegevens** , wordt er een waarschuwing gegenereerd en wordt er een e-mail bericht verzonden naar de eigen aren van het abonnement, beheerders en mede beheerders, zelfs als er geen meldingen zijn geconfigureerd voor de Recovery Services kluis.
> - Gebruik [log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)voor het configureren van een melding voor geslaagde taken.

## <a name="inactivating-alerts"></a>Waarschuwingen inactiveren

Als u een actieve waarschuwing wilt inactief maken/oplossen, kunt u het lijst item selecteren dat overeenkomt met de waarschuwing die u wilt deactiveren. Hiermee opent u een scherm waarin gedetailleerde informatie over de waarschuwing wordt weer gegeven, met een knop **actief** aan de bovenkant. Als u deze knop selecteert, wordt de status van de waarschuwing gewijzigd in **inactief**. U kunt ook een waarschuwing inactief maken door met de rechter muisknop te klikken op het item in de lijst dat overeenkomt met die waarschuwing en **inactief** te selecteren.

![Waarschuwing voor RS-kluis inactivering](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="azure-monitor-alerts-for-azure-backup-preview"></a>Azure Monitor waarschuwingen voor Azure Backup (preview-versie)

Azure Backup biedt ook waarschuwingen via Azure Monitor, zodat gebruikers een consistente ervaring kunnen hebben voor waarschuwings beheer in verschillende Azure-Services, inclusief back-ups. Met Azure Monitor waarschuwingen kunt u waarschuwingen routeren naar elk meldings kanaal dat wordt ondersteund door Azure Backup zoals e-mail, ITSM, webhook, logische app, enzovoort.

Deze functie is momenteel beschikbaar voor Azure-data bases voor PostgreSQL-server, Azure-blobs en Azure Managed Disks. Er worden waarschuwingen gegenereerd voor de volgende scenario's en kunnen worden geopend door naar een back-upkluis te gaan en te klikken op het menu-item **waarschuwingen** :

- Back-upgegevens verwijderen
- Back-upfout (als u waarschuwingen wilt ontvangen voor een back-upfout, moet u de AFEC-markering met de naam **EnableAzureBackupJobFailureAlertsToAzureMonitor** registreren via de preview-Portal)
- Fout bij het herstellen (als u waarschuwingen wilt ontvangen voor het herstellen is mislukt, moet u de AFEC-markering met de naam **EnableAzureBackupJobFailureAlertsToAzureMonitor** registreren via de preview-Portal)

Zie [overzicht van waarschuwingen in azure](../azure-monitor/alerts/alerts-overview.md)voor meer informatie over Azure monitor waarschuwingen.

## <a name="next-steps"></a>Volgende stappen

[Azure Backup workloads bewaken met behulp van Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)