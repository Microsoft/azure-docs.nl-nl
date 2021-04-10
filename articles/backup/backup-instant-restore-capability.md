---
title: Azure direct Restore-mogelijkheid
description: Azure direct Restore-mogelijkheid en veelgestelde vragen over VM-back-upstack, implementatie model van Resource Manager
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 3448b162c17dec2ab5b7637a3527d1c470bd415c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102618573"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Profiteer van verbeterde prestaties voor back-up en herstel met Azure Backup functie voor direct terugzetten

> [!NOTE]
> Op basis van feedback van gebruikers hebben we de naam van de **VM-back-upstack v2** gewijzigd in **direct terugzetten** om Verwar ring met Azure stack functionaliteit te verminderen.
> Alle Azure Backup gebruikers zijn nu bijgewerkt naar **direct herstellen**.

Het nieuwe model voor direct terugzetten biedt de volgende functie verbeteringen:

* De mogelijkheid om moment opnamen te gebruiken die zijn gemaakt als onderdeel van een back-uptaak die beschikbaar is voor herstel, zonder te wachten tot de gegevens overdracht naar de kluis is voltooid. Het verkleint de wacht tijd voor moment opnamen die moeten worden gekopieerd naar de kluis voordat de herstel bewerking wordt geactiveerd.
* Hiermee vermindert u de back-up-en herstel tijden door moment opnamen lokaal te bewaren, voor twee dagen standaard. Deze standaard waarde voor het bewaren van moment opnamen kan worden geconfigureerd voor elke waarde tussen 1 en 5 dagen.
* Ondersteunt schijf grootten tot 32 TB. Het wijzigen van de grootte van schijven wordt niet aanbevolen door Azure Backup.
* Ondersteunt Standard-SSD schijven samen met Standard-HDD schijven en Premium-SSD schijven.
* De mogelijkheid om een niet-beheerde virtuele machine te gebruiken oorspronkelijke opslag accounts (per schijf) bij het herstellen. Deze mogelijkheid bestaat zelfs wanneer de virtuele machine schijven heeft die over verschillende opslag accounts worden gedistribueerd. Het versnelt de herstel bewerkingen voor een groot aantal verschillende VM-configuraties.
* Voor het maken van back-ups van virtuele machines die gebruikmaken van niet-beheerde Premium-schijven in opslag accounts, kunt u met direct terugzetten *50%* beschik bare ruimte toewijzen aan de totale toegewezen opslag ruimte. Dit is **alleen** vereist voor de eerste back-up. De 50% beschik bare ruimte is geen vereiste voor back-ups nadat de eerste back-up is voltooid.

## <a name="whats-new-in-this-feature"></a>Wat is er nieuw in deze functie

Op dit moment bestaat de back-uptaak uit twee fasen:

1. Het maken van een VM-schaduwkopie.
2. Het overdragen van een VM-schaduwkopie naar de Azure Recovery Services-kluis.

Een herstelpunt wordt alleen als gemaakt beschouwd nadat fase 1 en 2 zijn voltooid. Als onderdeel van deze upgrade wordt een herstelpunt gemaakt zodra de schaduwkopie is voltooid. Dit herstelpunt van het type schaduwkopie kan worden gebruikt om een herstel uit te voeren met dezelfde herstelstroom. U kunt dit herstel punt in de Azure Portal identificeren door ' moment opname ' te gebruiken als het herstel punt type en nadat de moment opname is overgebracht naar de kluis, wordt het herstel punt type gewijzigd in moment opname en kluis.

![Back-uptaak in VM-back-upstack Resource Manager-implementatie model: opslag en kluis](./media/backup-azure-vms/instant-rp-flow.png)

Moment opnamen worden standaard twee dagen bewaard. Met deze functie kan de herstel bewerking vanuit deze moment opnamen worden uitgevoerd. Het vermindert de tijd die nodig is om gegevens terug te zetten en te kopiëren van de kluis.

## <a name="feature-considerations"></a>Functie overwegingen

