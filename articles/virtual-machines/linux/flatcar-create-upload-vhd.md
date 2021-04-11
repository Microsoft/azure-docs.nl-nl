---
title: Een Flatcar-container-VHD voor Linux maken en uploaden voor gebruik in azure
description: Meer informatie over het maken en uploaden van een VHD die een Flatcar container Linux-besturings systeem bevat.
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 5d8be9493b7a312270301e3520f301f797fe2167
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565288"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>Een vooraf gemaakte Flatcar-installatie kopie gebruiken voor Azure

U kunt vooraf ontwikkelde installatie kopieën van virtuele harde schijven van Azure van Flatcar-container Linux downloaden voor elk van de Flatcar ondersteunde kanalen:

- [vast](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [bèta](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [alfa](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [Edge](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

Deze installatie kopie is al volledig ingesteld en geoptimaliseerd voor uitvoering op Azure. U hoeft deze alleen te decomprimeren.

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>Uw eigen virtuele machine op basis van Flatcar maken voor Azure

U kunt er ook voor kiezen om uw eigen Flatcar-container installatie kopie voor Linux te bouwen.

Volg op een op Linux gebaseerde computer de instructies in de [hand leiding voor de Flatcar container Linux Developer SDK](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/). Wanneer u het `image_to_vm.sh` script uitvoert, moet u `--format=azure` een virtuele harde schijf met Azure maken.

## <a name="next-steps"></a>Volgende stappen

Zodra u het VHD-bestand hebt, kunt u het resulterende bestand gebruiken om nieuwe virtuele machines te maken in Azure. Zie [een virtuele Linux-machine maken op basis van een aangepaste schijf](upload-vhd.md#option-1-upload-a-vhd)als dit de eerste keer is dat u een VHD-bestand naar Azure uploadt.
