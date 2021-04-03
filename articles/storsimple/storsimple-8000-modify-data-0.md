---
title: DATA 0-instellingen wijzigen op StorSimple 8000 Series-apparaat | Microsoft Docs
description: Meer informatie over het gebruik van Windows PowerShell voor StorSimple om de DATA 0-netwerk interface opnieuw te configureren op uw StorSimple-apparaat.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 85d7114f419266124d0d23368b24700af025758a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961036"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>De DATA 0-netwerk interface-instellingen op uw StorSimple 8000 Series-apparaat wijzigen

## <a name="overview"></a>Overzicht

Uw Microsoft Azure StorSimple-apparaat heeft zes netwerk interfaces, van gegevens 0 tot en met DATA 5. De DATA 0-interface wordt altijd geconfigureerd via de Windows Power shell-interface of de seriële console en wordt automatisch ingeschakeld voor de Cloud. Houd er rekening mee dat u de DATA 0-netwerk interface niet kunt configureren via de Azure Portal.

De DATA 0-interface wordt eerst geconfigureerd via de wizard Setup tijdens de eerste implementatie van het StorSimple-apparaat. Wanneer het apparaat zich in de operationele modus bevindt, moet u de DATA 0-instellingen mogelijk opnieuw configureren. Deze zelf studie biedt twee methoden voor het wijzigen van gegevens 0 netwerk instellingen, zowel via Windows PowerShell voor StorSimple.

Na het lezen van deze zelf studie kunt u het volgende doen:

* DATA 0-netwerk instelling wijzigen via de installatie wizard
* DATA 0-netwerk instellingen wijzigen via de `Set-HcsNetInterface` cmdlet

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>DATA 0-netwerk instellingen wijzigen via de wizard Setup
U kunt de gegevens van de netwerk instellingen van 0 opnieuw configureren door verbinding te maken met de Windows Power shell-interface van uw StorSimple-apparaat en een installatie wizard-sessie te starten. Voer de volgende stappen uit om de DATA 0-instellingen te wijzigen:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>DATA 0 netwerk instellingen wijzigen via de wizard Setup
1. Kies in het menu van de seriële console optie 1, **Meld u aan met volledige toegang**. Geef het **beheerders wachtwoord** voor het apparaat op wanneer u hierom wordt gevraagd. Het standaard wachtwoord is `Password1` .
2. Typ in de opdrachtprompt:
   
    `Invoke-HcsSetupWizard`
3. De wizard Setup wordt weer gegeven om de DATA 0-interface van uw apparaat te configureren. Geef nieuwe waarden op voor het IP-adres, de gateway en het netmasker.

> [!NOTE]
> De IP-adressen van de vaste controllers moeten opnieuw worden geconfigureerd via de Blade **netwerk instellingen** van het StorSimple-apparaat in de Azure Portal. Ga naar [netwerk interfaces aanpassen](storsimple-8000-modify-device-config.md#modify-network-interfaces)voor meer informatie.

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>DATA 0 Network Settings wijzigen via Set-HcsNetInterface-cmdlet
Een alternatieve manier om de DATA 0-netwerk interface opnieuw te configureren, is door het gebruik van de `Set-HcsNetInterface` cmdlet. De cmdlet wordt uitgevoerd vanuit de Windows Power shell-interface van uw StorSimple-apparaat. Wanneer u deze procedure gebruikt, kunnen de vaste IP-adressen van de controller hier ook worden geconfigureerd. Voer de volgende stappen uit om de DATA 0-instellingen te wijzigen: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>DATA 0 Network Settings wijzigen via de cmdlet Set-HcsNetInterface
1. Kies in het menu van de seriële console optie 1, **Meld u aan met volledige toegang**. Geef het beheerders wachtwoord voor het apparaat op wanneer u hierom wordt gevraagd. Het standaard wachtwoord is `Password1` .
2. Typ in de opdrachtprompt:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    Typ in de vier Kante haken de volgende waarden voor de gegevens 0:
   
   * IPv4-adres
   * IPv4-Gateway
   * IPv4-subnetmasker
   * Vast IPv4-adres voor controller 0
   * Vast IPv4-adres voor controller 1
     
     Ga naar [Windows PowerShell voor StorSimple-cmdlet-verwijzing](/previous-versions/windows/powershell-scripting/dn688161(v=wps.630))voor meer informatie over het gebruik van deze cmdlet.

## <a name="next-steps"></a>Volgende stappen
* Als u andere netwerk interfaces dan DATA 0 wilt configureren, kunt u de [netwerk instellingen configureren in de Azure Portal](storsimple-8000-modify-device-config.md). 
* Als u problemen ondervindt bij het configureren van uw netwerk interfaces, raadpleegt u [implementatie problemen oplossen](./storsimple-8000-troubleshoot-deployment.md).