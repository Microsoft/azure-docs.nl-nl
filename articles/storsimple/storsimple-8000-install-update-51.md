---
title: Update 5,1 op StorSimple 8000 Series-apparaat installeren | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u StorSimple 8000 Series Update 5,1 installeert op uw apparaat met de StorSimple 8000-serie.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/05/2020
ms.author: alkohli
ms.openlocfilehash: 5b9958f3dd497aa612a92947b8d968439ef9d0e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91575959"
---
# <a name="install-update-51-on-your-storsimple-device"></a>Update 5,1 op uw StorSimple-apparaat installeren

## <a name="overview"></a>Overzicht

In deze zelf studie wordt uitgelegd hoe u update 5,1 installeert op een StorSimple-apparaat waarop een eerdere software versie wordt uitgevoerd via de Azure Portal. <!--The hotfix method is used when you are trying to install Update 5.1 on a device running pre-Update 3 versions. The hotfix method is also used when a gateway is configured on a network interface other than DATA 0 of the StorSimple device and you are trying to update from a pre-Update 1 software version.-->

Update 5,1 bevat niet-verstorende beveiligings updates. De niet-verstorende of reguliere updates kunnen worden toegepast via de Azure Portal <!--or by the hotfix method-->.

> [!IMPORTANT]
>
> * Update 5,1 is een verplichte update die direct moet worden geïnstalleerd. Zie [Update 5,1 Release Notes](storsimple-update51-release-notes.md)(Engelstalig) voor meer informatie.
> * Vóór de installatie wordt een set hand matig en automatische controles uitgevoerd om te bepalen wat de status is van de apparaatstatus en de netwerk verbinding. Deze controle vooraf wordt alleen uitgevoerd als u de updates van de Azure Portal toepast.
> * Als u wilt installeren met behulp van de hotfix-methode, neemt u contact op met [Microsoft ondersteuning](mailto:support@microsoft.com).

<!--
> * We strongly recommend that when updating a device running versions prior to Update 3, you install the updates using hotfix method. If you encounter any issues, [log a support ticket](storsimple-8000-contact-microsoft-support.md). 


> * We recommend that you install the software and other regular updates via the Azure portal. You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device.


> * If running the optional StorSimple Snapshot Manager, ensure that you have upgraded your Snapshot Manager version to Update 5.1 prior to updating the device.
-->

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>Installeer update 5,1 via de Azure Portal

Voer de volgende stappen uit om uw apparaat bij te werken naar [update 5,1](storsimple-update51-release-notes.md).

> [!NOTE]
> Micro soft haalt extra diagnostische gegevens van het apparaat op. Als het operationele team apparaten identificeert die problemen ondervinden, zijn we beter uitgerust met het verzamelen van gegevens van het apparaat en het vaststellen van problemen.

#### <a name="to-install-an-update-from-the-azure-portal"></a>Een update installeren via Azure Portal

1. Selecteer uw apparaat op de pagina van de StorSimple-service.

    ![Apparaat selecteren](./media/storsimple-8000-install-update-51/update1.png)

2. Navigeren naar **Apparaatinstellingen apparaat-**  >  **updates**.

    ![Scherm afbeelding van de Blade instellingen met de optie voor het bijwerken van het apparaat.](./media/storsimple-8000-install-update-51/update2.png)

3. Er wordt een melding weer gegeven als er nieuwe updates beschikbaar zijn. U kunt ook op de Blade **updates voor apparaten** klikken op **updates scannen**. Er wordt een taak gemaakt om te zoeken naar beschikbare updates. U ontvangt een melding wanneer de taak is voltooid.

    ![Scherm afbeelding van de Blade instellingen met de optie updates van het apparaat wordt aangeroepen en de Blade apparaat bijwerken met de nieuwe reguliere updates is beschikbaar bericht met de naam.](./media/storsimple-8000-install-update-51/update3.png)

4. We raden u aan de releaseopmerkingen te bekijken voordat u een update toepast op uw apparaat. Klik op **updates installeren** om updates toe te passen. Controleer op de Blade **regel matige updates bevestigen** de vereiste onderdelen om te volt ooien voordat u updates toepast. Schakel het selectie vakje in om aan te geven dat u klaar bent voor het bijwerken van het apparaat en klik vervolgens op **installeren**.

    ![Scherm afbeelding van de Blade updates van het apparaat met de optie voor het installeren van updates en de optie regel matige updates bevestigen met de optie akkoord en installeren.](./media/storsimple-8000-install-update-51/update4.png)

