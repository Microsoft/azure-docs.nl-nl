---
title: Rendering van toepassingen
description: Het is mogelijk om alle rendering-toepassingen met Azure Batch te gebruiken. Azure Marketplace-VM-installatie kopieën zijn echter beschikbaar met vooraf geïnstalleerde toepassingen.
ms.date: 09/19/2019
ms.topic: how-to
ms.openlocfilehash: d4eefa287575d68acccb2697e1d262c7e48dab62
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234406"
---
# <a name="pre-installed-applications-on-batch-rendering-vm-images"></a>Vooraf geïnstalleerde toepassingen voor VM-installatie kopieën voor batch Rendering

Het is mogelijk om alle rendering-toepassingen met Azure Batch te gebruiken. Azure Marketplace-VM-installatie kopieën zijn echter beschikbaar met vooraf geïnstalleerde toepassingen.

Indien van toepassing is licenties voor betalen per gebruik beschikbaar voor de vooraf geïnstalleerde rendering-toepassingen. Wanneer een batch-pool wordt gemaakt, kunnen de vereiste toepassingen worden opgegeven en worden de kosten van de VM en de toepassingen per minuut in rekening gebracht. Toepassings prijzen worden vermeld op de [pagina met Azure batch prijzen](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Sommige toepassingen bieden alleen ondersteuning voor Windows, maar de meeste worden ondersteund voor zowel Windows als Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Toepassingen op CentOS 7-rendering van installatie kopieën

De volgende lijst is van toepassing op CentOS 7,6, version 1.1.6 rendering installatie kopieën.

* Autodesk Maya I/O 2017 Update 5 (cut 201708032230)
* Autodesk Maya I/O 2018-update 2 (knippen 201711281015)
* Update van I/O 2019 Autodesk Maya 1
* Autodesk Arnold voor Maya 2017 (Arnold versie 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold voor Maya 2018 (Arnold versie 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Arnold voor Maya 2019 (Arnold versie 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017 (versie 3.60.04)
* Chaos Group V-Ray for Maya 2018 (versie 3.60.04)
* Blender (2.68)
* Blender (2,8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Toepassingen op de nieuwste installatie kopieën van Windows Server 2016-Rendering

De volgende lijst is van toepassing op Windows Server 2016, version 1.3.8 rendering installatie kopieën.

* Autodesk Maya I/O 2017 Update 5 (versie 17.4.5459)
* Autodesk Maya I/O 2018 update 6 (versie 18.4.0.7622)
* Autodesk Maya I/O 2019
* Autodesk 3ds Max I/O 2018 Update 4 (versie 20.4.0.4254)
* Auto Desk 3ds Max I/O 2019 update 1 (versie 21.2.0.2219)
* Auto Desk 3ds Max I/O 2020-update 2
* Autodesk Arnold voor Maya 2017 (Arnold versie 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Arnold voor Maya 2018 (Arnold versie 5.3.0.2) MtoA-3.2.0.2-2018
* Autodesk Arnold voor Maya 2019 (Arnold versie 5.3.0.2) MtoA-3.2.0.2-2019
* Autodesk Arnold voor 3ds Max 2018 (Arnold version 5.3.0.2) (versie 1.2.926)
* Autodesk Arnold voor 3ds Max 2019 (Arnold version 5.3.0.2) (versie 1.2.926)
* Autodesk Arnold voor 3ds Max 2020 (Arnold version 5.3.0.2) (versie 1.2.926)
* Chaos-groep V-Ray voor Maya 2017 (versie 4.12.01)
* Chaos-groep V-Ray voor Maya 2018 (versie 4.12.01)
* Chaos-groep V-Ray voor Maya 2019 (versie 4.04.03)
* Chaos Group V-Ray voor 3ds Max 2018 (versie 4.20.01)
* Chaos Group V-Ray voor 3ds Max 2019 (versie 4.20.01)
* Chaos Group V-Ray voor 3ds Max 2020 (versie 4.20.01)
* Blender (2.79)
* Blender (2,80)
* AZ 10

> [!IMPORTANT]
> Begin met het uitvoeren van een V-Ray met Maya buiten de [Azure batch extensie sjablonen](https://github.com/Azure/batch-extension-templates) `vrayses.exe` voordat u de weer gave uitvoert. Als u de vrayses.exe buiten de sjablonen wilt starten, kunt u de volgende opdracht gebruiken `%MAYA_2017%\vray\bin\vrayses.exe"` .
>
> Zie de begin taak van de [Maya-en V-Ray-sjabloon](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) op github voor een voor beeld.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Toepassingen op eerdere Windows Server 2016-rendering van installatie kopieën

De volgende lijst is van toepassing op Windows Server 2016, version 1.3.7 rendering installatie kopieën.

* Autodesk Maya I/O 2017 Update 5 (versie 17.4.5459)
* Autodesk Maya I/O 2018 update 4 (versie 18.4.0.7622)
* Auto Desk 3ds Max I/O 2019 update 1 (versie 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (versie 20.4.0.4254)
* Autodesk Arnold voor Maya 2017 (Arnold versie 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold voor Maya 2018 (Arnold versie 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold voor 3ds Max 2018 (Arnold version 5.0.2.4) (versie 1.2.926)
* Autodesk Arnold voor 3ds Max 2019 (Arnold version 5.0.2.4) (versie 1.2.926)
* Chaos-groep V-Ray voor Maya 2018 (versie 3.52.03)
* Chaos Group V-Ray voor 3ds Max 2018 (versie 3.60.02)
* Chaos-groep V-Ray voor Maya 2019 (versie 3.52.03)
* Chaos Group V-Ray voor 3ds Max 2019 (versie 4.10.01)
* Blender (2.79)

> [!NOTE]
> Chaos Group V-Ray voor 3ds Max 2019 (versie 4.10.01) introduceert een belang rijke wijziging in de V-Ray. Als u de vorige versie (versie 3.60.02) wilt gebruiken, gebruikt u Windows Server 2016, versie 1.3.2 rendering-knoop punten.

## <a name="next-steps"></a>Volgende stappen

Als u de weer gave-VM-installatie kopieën wilt gebruiken, moet u deze opgeven in de groeps configuratie wanneer er een groep wordt gemaakt. Bekijk de [mogelijkheden van de batch-pool voor rendering](./batch-rendering-functionality.md).
