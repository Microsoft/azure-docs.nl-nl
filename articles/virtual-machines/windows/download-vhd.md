---
title: Een Windows-VHD downloaden van Azure
description: Download een Windows-VHD met behulp van Azure Portal.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: 32b9753b79273ce747d00cba077dd8a5ee6d724d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565284"
---
# <a name="download-a-windows-vhd-from-azure"></a>Een Windows-VHD downloaden van Azure

In dit artikel leert u hoe u een VHD-bestand (virtuele harde schijf) van Windows downloadt vanuit Azure met behulp van de Azure Portal.

## <a name="optional-generalize-the-vm"></a>Optioneel: De VM generaliseren

Als u de VHD als [](tutorial-custom-images.md) installatie afbeelding wilt gebruiken om andere VM's te maken, moet u [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) gebruiken om het besturingssysteem te generaliseren. Anders moet u een kopie maken van de schijf voor elke VM die u wilt maken.

Als u de VHD als een afbeelding wilt gebruiken om andere VM's te maken, generaliseert u de VM.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als u dat nog niet hebt gedaan.
2. [Maak verbinding met de VM](connect-logon.md). 
3. Open op de VM het opdrachtpromptvenster als beheerder.
4. Wijzig de map in *%windir%\system32\sysprep* en voer de sysprep.exe.
5. Selecteer in Hulpprogramma voor systeemvoorbereiding dialoogvenster **System Out-of-Box Experience (OOBE)** invoeren en zorg ervoor dat **Generaliseren** is geselecteerd.
6. Selecteer in Afsluitopties **afsluiten** en klik vervolgens op **OK.** 

Als u uw huidige VM niet wilt generaliseren, kunt u nog steeds een ge generaliseerde afbeelding maken door eerst een momentopname van de besturingssysteemschijf te [maken,](#alternative-snapshot-the-vm-disk)een nieuwe VM te maken op de momentopname en vervolgens de kopie te generaliseren.

## <a name="stop-the-vm"></a>De virtuele machine stoppen

Een VHD kan niet worden gedownload van Azure als deze is gekoppeld aan een virtuele VM die wordt uitgevoerd. Als u de VM actief wilt houden, kunt u een [momentopname maken en vervolgens de momentopname downloaden.](#alternative-snapshot-the-vm-disk)

1. Klik in het menu Hub in Azure Portal op **Virtual Machines**.
1. Selecteer de VM in de lijst.
1. Klik op de blade voor de VM op **Stoppen.**

### <a name="alternative-snapshot-the-vm-disk"></a>Alternatief: Momentopname maken van de VM-schijf

Maak een momentopname van de schijf die u wilt downloaden.

1. Selecteer de VM in de [portal](https://portal.azure.com).
2. Selecteer **Schijven in het** menu links en selecteer vervolgens de schijf waarop u een momentopname wilt maken. De details van de schijf worden weergegeven.  
3. Selecteer **Momentopname** maken in het menu bovenaan de pagina. De **pagina Momentopname** maken wordt geopend.
4. In **Naam** typt u een naam voor de momentopname. 
5. Bij **Type momentopname** selecteert **u Volledig** of **Incrementeel.**
6. Als u klaar bent, selecteert u **Beoordelen en maken**.

Uw momentopname wordt binnenkort gemaakt en kan vervolgens worden gebruikt om een andere VM te downloaden of te maken.

> [!NOTE]
> Als u de VM niet eerst stopt, is de momentopname niet schoon. De momentopname heeft dezelfde status als wanneer de VM is gecrasht of gecrasht op het moment dat de momentopname is gemaakt.  Hoewel het meestal veilig is, kan dit problemen veroorzaken als de toepassingen die worden uitgevoerd een tijdstip niet crash-bestand zijn.
>  
> Deze methode wordt alleen aanbevolen voor VM's met één besturingssysteemschijf. VM's met een of meer gegevensschijven moeten worden gestopt voordat ze worden gedownload of voordat u een momentopname maakt voor de besturingssysteemschijf en elke gegevensschijf.

## <a name="generate-download-url"></a>Download-URL genereren

Als u het VHD-bestand wilt downloaden, moet u een [SAS-URL (Shared Access Signature)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) genereren. Wanneer de URL wordt gegenereerd, wordt er een verlooptijd toegewezen aan de URL.

1. Klik op de pagina voor de VM op **Schijven** in het menu links.
1. Selecteer de besturingssysteemschijf voor de VM.
1. Selecteer schijfexport in  het menu links op de pagina voor de schijf.
1. De standaardverlooptijd van de URL is *3600* seconden (één uur). Mogelijk moet u dit verhogen voor Windows-besturingssysteemschijven of grote gegevensschijven. **36000** seconden (10 uur) is meestal voldoende.
1. Klik **op URL genereren.**

> [!NOTE]
> De verlooptijd wordt vanaf de standaardwaarde verhoogd om voldoende tijd te bieden om het grote VHD-bestand voor een Windows Server-besturingssysteem te downloaden. Het downloaden van grote VHD's kan enkele uren duren, afhankelijk van uw verbinding en de grootte van de VM. 
> 
> 

## <a name="download-vhd"></a>VHD downloaden

1. Klik onder de URL die is gegenereerd op Het VHD-bestand downloaden.
1. Mogelijk moet u in uw browser **op Opslaan** klikken om het downloaden te starten. De standaardnaam voor het VHD-bestand is *abcd.*

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [uploaden van een VHD-bestand naar Azure](upload-generalized-managed.md). 
- [Beheerde schijven maken van niet-beheerde schijven in een opslagaccount.](attach-disk-ps.md)
- [Azure-schijven beheren met PowerShell](tutorial-manage-data-disk.md).