5. Er wordt een reeks vereiste controles gestart. Deze controles zijn onder andere:
   
   * **Statuscontroles controllers** om te controleren of beide apparaatcontrollers in orde en online zijn.
   * **Statuscontroles hardwareonderdelen** om te controleren of alle hardwareonderdelen op uw StorSimple-apparaat in orde zijn.
   * **DATA 0-controles** om te controleren of DATA 0 is ingeschakeld op uw apparaat. Als deze interface niet is ingeschakeld, schakelt u deze in en probeert u het vervolgens opnieuw.

     De update wordt alleen gedownload en geïnstalleerd als alle controles zijn voltooid. U krijgt een melding wanneer de controles worden uitgevoerd. Als de voor controle mislukt, dan worden de redenen voor de fout weer gegeven. Los deze problemen op en voer de bewerking opnieuw uit. Mogelijk moet u contact opnemen met Microsoft-ondersteuning als u deze problemen niet zelf kunt oplossen.

7. Nadat de voor controle is voltooid, wordt een update taak gemaakt. U krijgt een melding wanneer de bijwerktaak is gemaakt.
   
    ![Scherm afbeelding van de melding met de tekst ' de taak software-updates starten '.](./media/storsimple-8000-install-update-51/update6.png)
   
    De update wordt vervolgens op het apparaat toegepast.

9. De update neemt enkele uren in beslag. Selecteer de bijwerktaak en klik op **Details** om de details van de taak te bekijken.

    ![Scherm afbeelding van de Blade apparaat bijwerken met de optie Software-updates downloaden en installeren die worden uitgevoerd en de Blade updates installeren.](./media/storsimple-8000-install-update-51/update8.png)

     U kunt ook de voortgang van de update taak controleren op basis van **apparaatinstellingen > taken**. Op de Blade **taken** ziet u de voortgang van de update.

     ![Scherm opname van de Blade instellingen met taken die worden genoemd en de Blade taken met de voortgang van de update.](./media/storsimple-8000-install-update-51/update7.png)

10. Nadat de taak is voltooid, gaat u naar de **apparaatinstellingen > updates** van het apparaat. De software versie moet nu worden bijgewerkt.


Controleer of de **StorSimple 8000 Series Update 5,1 (6.3.9600.17885)** op uw apparaat wordt uitgevoerd. De **laatst bijgewerkte datum** moet worden gewijzigd.
<!-- 5.1 - KB 4542887-->

<!--You will now see that the Maintenance mode updates are available (this message might continue to be displayed for up to 24 hours after you install the updates). The steps to install maintenance mode update are detailed in the next section.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## Install Update 5.1 as a hotfix

The software versions that can be upgraded using the hotfix method are:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4
* Update 5

> [!NOTE]
> The recommended method to install Update 5.1 is through the Azure portal when trying to update from Update 3 and later version. When updating a device running versions prior to Update 3, use this procedure. You can also use this procedure if you fail the gateway check when trying to install the updates through the Azure portal. The check fails when you have a gateway assigned to a non-DATA 0 network interface and your device is running a software version earlier than Update 1.

The hotfix method involves the following three steps:

1. Download the hotfixes from the Microsoft Update Catalog.
2. Install and verify the regular mode hotfixes.
3. Install and verify the maintenance mode hotfix.

#### Download updates for your device

You must download and install the following hotfixes in the prescribed order and the suggested folders:

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Software update<br> Download both _HcsSoftwareUpdate.exe_ and _CisMSDAgent.exe_ |Regular <br></br>Non-disruptive |~ 25 mins |FirstOrderUpdate|

If updating from a device running Update 4, you only need to install the OS cumulative updates as second order updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS cumulative updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|

If installing from a device running Update 3 or earlier, install the following in addition to the cumulative updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI driver and firmware updates <br> USM firmware update (version 3.38) |Regular <br></br>Non-disruptive |~ 3 hrs <br> (includes 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |OS security updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |OS updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|



You may also need to install disk firmware updates on top of all the updates shown in the preceding tables. You can verify whether you need the disk firmware updates by running the `Get-HcsFirmwareVersion` cmdlet. If you are running these firmware versions: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, then you do not need to install these updates.

| Order | KB | Description | Update type | Install time | Install in folder|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Disk firmware |Maintenance <br></br>Disruptive |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * If updating from Update 4, the total install time is close to 4 hours.
> * Before using this procedure to apply the update, make sure that both the device controllers are online and all the hardware components are healthy.

Perform the following steps to download and install the hotfixes.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]
-->
<!--
[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]
-->

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Update 5,1-release](storsimple-update51-release-notes.md).