* Moment opnamen worden samen met de schijven opgeslagen om het maken van herstel punten te verhogen en herstel bewerkingen sneller uit te voeren. Als gevolg hiervan worden opslag kosten weer gegeven die overeenkomen met moment opnamen die zijn gemaakt tijdens deze periode.
* Incrementele moment opnamen worden opgeslagen als pagina-blobs. Alle gebruikers die gebruikmaken van niet-beheerde schijven, worden in rekening gebracht voor de moment opnamen die zijn opgeslagen in hun lokale opslag account. Omdat de herstel punt verzamelingen die worden gebruikt door beheerde VM-back-ups BLOB-moment opnamen op het onderliggende opslag niveau gebruiken, voor beheerde schijven, worden de kosten weer geven die overeenkomen met de prijzen van BLOB-moment opnamen en ze incrementeel.
* Voor Premium Storage-accounts worden de moment opnamen die zijn gemaakt voor onmiddellijke herstel punten, meegeteld bij de limiet van 10 TB aan toegewezen ruimte.
* U krijgt de mogelijkheid om de Bewaar periode voor moment opnamen te configureren op basis van de herstel behoeften. Afhankelijk van de vereiste kunt u de retentie van de moment opname instellen op mini maal één dag in het deel venster back-upbeleid, zoals hieronder wordt uitgelegd. Dit helpt u kosten voor het bewaren van moment opnamen op te slaan als u de herstel bewerking niet regel matig uitvoert.
* Het is een upgrade met één richting. Nadat de upgrade is uitgevoerd naar direct herstellen, kunt u niet meer teruggaan.

>[!NOTE]
>Met deze onmiddellijke terugzet upgrade wordt de duur van de moment opname van alle klanten (**Nieuw en bestaand al inbegrepen**) ingesteld op een standaard waarde van twee dagen. U kunt de duur echter instellen op basis van uw vereiste tot een waarde tussen 1 en 5 dagen.

## <a name="cost-impact"></a>Impact op kosten

De incrementele moment opnamen worden opgeslagen in het opslag account van de virtuele machine, die wordt gebruikt voor instant Recovery. Incrementele moment opname is de ruimte die wordt ingen Omen door een moment opname, is gelijk aan de ruimte die wordt ingen Omen door pagina's die zijn geschreven nadat de moment opname is gemaakt. De facturering is nog steeds beschikbaar voor de gebruikte ruimte per GB die wordt ingen Omen door de moment opname, en de prijs per GB is hetzelfde als vermeld op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/managed-disks/). Voor virtuele machines die gebruikmaken van niet-beheerde schijven, kunnen de moment opnamen worden weer gegeven in het menu voor het VHD-bestand van elke schijf. Voor beheerde schijven worden moment opnamen opgeslagen in een resource voor het verzamelen van herstel punten in een aangewezen resource groep en de moment opnamen zelf zijn niet direct zichtbaar.

>[!NOTE]
> Het bewaren van moment opnamen is vastgesteld op 5 dagen voor wekelijks beleid.

## <a name="configure-snapshot-retention"></a>De retentie van de moment opname configureren

### <a name="using-azure-portal"></a>Azure Portal gebruiken

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

In de Azure Portal ziet u een veld dat is toegevoegd in het deel venster **VM-back-upbeleid** in de sectie **direct terugzetten** . U kunt de Bewaar duur van de moment opname wijzigen via het deel venster **back-upbeleid** voor vm's voor alle virtuele machines die aan het specifieke back-upbeleid zijn gekoppeld.

