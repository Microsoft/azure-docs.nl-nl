---
title: Beheer van Azure Stack Edge Pro GPU-opslag account | Microsoft Docs
description: Hierin wordt beschreven hoe u de Azure Portal gebruikt voor het beheren van het opslag account op uw Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 2d9520c8f97171dbf2f46aa2c94b9e1e280ed86c
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201284"
---
# <a name="use-the-azure-portal-to-manage-edge-storage-accounts-on-your-azure-stack-edge-pro"></a>Gebruik de Azure-portal om Edge-opslagaccounts te beheren op uw Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u Edge-opslag accounts kunt beheren op uw Azure Stack Edge Pro. U kunt de Azure Stack Edge Pro beheren via de Azure Portal of via de lokale web-UI. Gebruik de Azure Portal om Edge-opslag accounts op uw apparaat toe te voegen of te verwijderen.

## <a name="about-edge-storage-accounts"></a>Over Edge-opslag accounts

U kunt gegevens van uw Azure Stack Edge Pro-apparaat overdragen via de SMB-, NFS-of REST-protocollen. Als u gegevens wilt overdragen naar Blob-opslag met behulp van de REST Api's, moet u Edge-opslag accounts maken op uw Azure Stack Edge Pro. 

De Edge-opslag accounts die u op het Azure Stack Edge Pro-apparaat toevoegt, worden toegewezen aan Azure Storage-accounts. Alle gegevens die naar de Edge-opslag accounts worden geschreven, worden automatisch naar de Cloud gepusht.

Hieronder ziet u een diagram met een gedetailleerde beschrijving van de twee typen accounts en de wijze waarop de gegevens stromen van elk van deze accounts naar Azure worden weer gegeven:

![Diagram voor Blob Storage-accounts](media/azure-stack-edge-gpu-manage-storage-accounts/ase-blob-storage.svg)

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Edge-opslagaccount toevoegen
> * Een Edge-opslag account verwijderen


## <a name="add-an-edge-storage-account"></a>Edge-opslagaccount toevoegen

Voer de volgende procedure uit om een Edge-opslagaccount te maken:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]

## <a name="delete-an-edge-storage-account"></a>Een Edge-opslag account verwijderen

Voer de volgende stappen uit om een Edge-opslag account te verwijderen.

1. Ga naar **configuratie > opslag accounts** in uw resource. Selecteer in de lijst met opslag accounts het opslag account dat u wilt verwijderen. Selecteer in de bovenste opdracht balk de optie **opslag account verwijderen**.

    ![Ga naar de lijst met opslag accounts](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-1.png)

2. Bevestig op de Blade **opslag account verwijderen** het opslag account dat u wilt verwijderen en selecteer **verwijderen**.

    ![Het opslag account bevestigen en verwijderen](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-2.png)

De lijst met opslag accounts wordt bijgewerkt om het verwijderen weer te geven.


## <a name="add-delete-a-container"></a>Een container toevoegen, verwijderen

U kunt ook de containers voor deze opslag accounts toevoegen of verwijderen.

Voer de volgende stappen uit om een container toe te voegen:

1. Selecteer het opslag account dat u wilt beheren. Selecteer op de bovenste opdracht balk **+ container toevoegen**.

    ![Selecteer een opslag account om een container toe te voegen](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-1.png)

2. Geef een naam op voor de container. Deze container wordt gemaakt in uw Edge-opslag account en het Azure Storage-account dat aan dit account is toegewezen. 

    ![Rand container toevoegen](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-2.png)

De lijst met containers wordt bijgewerkt op basis van de zojuist toegevoegde container.

![Lijst met containers is bijgewerkt](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-4.png)

U kunt nu een container selecteren in deze lijst en selecteren **+ container verwijderen** in de bovenste opdracht balk. 

![Een container verwijderen](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-3.png)

## <a name="sync-storage-keys"></a>Opslagsleutels synchroniseren

U kunt de toegangs sleutels voor de Edge (lokale) opslag accounts op uw apparaat synchroniseren. 

Voer de volgende stappen uit om de toegangs sleutel voor het opslag account te synchroniseren:

1. Selecteer in uw resource het opslag account dat u wilt beheren. Selecteer in de bovenste opdracht balk de optie **opslag sleutel synchroniseren**.

    ![Opslag sleutel voor synchronisatie selecteren](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-1.png)

2. Selecteer **Ja** als u om bevestiging wordt gevraagd.

    ![Selecteer de synchronisatie opslag sleutel 2](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-2.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Gebruikers beheren via Azure Portal](azure-stack-edge-gpu-manage-users.md).
