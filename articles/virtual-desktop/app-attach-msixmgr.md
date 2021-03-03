---
title: Het hulp programma MSIXMGR gebruiken-Azure
description: Het gebruik van het hulp programma MSIXMGR voor virtueel bureau blad van Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 02/23/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4b34fb0d3bb2d49255007b9722a0a636c1441b8c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744683"
---
# <a name="using-the-msixmgr-tool"></a>Het MSIXMGR-hulp programma gebruiken

Het hulp programma MSIXMGR is voor het uitbreiden van MSIX-verpakte toepassingen naar MSIX-installatie kopieën. Het hulp programma gebruikt een MSIX-toepassing (. MSIX) en wordt deze uitgebreid naar een VHD-, VHDx-of CIM-bestand. De resulterende MSIX-installatie kopie wordt opgeslagen in het Azure Storage-account dat wordt gebruikt door uw Windows-implementatie voor virtueel bureau blad. In dit artikel wordt uitgelegd hoe u het MSIXMGR-hulp programma gebruikt.

>[!NOTE]
>Om compatibiliteit te garanderen, zorgt u ervoor dat de CIMs die uw MSIX-installatie kopieën opslaat, worden gegenereerd op de versie van het besturings systeem die u uitvoert in uw Windows Virtual Desktop host-Pools. MSIXMGR kan CIM-bestanden maken, maar u kunt alleen die bestanden gebruiken met een hostgroep met Windows 10 20H2.

## <a name="requirements"></a>Vereisten

Voordat u de instructies in dit artikel kunt volgen, moet u het volgende doen:

- [Het MSIXMGR-hulp programma downloaden](https://aka.ms/msixmgr)
- Een MSIX-toepassing (. MSIX-bestand)
- Beheer machtigingen ophalen op de computer waarop u de MSIX-installatie kopie wilt maken

## <a name="create-an-msix-image"></a>Een MSIX-installatie kopie maken

Uitbrei ding is het proces van het maken van een MSIX-verpakte toepassing (. MSIX) en uitgepakt deze naar een MSIX-installatie kopie (. VHD (x) of. CIM-bestand).

Een MSIX-bestand uitbreiden:

1. [Down load het MSIXMGR-hulp programma](https://aka.ms/msixmgr) als u dat nog niet hebt gedaan.

2. Unzip MSIXMGR.zip naar een lokale map.

3. Open een opdracht prompt in de modus met verhoogde bevoegdheden.

4. Zoek de lokale map uit stap 2.

5. Voer de volgende opdracht uit in de opdracht prompt om een MSIX-installatie kopie te maken.

    ```cmd
    msixmgr.exe -Unpack -packagePath <path to package> -destination <output folder> [-applyacls] [-create] [-vhdSize <size in MB>] [-filetype <CIM | VHD | VHDX>] [-rootDirectory <rootDirectory>]
    ```

    Vergeet niet om de waarden van de tijdelijke aanduidingen te vervangen door de relevante waarden. Bijvoorbeeld:

    ```cmd
    msixmgr.exe -Unpack -packagePath "C:\Users\%username%\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
    ```

6. Nu u de installatie kopie hebt gemaakt, gaat u naar de doelmap en zorgt u ervoor dat u de MSIX-installatie kopie hebt gemaakt (. VHDX).

## <a name="create-an-msix-image-in-a-cim-file"></a>Een MSIX-installatie kopie maken in een CIM-bestand

U kunt ook de opdracht in [stap 5](#create-an-msix-image) gebruiken om CIM-en VHDX-bestanden te maken door het bestands type en het doelpad te vervangen.

Hier volgt een voor beeld van de manier waarop u deze opdracht kunt gebruiken om een CIM-bestand te maken:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.cim" -applyacls -create -vhdSize 200 -filetype "cim" -rootDirectory apps
```

U kunt deze opdracht als volgt gebruiken om een VHDX te maken:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het koppelen van MSIX-apps bij [Wat is een MSIX-app-koppeling?](what-is-app-attach.md)

Bekijk de volgende artikelen voor meer informatie over het instellen van een app-koppeling:

- [MSIX-app-koppeling instellen met de Azure-portal](app-attach-azure-portal.md)
- [MSIX-app-koppeling instellen met Power shell](app-attach-powershell.md)
- [Power shell-scripts maken voor MSIX-app koppelen](app-attach.md)
- [Een MSIX-installatie kopie voorbereiden voor het virtuele bureau blad van Windows](app-attach-image-prep.md)
- [Een bestands share instellen voor het koppelen van MSIX-apps](app-attach-file-share.md)

Als u vragen hebt over het toevoegen van MSIX-apps, raadpleegt u de [woorden lijst](app-attach-glossary.md) [Veelgestelde vragen over apps](app-attach-faq.md) en app-koppeling toevoegen.
