---
title: Uw StorSimple-apparaat bijwerken | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u de StorSimple-update functie kunt gebruiken om updates en hotfixes voor reguliere en onderhouds modus te installeren.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: faf06775b78c5a7c90cea000ac0a1eb768107ef4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956837"
---
# <a name="update-your-storsimple-8000-series-device"></a>Uw StorSimple 8000 Series-apparaat bijwerken
> [!NOTE]
> De klassieke portal voor StorSimple is afgeschaft. De StorSimple-apparaatbeheerfuncties worden automatisch volgens schema verplaatst naar de nieuwe Azure Portal. U ontvangt hierover een e-mailbericht en een portalmelding. Dit document wordt binnenkort ook ingetrokken. Zie [FAQ: Move to Azure portal](./index.yml) (Veelgestelde vragen: verplaatsing naar Azure Portal) voor vragen met betrekking tot de verplaatsing.

## <a name="overview"></a>Overzicht
Met de functies van StorSimple-updates kunt u uw StorSimple-apparaat eenvoudig up-to-date houden. Afhankelijk van het update type, kunt u updates op het apparaat Toep assen via de klassieke Azure-portal of via de Windows Power shell-interface. In deze zelf studie worden de update typen beschreven en wordt uitgelegd hoe u deze kunt installeren.

U kunt twee soorten updates van apparaten Toep assen: 

* Normale updates (of normale modus)
* Onderhouds modus updates

U kunt regel matige updates installeren via de klassieke Azure-portal of Windows Power shell. u moet echter Windows Power shell gebruiken om updates voor de onderhouds modus te installeren. 

Elk update type wordt afzonderlijk beschreven.

### <a name="regular-updates"></a>Regel matige updates
Regel matige updates zijn niet-verstorende updates die kunnen worden geïnstalleerd wanneer het apparaat de normale modus heeft. Deze updates worden door de Microsoft Update-website toegepast op elke apparaat controller. 

> [!IMPORTANT]
> Er kan een failover van een controller optreden tijdens het update proces. Dit heeft echter geen invloed op de beschik baarheid van het systeem of de bewerking.
> 
> 