![Mogelijkheid tot direct terugzetten](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>PowerShell gebruiken

>[!NOTE]
> Vanuit AZ Power shell version 1.6.0 kunt u de retentie periode voor het terugzetten van moment opnamen in het beleid bijwerken met behulp van Power shell

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

De standaard retentie van de moment opname voor elk beleid is ingesteld op twee dagen. U kunt de waarde wijzigen in Mini maal 1 en Maxi maal vijf dagen. Voor wekelijks beleid is de Bewaar termijn van de moment opname ingesteld op vijf dagen.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Wat zijn de kosten voor direct herstel?

Moment opnamen worden samen met de schijven opgeslagen om bewerkingen voor het maken en terugzetten van herstel punten te versnellen. Als gevolg hiervan worden opslag kosten weer geven die overeenkomen met de retentie van de moment opname die is geselecteerd als onderdeel van het back-upbeleid van de VM.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>In Premium Storage accounts nemen de moment opnamen die zijn gemaakt voor het directe herstel punt de limiet van 10 TB aan?

Ja, voor Premium Storage-accounts de moment opnamen die zijn gemaakt voor direct herstel punt nemen 10 TB aan toegewezen momentopname ruimte.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Hoe werkt de retentie van de moment opname tijdens de periode van vijf dagen?

Elke dag wordt een nieuwe moment opname gemaakt, waarna er vijf afzonderlijke incrementele moment opnamen zijn. De grootte van de moment opname is afhankelijk van het gegevens verloop, die in de meeste gevallen ongeveer 2%-7% zijn.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Is een moment opname van direct terugzetten een incrementele moment opname of volledige moment opname?

Moment opnamen die als onderdeel van de functie voor direct terugzetten worden gemaakt, zijn incrementele moment opnamen.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Hoe kan ik de kosten toename benaderen als gevolg van de functie voor direct terugzetten?

Dit is afhankelijk van het verloop van de virtuele machine. Bij een stabiele status kunt u aannemen dat de toename van de kosten = moment opname van de retentie periode per opslag kosten per GB voor de VM.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Als het herstel type voor een herstel punt ' moment opname en kluis ' is en er een herstel bewerking wordt uitgevoerd, welk herstel type wordt gebruikt?

Als het herstel type ' moment opname en kluis ' is, wordt de herstel bewerking automatisch uitgevoerd vanaf de lokale moment opname, wat veel sneller is vergeleken met de herstel bewerking die vanuit de kluis wordt uitgevoerd.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Wat gebeurt er als ik de Bewaar periode van het herstel punt (laag 2) kleiner dan de retentie periode van de moment opname (Tier1)?

Het nieuwe model staat het verwijderen van het herstel punt (Tier2) niet toe, tenzij de moment opname (Tier1) is verwijderd. Het is raadzaam om de Bewaar periode voor herstel punten (Tier2) te plannen die groter is dan de Bewaar periode voor de moment opname.

### <a name="why-does-my-snapshot-still-exist-even-after-the-set-retention-period-in-backup-policy"></a>Waarom bestaat mijn moment opname nog, zelfs na het instellen van de Bewaar periode in back-upbeleid?

Als het herstel punt een moment opname heeft en het meest recente herstel punt beschikbaar is, wordt het behouden tot de volgende geslaagde back-up. Dit is afhankelijk van het ingestelde beleid voor garbage collection (GC). Het is verplicht dat er altijd ten minste één laatste herstel punt aanwezig is, in het geval dat alle volgende back-ups mislukken vanwege een probleem in de virtuele machine. In normale scenario's worden herstel punten Maxi maal 24 uur na verloop van tijd opgeruimd. In zeldzame scenario's kunnen zich een of twee extra moment opnamen bevinden op basis van de zwaarste belasting op de garbage collector (GC).

### <a name="why-do-i-see-more-snapshots-than-my-retention-policy"></a>Waarom zie ik meer moment opnamen dan mijn Bewaar beleid?

In een scenario waarin een Bewaar beleid is ingesteld op ' 1 ', vindt u twee moment opnamen. Dit wil zeggen dat er altijd ten minste één laatste herstel punt aanwezig is, in het geval dat alle volgende back-ups mislukken vanwege een probleem in de virtuele machine. Dit kan de aanwezigheid van twee moment opnamen veroorzaken.<br></br>Als het beleid bijvoorbeeld is voor moment opnamen van ' n ', kunt u de moment opnamen ' n + 1 ' op momenten vinden. U kunt zelfs ' n + 1 + 2 ' moment opnamen vinden als er een vertraging optreedt in garbagecollection. Dit kan in zeldzame gevallen optreden wanneer:
- U kunt moment opnamen opschonen, die eerder zijn bewaard.
- De garbage collector (GC) in de back-end is zware belasting.

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>Ik heb niet de functionaliteit voor direct terugzetten nodig. Kan deze worden uitgeschakeld?

De functie voor direct terugzetten is ingeschakeld voor iedereen en kan niet worden uitgeschakeld. U kunt de retentie van de moment opname beperken tot een minimum van één dag.

### <a name="is-it-safe-to-restart-the-vm-during-the-transfer-process-which-can-take-many-hours-will-restarting-the-vm-interrupt-or-slow-down-the-transfer"></a>Is het veilig om de virtuele machine opnieuw op te starten tijdens het overdrachts proces (dit kan veel uren duren)? Wordt de VM-interrupt opnieuw gestart of wordt de overdracht vertraagd?

Ja, het is veilig en er is geen invloed op de snelheid van de gegevens overdracht.

