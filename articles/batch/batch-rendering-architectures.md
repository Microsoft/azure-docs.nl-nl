---
title: Azure rendering-referentie architecturen
description: Architecturen voor het gebruik van Azure Batch en andere Azure-Services voor het uitbreiden van een on-premises render-farm door bursting naar de Cloud
ms.date: 02/07/2019
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 784fd89c3dea88e25a2058713897c7a655c8a3af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83726516"
---
# <a name="reference-architectures-for-azure-rendering"></a>Referentie architecturen voor Azure-Rendering

In dit artikel worden architectuur diagrammen op hoog niveau weer gegeven voor scenario's om een on-premises render-farm naar Azure uit te breiden. In de voor beelden ziet u verschillende opties voor Azure compute-, netwerk-en opslag Services.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybride met NFS of CFS

Het volgende diagram toont een hybride scenario met de volgende Azure-Services:

* **Compute** -Azure batch pool of virtuele-machine schaalset.

* **Netwerk** -on-premises: Azure EXPRESSROUTE of VPN. Azure: Azure VNet.

* **Opslag** -invoer-en uitvoer bestanden: NFS of CFS met behulp van Azure-vm's, gesynchroniseerd met on-premises opslag via Azure file sync of rsync. U kunt ook avere vFXT naar invoer-of uitvoer bestanden van on-premises NAS-apparaten met behulp van NFS.

  ![Cloud bursting-Hybrid met NFS of CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Hybride met Blobfuse

Het volgende diagram toont een hybride scenario met de volgende Azure-Services:

* **Compute** -Azure batch pool of virtuele-machine schaalset.

* **Netwerk** -on-premises: Azure EXPRESSROUTE of VPN. Azure: Azure VNet.

* **Opslag** -invoer-en uitvoer bestanden: Blob Storage, gekoppeld aan Compute-bronnen via Azure Blobfuse.

  ![Cloud bursting-Hybrid met Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybride Compute en opslag

In het volgende diagram ziet u een volledig verbonden hybride scenario voor zowel Compute als Storage, inclusief de volgende Azure-Services:

* **Compute** -Azure batch pool of virtuele-machine schaalset.

* **Netwerk** -on-premises: Azure EXPRESSROUTE of VPN. Azure: Azure VNet.

* **Opslag** -cross-premises: avere vFXT. Optioneel archiveren van on-premises bestanden via Azure Data Box naar Blob Storage, of on-premises avere FXT voor de versnelling van de NAS.

  ![Cloud bursting-hybride Compute en opslag](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het gebruik van [weergave beheerders](batch-rendering-render-managers.md) met Azure batch.

* Meer informatie over opties voor [rendering in azure](batch-rendering-service.md).