* Zie [reguliere updates installeren via de klassieke Azure-Portal](#install-regular-updates-via-the-azure-classic-portal)voor meer informatie over het installeren van regel matige updates via de klassieke Azure-Portal.
* U kunt ook regel matige updates installeren via Windows PowerShell voor StorSimple. Zie voor meer informatie [regel matige updates installeren via Windows PowerShell voor StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Onderhouds modus updates
Updates voor onderhouds modus zijn verstorende updates, zoals upgrades van de schijf firmware. Voor deze updates moet het apparaat in de onderhouds modus worden geplaatst. Zie [stap 2: de onderhouds modus invoeren](#step2)voor meer informatie. U kunt de klassieke Azure-Portal niet gebruiken om updates voor de onderhouds modus te installeren. In plaats daarvan moet u Windows PowerShell voor StorSimple gebruiken. 

Zie updates voor de [onderhouds modus installeren via Windows PowerShell voor StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple)voor meer informatie over het installeren van updates voor de onderhouds modus.

> [!IMPORTANT]
> Updates voor de onderhouds modus moeten afzonderlijk worden toegepast op elke controller. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Regel matige updates installeren via de klassieke Azure-Portal
U kunt de klassieke Azure-Portal gebruiken om updates toe te passen op uw StorSimple-apparaat.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Installeer reguliere updates via Windows PowerShell voor StorSimple
U kunt ook Windows PowerShell voor StorSimple gebruiken om regel matige updates (normale modus) toe te passen.

> [!IMPORTANT]
> Hoewel u regel matige updates kunt installeren met behulp van Windows PowerShell voor StorSimple, raden wij u ten zeerste aan om regel matig updates te installeren via de klassieke Azure-Portal. Vanaf update 1 worden vóór controles uitgevoerd voordat updates vanuit de portal worden geïnstalleerd. Deze vooraf controles hebben voor rang op storingen en zorgen voor een soepeler ervaring. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Updates voor onderhouds modus installeren via Windows PowerShell voor StorSimple
U gebruikt Windows PowerShell voor StorSimple om updates voor de onderhouds modus toe te passen op uw StorSimple-apparaat. Alle I/O-aanvragen worden in deze modus onderbroken. Services als niet-vluchtig RAM-geheugen (Random Access Memory) of de Clustering-service worden ook gestopt. Beide controllers worden opnieuw opgestart wanneer u deze modus invoert of afsluit. Wanneer u deze modus verlaat, worden alle services hervat en moeten ze in orde zijn. (Dit kan enkele minuten duren.)

Als u updates voor onderhouds modus wilt Toep assen, ontvangt u een waarschuwing via de klassieke Azure-Portal waarvoor u updates hebt die moeten worden geïnstalleerd. Deze waarschuwing bevat instructies voor het gebruik van Windows PowerShell voor StorSimple om de updates te installeren. Nadat u het apparaat hebt bijgewerkt, gebruikt u dezelfde procedure om het apparaat te wijzigen in de normale modus. Zie [stap 4: onderhouds modus afsluiten](#step4)voor stapsgewijze instructies.

> [!IMPORTANT]
> * Voordat u de onderhouds modus inschakelt, controleert u of beide controllers in orde zijn door de **status hardware** te controleren op de pagina **onderhoud** in de klassieke Azure-Portal. Als de controller niet in orde is, neemt u contact op met Microsoft Ondersteuning om de volgende stappen uit te voeren. Ga naar contact Microsoft Ondersteuning voor meer informatie. 
> * Wanneer u zich in de onderhouds modus bevindt, moet u de update eerst op één controller Toep assen en vervolgens op de andere controller.
> 
> 

### <a name="step-1-connect-to-the-serial-console"></a>Stap 1: verbinding maken met de seriële console <a name="step1"></a>
Gebruik eerst een toepassing zoals PuTTy om toegang te krijgen tot de seriële console. In de volgende procedure wordt uitgelegd hoe u PuTTy gebruikt om verbinding te maken met de seriële console.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode"></a>Stap 2: de onderhouds modus invoeren <a name="step2"></a>
Nadat u verbinding hebt gemaakt met de-console, controleert u of er updates zijn om te installeren en voert u de onderhouds modus in om ze te installeren.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates"></a>Stap 3: updates installeren <a name="step3"></a>
Installeer vervolgens uw updates.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode"></a>Stap 4: onderhouds modus afsluiten <a name="step4"></a>
Ten slotte wordt de onderhouds modus afgesloten.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Installeer hotfixes via Windows PowerShell voor StorSimple
In tegens telling tot updates voor Microsoft Azure StorSimple, worden hotfixes geïnstalleerd vanuit een gedeelde map. Net als bij updates zijn er twee typen hotfixes: 

* Reguliere hotfixes 
* Hotfixes voor onderhouds modus  

In de volgende procedures wordt uitgelegd hoe u Windows PowerShell voor StorSimple gebruikt voor het installeren van hotfixes voor reguliere en onderhouds modus.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Wat gebeurt er met updates als u de fabrieks instellingen van het apparaat opnieuw instelt?
Als een apparaat opnieuw wordt ingesteld op de fabrieks instellingen, gaan alle updates verloren. Nadat het apparaat voor de fabrieks instellingen is geregistreerd en geconfigureerd, moet u de updates hand matig installeren via de klassieke Azure-Portal en/of de Windows PowerShell voor StorSimple. Zie [de standaard fabrieks instellingen van het apparaat herstellen](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)voor meer informatie over het opnieuw instellen van de fabriek.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het gebruik van Windows PowerShell voor StorSimple voor het beheren van uw StorSimple-apparaat](./storsimple-8000-windows-powershell-administration.md).
* Meer informatie over [het gebruik van de StorSimple Manager-service voor het beheren van uw StorSimple-apparaat](./storsimple-8000-manager-service-administration.md).