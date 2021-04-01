---
title: Veelgestelde vragen over het maken van een back-up van Azure-bestanden
description: In dit artikel vindt u antwoorden op veelgestelde vragen over het beveiligen van uw Azure-bestands shares met de Azure Backup-service.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: e2b6afb25e189ee2848f25c0ba59d843baf37090
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91940832"
---
# <a name="questions-about-backing-up-azure-files"></a>Vragen over het maken back-ups van Azure-bestanden

In dit artikel vindt u antwoorden op veelgestelde vragen over het maken back-ups van Azure-bestanden. Sommige antwoorden bevatten koppelingen naar artikelen met uitgebreide informatie over het onderwerp. U kunt ook vragen over de Azure Backup-Service plaatsen op de [pagina micro soft Q&een vraag voor een discussie](/answers/topics/azure-backup.html).

Als u kort de secties in dit artikel wilt bekijken, gebruikt u de koppelingen aan de rechterkant, onder **In dit artikel**.

## <a name="configuring-the-backup-job-for-azure-files"></a>De back-uptaak configureren voor Azure-bestanden

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>Waarom zie ik een aantal van mijn opslag accounts die ik wil beveiligen, met geldige Azure-bestands shares?

Raadpleeg de [ondersteunings matrix voor Azure file shares backup](azure-file-share-support-matrix.md) om te controleren of het opslag account tot een van de ondersteunde typen opslag accounts behoort. Het is ook mogelijk dat het opslag account dat u zoekt al wordt beveiligd of is geregistreerd bij een andere kluis. [Hef de registratie van het opslag account](manage-afs-backup.md#unregister-a-storage-account) uit de kluis op om het opslag account in andere kluizen voor beveiliging te detecteren.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Waarom zie ik een aantal van mijn Azure-bestandsshares in het opslagaccount niet als ik mijn back-up configureer?

Controleer of de Azure-bestandsshare al in dezelfde Recovery Services-kluis wordt beveiligd en of de bestandsshare onlangs wellicht is verwijderd.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Kan ik bestandsshares die met een synchronisatiegroep in Azure File Sync zijn verbonden beveiligen?

Ja. De beveiliging van Azure-bestands shares die zijn verbonden met synchronisatie groepen is ingeschakeld.

### <a name="when-trying-to-back-up-file-shares-i-selected-a-storage-account-to-discover-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Bij het maken van een back-up van bestands shares heb ik een opslag account geselecteerd om de bestands shares erin te detecteren. Ik heb ze echter niet beveiligd. Hoe kan ik deze bestands shares beveiligen met een andere kluis?

Wanneer u een back-up wilt maken, selecteert u een opslag account om bestands shares te detecteren in het opslag account met de kluis van waaruit dit wordt gedaan. Als u de bestands shares met een andere kluis wilt beveiligen, moet u de [registratie](manage-afs-backup.md#unregister-a-storage-account) van het geselecteerde opslag account bij deze kluis opheffen.

### <a name="why-cant-i-change-the-vault-to-configure-backup-for-the-file-share"></a>Waarom kan ik de kluis niet wijzigen om een back-up voor de bestands share te configureren?

Als het opslag account al is geregistreerd bij een kluis of andere bestands shares in het opslag account worden beveiligd met behulp van een kluis, hebt u geen optie om het te wijzigen. Alle bestands shares in een opslag account kunnen alleen worden beveiligd door dezelfde kluis. Als u de kluis wilt wijzigen, moet u de [beveiliging stoppen voor alle bestands shares in het opslag account](manage-afs-backup.md#stop-protection-on-a-file-share) van de verbonden kluis, de registratie van het opslag account [ongedaan maken](manage-afs-backup.md#unregister-a-storage-account) en vervolgens een andere kluis voor beveiliging kiezen.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Kan ik de kluis wijzigen waarnaar ik een back-up van mijn bestands shares Maak?

Ja. U moet [de beveiliging van de bestands share](manage-afs-backup.md#stop-protection-on-a-file-share) van de verbonden kluis echter stoppen, de registratie van dit opslag account [ongedaan maken](manage-afs-backup.md#unregister-a-storage-account) en vervolgens beveiligen tegen een andere kluis.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Kan ik twee verschillende bestandsshares van hetzelfde opslagaccount in verschillende kluizen beveiligen?

Nee. Alle bestandsshares in een opslagaccount kunnen alleen in dezelfde kluis worden beveiligd.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>Wat moet ik doen als er een fout is opgetreden bij het starten van mijn back-ups omdat de maximum limiet is bereikt?

U kunt op elk moment maximaal 200 momentopnamen voor een bestandsshare hebben. Deze limiet is inclusief momentopnamen die zijn gemaakt met Azure Backup zoals gedefinieerd in uw beleid. Als uw back-ups mislukken nadat de limiet is bereikt, verwijdert u moment opnamen op aanvraag voor geslaagde toekomstige back-ups.

## <a name="restore"></a>Herstellen

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Kan ik gegevens herstellen vanuit een verwijderde Azure-bestandsshare?

Als de bestands share de modus voorlopig verwijderd heeft, moet u eerst de verwijdering van de bestands share ongedaan maken om de herstel bewerking uit te voeren. Met de bewerking voor het ongedaan maken van de verwijdering wordt de bestands share in de actieve staat gezet, waar u kunt herstellen naar elk gewenst moment. Ga naar [deze koppeling](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) of Zie het script voor het [verwijderen van bestands shares](./scripts/backup-powershell-script-undelete-file-share.md)voor meer informatie over het ongedaan maken van de verwijdering van de bestands share. Als de bestands share permanent wordt verwijderd, kunt u de inhoud en moment opnamen niet meer herstellen.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Kan ik gegevens herstellen vanuit back-ups als ik ben gestopt met de beveiliging van een Azure-bestandsshare?

Ja. Als u **Back-upgegevens behouden** hebt gekozen toen u met de beveiliging stopte, kunt u vanaf alle bestaande herstelpunten iets terugzetten.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Wat gebeurt er als ik een actieve herstel taak Annuleer?

Als een actieve herstel taak wordt geannuleerd, wordt het herstel proces gestopt en alle bestanden die vóór de annulering zijn teruggezet, blijven de geconfigureerde bestemming (oorspronkelijke of alternatieve locatie) zonder terugdraai bewerkingen.

## <a name="manage-backup"></a>Back-up beheren

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Kan ik PowerShell gebruiken voor het configureren/beheren/terugzetten van back-ups van Azure-bestandsshares?

Ja. Raadpleeg de [gedetailleerde documentatie voor meer informatie.](backup-azure-afs-automation.md)

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Kan ik toegang krijgen tot de moment opnamen die zijn gemaakt door Azure backups en deze koppelen?

Alle moment opnamen die door Azure Backup worden gemaakt, zijn toegankelijk via moment opnamen weer geven in de portal, Power shell of CLI. Zie [overzicht van moment opnamen van shares voor Azure files](../storage/files/storage-snapshots-files.md)voor meer informatie over Azure files moment opnamen van shares.

### <a name="what-happens-after-i-move-a-backed-up-file-share-to-a-different-subscription"></a>Wat gebeurt er nadat ik een back-up van een bestands share heb verplaatst naar een ander abonnement?

Zodra een bestands share is verplaatst naar een ander abonnement, wordt deze als een nieuwe bestands share beschouwd door Azure Backup. Dit zijn de aanbevolen stappen:
 
Scenario: Stel dat u een bestands share *FS1* hebt in het abonnement *S1* en dat deze is beveiligd met de *v1* -kluis. Nu wilt u de bestands share verplaatsen naar abonnement *S2*.
 
1.  Verplaats het gewenste opslag account en de juiste bestands share (FS1) naar het andere abonnement (S2).
2.  Activeer in de V1-kluis de bewerking beveiliging stoppen met gegevens verwijderen voor FS1.
3.  Hef de registratie van het opslag account dat als host fungeert voor FS1 op in de V1-kluis.
4.  Configureer de back-up voor FS1, nu verplaatst naar S2, met een kluis (v2) in het S2-abonnement. 
 
Houd er rekening mee dat na het opnieuw configureren van de back-up met v2 de moment opnamen die zijn gemaakt met v1, niet meer door Azure Backup worden beheerd. Daarom moet u deze moment opnamen hand matig verwijderen volgens uw vereisten.

### <a name="can-i-move-my-backed-up-file-share-to-a-different-resource-group"></a>Kan ik mijn back-up van de bestands share verplaatsen naar een andere resource groep?
 
Ja, u kunt de back-up van de bestands share verplaatsen naar een andere resource groep. U moet echter de back-up voor de bestands share opnieuw configureren, omdat deze wordt beschouwd als een nieuwe resource door Azure Backup. Ook de moment opnamen die zijn gemaakt voordat de resource groep wordt verplaatst, worden niet meer beheerd door Azure backup. Daarom moet u deze moment opnamen hand matig verwijderen volgens uw vereisten.

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Wat is de maximale Bewaar periode die ik voor back-ups kan configureren?

Raadpleeg de [ondersteunings matrix](azure-file-share-support-matrix.md) voor meer informatie over de maximale Bewaar periode. Azure Backup voert een realtime berekening uit van het aantal moment opnamen wanneer u de retentie waarden invoert tijdens het configureren van het back-upbeleid. Zodra het aantal moment opnamen dat overeenkomt met uw gedefinieerde Bewaar waarden 200 overschrijdt, wordt in de portal een waarschuwing weer gegeven waarin u wordt gevraagd uw Bewaar waarden aan te passen. Dat betekent dat u de limiet van het maximum aantal moment opnamen dat door Azure Files voor een bestands share op een wille keurig moment wordt ondersteund, niet overschrijdt.

### <a name="what-is-the-impact-on-existing-recovery-points-and-snapshots-when-i-modify-the-backup-policy-for-an-azure-file-share-to-switch-from-daily-policy-to-gfs-policy"></a>Wat is de impact van bestaande herstel punten en moment opnamen wanneer ik het back-upbeleid voor een Azure-bestands share Wijzig om over te scha kelen van ' dagelijks beleid ' naar ' GFS-beleid '?

Wanneer u een dagelijks back-upbeleid wijzigt in GFS-beleid (wekelijks/maandelijks/jaarlijks retentie toevoegen), is het gedrag als volgt:

- **Bewaren**: als u wekelijks/maandelijks/jaarlijks retentie toevoegt als onderdeel van het wijzigen van het beleid, worden alle toekomstige herstel punten die zijn gemaakt als onderdeel van de geplande back-up, gelabeld volgens het nieuwe beleid. Alle bestaande herstel punten worden nog steeds beschouwd als dagelijkse herstel punten en worden dus niet als wekelijks/maandelijks/jaarlijks gemarkeerd.

- **Opschoning van moment opnamen en herstel punten**:

  - Als dagelijks bewaren is verlengd, wordt de verval datum van de bestaande herstel punten bijgewerkt volgens de dagelijkse Bewaar waarde die is geconfigureerd in het nieuwe beleid.
  - Als dagelijks bewaren is beperkt, worden de bestaande herstel punten en moment opnamen in de volgende opschoon taak gemarkeerd voor verwijdering volgens de dagelijkse Bewaar waarde die is geconfigureerd in het nieuwe beleid en vervolgens verwijderd.

Hier volgt een voor beeld van hoe dit werkt:

#### <a name="existing-policy-p1"></a>Bestaand beleid [P1]

|Type Bewaar periode |Schema |Bewaartermijn  |
|---------|---------|---------|
|Dagelijks    |    Elke dag om 8 uur    |  100 dagen       |

#### <a name="new-policy-modified-p1"></a>Nieuw beleid [gewijzigde P1]

| Type Bewaar periode | Schema                       | Bewaartermijn |
| -------------- | ------------------------------ | --------- |
| Dagelijks          | Elke dag om 9 uur              | 50 dagen   |
| Wekelijks         | Op zondag om 9 uur              | 3 weken   |
| Maandelijks        | Op de afgelopen maandag om 9 uur         | 1 maand   |
| Jaar         | In Jan op de derde zondag om 9 uur | 4 jaar   |

#### <a name="impact"></a>Impact

1. De verval datum van bestaande herstel punten wordt aangepast op basis van de dagelijkse Bewaar waarde van het nieuwe beleid: dat wil zeggen 50 dagen. Een herstel punt dat ouder is dan 50 dagen, wordt dus gemarkeerd voor verwijdering.

2. De bestaande herstel punten worden niet als wekelijks/maandelijks/jaarlijks gelabeld op basis van het nieuwe beleid.

3. Alle toekomstige back-ups worden geactiveerd volgens het nieuwe schema: dat wil zeggen, op 9 PM.

4. De verval datum van alle toekomstige herstel punten wordt afgestemd op het nieuwe beleid.

>[!NOTE]
>De beleids wijzigingen zijn alleen van invloed op de herstel punten die zijn gemaakt als onderdeel van de geplande back-uptaak. Voor back-ups op aanvraag wordt de Bewaar periode bepaald door de waarde voor **behouden kassa** die is opgegeven op het moment dat de back-up wordt gemaakt.

### <a name="what-is-the-impact-on-existing-recovery-points-when-i-modify-an-existing-gfs-policy"></a>Wat is de invloed op bestaande herstel punten wanneer ik een bestaand GFS-beleid Wijzig?

Wanneer een nieuw beleid wordt toegepast op bestands shares, worden alle toekomstige geplande back-ups uitgevoerd volgens de planning die in het gewijzigde beleid is geconfigureerd.  De Bewaar periode van alle bestaande herstel punten wordt uitgelijnd op basis van de nieuwe Bewaar waarden die zijn geconfigureerd. Als de retentie is uitgebreid, worden de bestaande herstel punten gemarkeerd om te worden bewaard volgens het nieuwe beleid. Als de retentie wordt verminderd, worden deze gemarkeerd voor opschonen in de volgende opschoon taak en vervolgens verwijderd.

Hier volgt een voor beeld van hoe dit werkt:

#### <a name="existing-policy-p2"></a>Bestaand beleid [P2]

| Type Bewaar periode | Schema           | Bewaartermijn |
| -------------- | ------------------ | --------- |
| Dagelijks          | Elke dag om 8 uur | 50 dagen   |
| Wekelijks         | Op maandag om 8 uur  | 3 weken   |

#### <a name="new-policy-modified-p2"></a>Nieuw beleid [aangepaste P2]

| Type Bewaar periode | Schema               | Bewaartermijn |
| -------------- | ---------------------- | --------- |
| Dagelijks          | Elke dag om 9 uur     | 10 dagen   |
| Wekelijks         | Op maandag om 9 uur      | 2 weken   |
| Maandelijks        | Op de afgelopen maandag om 9 uur | 2 maanden  |

#### <a name="impact-of-change"></a>Gevolgen van wijziging

1. De verval datum van bestaande dagelijkse herstel punten wordt afgestemd op basis van de nieuwe dagelijkse Bewaar waarde, die 10 dagen is. Elk dagelijks herstel punt dat ouder is dan 10 dagen wordt verwijderd.

2. De verval datum van bestaande wekelijkse herstel punten wordt afgestemd op basis van de nieuwe wekelijkse Bewaar waarde, die twee weken is. Elk wekelijks herstel punt dat ouder is dan twee weken, wordt verwijderd.

3. De maandelijkse herstel punten worden alleen gemaakt als onderdeel van toekomstige back-ups op basis van de nieuwe beleids configuratie.

4. De verval datum van alle toekomstige herstel punten wordt afgestemd op het nieuwe beleid.

>[!NOTE]
>De beleids wijzigingen zijn alleen van invloed op de herstel punten die zijn gemaakt als onderdeel van de geplande back-up. Voor back-ups op aanvraag wordt de Bewaar periode bepaald **op het moment** dat de back-up wordt gemaakt.

## <a name="next-steps"></a>Volgende stappen

- [Problemen oplossen bij het maken van back-ups van Azure-bestands shares](troubleshoot-azure-files.md)
