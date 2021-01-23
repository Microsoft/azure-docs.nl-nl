---
title: Back-ups maken en herstellen Active Directory
description: Meer informatie over het maken van back-ups en het herstellen van Active Directory domein controllers.
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8db2dab605e90e4748b11a632d6651c23d631b6c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733550"
---
# <a name="back-up-and-restore-active-directory-domain-controllers"></a>Back-ups maken en herstellen Active Directory domein controllers

Als u een back-up maakt van Active Directory en u geslaagde herstel bewerkingen in geval van beschadiging wilt garanderen, is inbreuk of ramp een belang rijk onderdeel van Active Directory onderhoud.

In dit artikel vindt u een overzicht van de juiste procedures voor het maken van back-ups en het herstellen van Active Directory domein controllers met Azure Backup, of het nu gaat om virtuele machines van Azure of op on-premises servers. Er wordt een scenario beschreven waarin u een volledige domein controller op het moment van de back-up naar de status ervan moet herstellen. Raadpleeg [dit artikel](/windows-server/identity/ad-ds/manage/ad-forest-recovery-determine-how-to-recover)als u wilt weten welk herstel scenario het meest geschikt is voor u.  

>[!NOTE]
> In dit artikel wordt niet beschreven hoe u items terugzet van [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Zie [dit artikel](../active-directory/fundamentals/active-directory-users-restore.md)voor meer informatie over het herstellen van Azure Active Directory-gebruikers.

## <a name="best-practices"></a>Aanbevolen procedures

- Zorg ervoor dat er ten minste één domein controller met een back-up wordt gemaakt. Als u een back-up maakt van meer dan één domein controller, moet u ervoor zorgen dat er een back-up wordt gemaakt van alle taken die de [FSMO (Flexible Single Master Operation)](/windows-server/identity/ad-ds/plan/planning-operations-master-role-placement) hebben.

- Maak regel matig een back-up van Active Directory. De back-up mag nooit meer zijn dan de tombstone-levens duur (standaard 60 dagen), omdat objecten die ouder zijn dan de tombstone-levens duur ' tombstoned ' zijn en niet langer als geldig worden beschouwd.

- Een leeg herstel plan voor nood gevallen hebben dat instructies bevat over het herstellen van uw domein controllers. Lees de [Active Directory forest recovery Guide](/windows-server/identity/ad-ds/manage/ad-forest-recovery-guide)om voor te bereiden voor het herstellen van een Active Directory-forest.

- Als u een domein controller wilt herstellen en een resterende werkende domein controller in het domein wilt hebben, kunt u een nieuwe server maken in plaats van de back-up te herstellen. Voeg de **Active Directory Domain Services** serverrol toe aan de nieuwe server om deze een domein controller in het bestaande domein te maken. Vervolgens worden de Active Directory gegevens gerepliceerd naar de nieuwe server. Als u de vorige domein controller uit Active Directory wilt verwijderen, volgt u de stappen [in dit artikel](/windows-server/identity/ad-ds/deploy/ad-ds-metadata-cleanup) om het opruimen van meta gegevens uit te voeren.

>[!NOTE]
>Azure Backup bevat geen herstel op item niveau voor Active Directory. Als u verwijderde objecten wilt herstellen en u toegang hebt tot een domein controller, kunt u de [Prullenbak van Active Directory](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#ad_recycle_bin_mgmt)gebruiken. Als deze methode niet beschikbaar is, kunt u de back-up van uw domein controller gebruiken om de verwijderde objecten te herstellen met het **ntdsutil.exe** -hulp programma, zoals [hier](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac)wordt uitgelegd.
>
>Zie [dit artikel](/windows-server/identity/ad-ds/manage/ad-forest-recovery-authoritative-recovery-sysvol)voor meer informatie over het uitvoeren van een bindende terugzet bewerking van SYSVOL.

## <a name="backing-up-azure-vm-domain-controllers"></a>Back-ups maken van Azure VM-domein controllers

Als de domein controller een Azure-VM is, kunt u een back-up van de server maken met behulp van [Azure VM-back-up](backup-azure-vms-introduction.md).

Meer informatie over [operationele overwegingen voor gevirtualiseerde domein controllers](/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v#operational-considerations-for-virtualized-domain-controllers) om geslaagde back-ups (en toekomstige herstel bewerkingen) van uw Azure VM-domein controllers te garanderen.

## <a name="backing-up-on-premises-domain-controllers"></a>Back-ups maken van on-premises domein controllers

Als u een back-up wilt maken van een on-premises domein controller, moet u een back-up maken van de systeem status gegevens van de server.

- Als u MARS gebruikt, volgt u [deze instructies](backup-azure-system-state.md).
- Als u MABS (Azure Backup Server) gebruikt, volgt u [deze instructies](backup-mabs-system-state-and-bmr.md).

>[!NOTE]
> Het herstellen van on-premises domein controllers (van de systeem status of van Vm's) naar de Azure-Cloud wordt niet ondersteund. Als u de optie voor failover van een on-premises Active Directory omgeving naar Azure wilt, kunt u overwegen [Azure site Recovery](../site-recovery/site-recovery-active-directory.md)te gebruiken.

## <a name="restoring-active-directory"></a>Active Directory herstellen

Active Directory gegevens kunnen worden hersteld in een van de twee modi: **gezaghebbend** of niet- **bindend**. Bij een bindende terugzet bewerking worden de gegevens die op de andere domein controllers in het forest zijn gevonden, overschreven door de herstelde Active Directory gegevens.

In dit scenario wordt echter een domein controller in een bestaand domein opnieuw opgebouwd, dus er moet een niet- **bindende** terugzet bewerking worden uitgevoerd.

Tijdens het herstellen wordt de server gestart in DSRM (Directory Services Restore Mode). U moet het beheerders wachtwoord voor de modus Active Directory terugzetten opgeven.

>[!NOTE]
>Als het DSRM-wacht woord is verg eten, kunt u het opnieuw instellen met behulp van [deze instructies](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc754363(v=ws.11)).

### <a name="restoring-azure-vm-domain-controllers"></a>Azure VM-domein controllers herstellen

Zie Vm's van de [domein controller herstellen](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)als u een Azure VM-domein controller wilt herstellen.

Als u één domein controller-VM of meerdere domein controller-Vm's in één domein herstelt, herstelt u deze zoals elke andere virtuele machine. Directory Services Restore Mode (DSRM) is ook beschikbaar, dus alle Active Directory herstel scenario's zijn levensvatbaar.

Als u een VM van één domein controller in een configuratie met meerdere domeinen moet herstellen, herstelt u de schijven en maakt u een virtuele machine met [behulp van Power shell](backup-azure-vms-automation.md#restore-the-disks).

Als u de laatste domein controller in het domein herstelt of meerdere domeinen in een forest herstelt, raden we u aan een [forest te herstellen](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).

>[!NOTE]
> Gevirtualiseerde domein controllers, van Windows 2012 tot en met behulp van [beveiliging op basis van virtualisatie](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100#virtualization-based-safeguards). Met deze veiligheids maatregelen heeft Active Directory inzicht in de werking van de VM en worden de benodigde stappen uitgevoerd om de Active Directory gegevens te herstellen.

### <a name="restoring-on-premises-domain-controllers"></a>On-premises domein controllers herstellen

Als u een on-premises domein controller wilt herstellen, volgt u de instructies in voor het herstellen van de systeem status naar Windows Server, met behulp van de richt lijnen voor [speciale overwegingen voor herstel van de systeem status op een domein controller](backup-azure-restore-system-state.md#special-considerations-for-system-state-recovery-on-a-domain-controller).

## <a name="next-steps"></a>Volgende stappen

- [Ondersteunings matrix voor Azure Backup](backup-support-matrix.md)