---
title: Azure Storage Explorer gebruiken voor het beheren van Azure Managed disks
description: Meer informatie over het uploaden, downloaden en migreren van een Azure Managed disk over regio's en het maken van een moment opname van een beheerde schijf met behulp van de Azure Storage Explorer.
author: roygara
ms.author: rogarana
ms.date: 09/25/2019
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 9dfce7b76eed5bfc9f4979c0e3041b6c65c28422
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88749371"
---
# <a name="use-azure-storage-explorer-to-manage-azure-managed-disks"></a>Azure Storage Explorer gebruiken voor het beheren van Azure Managed disks

Met Storage Explorer 1.10.0 kunnen gebruikers Managed disks uploaden, downloaden en kopiëren, en moment opnamen maken. Als gevolg van deze aanvullende mogelijkheden kunt u Storage Explorer gebruiken om gegevens van on-premises naar Azure te migreren en gegevens te migreren tussen Azure-regio's.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om dit artikel te volt ooien:
- Een Azure-abonnement
- Een of meer Azure Managed disks
- De nieuwste versie van [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Verbinding maken met een Azure-abonnement

Als uw Storage Explorer niet is verbonden met Azure, kunt u dit niet gebruiken om resources te beheren. In deze sectie wordt verbinding gemaakt met uw Azure-account, zodat u resources kunt beheren met Storage Explorer.

1. Start Azure Storage Explorer en klik op het pictogram voor de **invoeg toepassing** aan de linkerkant.

    ![Klik op het pictogram voor de invoeg toepassing](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. Selecteer **een Azure-account toevoegen** en klik op **volgende**.

    ![Een Azure-account toevoegen](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. Voer in het dialoog venster **Aanmelden bij Azure** uw Azure-referenties in.

    ![Dialoog venster Aanmelden bij Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Selecteer uw abonnement in de lijst en klik op **Toepassen**.

    ![Selecteer uw abonnement](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Een beheerde schijf uploaden van een on-premises VHD

1. Vouw in het linkerdeel venster **schijven** uit en selecteer de resource groep waarnaar u uw schijf wilt uploaden.

    ![Resource groep 1 selecteren](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Selecteer **Uploaden**.

    ![Uploaden selecteren](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. Geef in **VHD uploaden** de bron-VHD op, de naam van de schijf, het type besturings systeem, de regio waarnaar u de schijf wilt uploaden, evenals het account type. In sommige regio's worden beschikbaarheids zones ondersteund voor deze regio's kunt u een door u gewenste zone selecteren.
1. Selecteer **maken** om te beginnen met het uploaden van uw schijf.

    ![Dialoog venster VHD uploaden](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. De status van de upload wordt nu weer gegeven in **activiteiten**.

    ![Upload status](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Als het uploaden is voltooid en u de schijf niet ziet in het rechterdeel venster, selecteert u **vernieuwen**.

## <a name="download-a-managed-disk"></a>Een beheerde schijf downloaden

In de volgende stappen wordt uitgelegd hoe u een beheerde schijf kunt downloaden naar een on-premises VHD. De status van een schijf moet **ongekoppeld** zijn om te kunnen worden gedownload. u kunt een **bijgevoegde** schijf niet downloaden.

1. Als deze nog niet is uitgevouwen, vouwt u in het linkerdeel venster de optie **schijven** uit en selecteert u de resource groep waarvan u de schijf wilt downloaden.

    ![Resource groep 1 selecteren](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Selecteer in het rechterdeel venster de schijf die u wilt downloaden.
1. Selecteer **downloaden** en kies vervolgens waar u de schijf wilt opslaan.

    ![Een beheerde schijf downloaden](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. Selecteer **Opslaan** om te beginnen met het downloaden van de schijf. De status van de down load wordt weer gegeven in **activiteiten**.

    ![Download status](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Een beheerde schijf kopiëren

Met Storage Explorer kunt u een beheerd-schijf kopiëren binnen of tussen verschillende regio's. Een schijf kopiëren:

1. Selecteer in de vervolg keuzelijst **schijven** aan de linkerkant de resource groep die de schijf bevat die u wilt kopiëren.

    ![Resource groep 1 selecteren](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Selecteer in het rechterdeel venster de schijf die u wilt kopiëren en selecteer **kopiëren**.

    ![Een beheerde schijf kopiëren](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. Selecteer in het linkerdeel venster de resource groep waarin u de schijf wilt plakken.

    ![Resource groep 2 selecteren](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. Selecteer **Plakken** in het rechterdeel venster.

    ![Een beheerde schijf plakken](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. Vul in het dialoog venster **schijf plakken** de waarden in. U kunt ook een beschikbaarheids zone opgeven in ondersteunde regio's.

    ![Dialoog venster schijf plakken](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. Selecteer **Plakken** en de schijf wordt gekopieerd, de status wordt weer gegeven in **activiteiten**.

    ![Plak status kopiëren](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Een momentopname maken

1. Selecteer in de vervolg keuzelijst **schijven** aan de linkerkant de resource groep die de schijf bevat waarvan u een moment opname wilt maken.

    ![Resource groep 1 selecteren](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Selecteer aan de rechter kant de schijf die u wilt moment opname en selecteer **moment opname maken**.

    ![Een momentopname maken](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. Geef in **moment opname maken** de naam op van de moment opname en de resource groep waarin u deze wilt maken. Selecteer vervolgens **Maken**.

    ![Dialoog venster moment opname maken](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. Zodra de moment opname is gemaakt, kunt u in **activiteiten** **openen in portal** selecteren om de moment opname in de Azure portal weer te geven.

    ![Moment opname openen in de portal](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Volgende stappen


Meer informatie over [het maken van een virtuele machine op basis van een VHD met behulp van de Azure Portal](windows/create-vm-specialized-portal.md).

Meer informatie over [het koppelen van een beheerde gegevens schijf aan een Windows-VM met behulp van de Azure Portal](windows/attach-managed-disk-portal.md).