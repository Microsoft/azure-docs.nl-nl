---
title: Problemen met een geïmplementeerd StorSimple-apparaat oplossen | Microsoft Docs
description: Hierin wordt beschreven hoe u fouten opspoort en corrigeert die optreden op een StorSimple-apparaat dat momenteel is geïmplementeerd en operationeel is.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 6cd1d981737db1e7c852931ecc2449e0afc03530
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956786"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Problemen met een operationeel StorSimple-apparaat oplossen
> [!NOTE]
> De klassieke portal voor StorSimple is afgeschaft. De StorSimple-apparaatbeheerfuncties worden automatisch volgens schema verplaatst naar de nieuwe Azure Portal. U ontvangt hierover een e-mailbericht en een portalmelding. Dit document wordt binnenkort ook ingetrokken. Zie [FAQ: Move to Azure portal](./index.yml) (Veelgestelde vragen: verplaatsing naar Azure Portal) voor vragen met betrekking tot de verplaatsing.

## <a name="overview"></a>Overzicht
In dit artikel vindt u nuttige richt lijnen voor probleem oplossing voor het oplossen van configuratie problemen die zich kunnen voordoen nadat het StorSimple-apparaat is geïmplementeerd en operationeel is. Hierin worden veelvoorkomende problemen, mogelijke oorzaken en aanbevolen stappen beschreven om u te helpen bij het oplossen van problemen die kunnen optreden wanneer u Microsoft Azure StorSimple uitvoert. Deze informatie is van toepassing op zowel het StorSimple on-premises fysieke apparaat als het virtuele StorSimple-apparaat.

Aan het einde van dit artikel vindt u een lijst met fout codes die u tijdens Microsoft Azure StorSimple bewerking kunt tegen komen, evenals de stappen die u kunt ondernemen om de fouten op te lossen. 

## <a name="setup-wizard-process-for-operational-devices"></a>Installatie wizard-proces voor operationele apparaten
U gebruikt de wizard Setup ([invoke-hcssetupwizard uit][1]) om de apparaatconfiguratie te controleren en zo nodig corrigerende maat regelen te nemen.

Wanneer u de installatie wizard uitvoert op een eerder geconfigureerd en operationeel apparaat, is de proces stroom anders. U kunt alleen de volgende vermeldingen wijzigen:

* IP-adres, subnetmasker en gateway
* Primaire DNS-server
* Primaire NTP-server
* Optionele web proxy-configuratie

De wizard Setup voert geen bewerkingen uit die betrekking hebben op wachtwoord verzameling en apparaatregistratie.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Fouten die optreden tijdens volgende uitvoeringen van de wizard Setup
In de volgende tabel worden de fouten beschreven die u kunt tegen komen wanneer u de installatie wizard uitvoert op een operationeel apparaat, mogelijke oorzaken van fouten en aanbevolen acties om deze op te lossen. 

| Nee. | Fout bericht of voor waarde | Mogelijke oorzaken | Aanbevolen actie |
|:--- |:--- |:--- |:--- |
| 1 |Fout 350032: dit apparaat is al gedeactiveerd. |Deze fout wordt weer geven als u de installatie wizard uitvoert op een apparaat dat wordt gedeactiveerd. |[Neem contact op met Microsoft ondersteuning](./storsimple-8000-contact-microsoft-support.md) voor de volgende stappen. Een gedeactiveerd apparaat kan niet in de service worden geplaatst. Het is mogelijk dat de fabrieks instellingen opnieuw worden ingesteld voordat het apparaat weer kan worden geactiveerd. |
| 2 |Invoke-HcsSetupWizard: ERROR_INVALID_FUNCTION (uitzonde ring van HRESULT: 0x80070001) |De update van de DNS-server is mislukt. DNS-instellingen zijn algemene instellingen en worden toegepast op alle ingeschakelde netwerk interfaces. |Schakel de interface in en pas de DNS-instellingen opnieuw toe. Hierdoor kan het netwerk voor andere ingeschakelde interfaces worden verstoord, omdat deze instellingen globaal zijn. |
| 3 |Het apparaat lijkt online te zijn in de StorSimple Manager-service portal, maar wanneer u de minimale installatie wilt volt ooien en de configuratie opslaat, mislukt de bewerking. |Tijdens de eerste installatie is de webproxy niet geconfigureerd, ook al is er een daad werkelijke proxy server aanwezig. |Gebruik de [cmdlet test-HcsmConnection][2] om de fout te vinden. [Neem contact op met Microsoft ondersteuning](./storsimple-8000-contact-microsoft-support.md) als u het probleem niet kunt oplossen. |
| 4 |Invoke-Hcssetupwizard uit: de waarde valt niet binnen het verwachte bereik. |Deze fout treedt op bij een onjuist subnetmasker. Mogelijke oorzaken zijn: <ul><li> Het subnetmasker ontbreekt of is leeg.</li><li>De IPv6-voorvoegsel indeling is onjuist.</li><li>De interface is in de Cloud ingeschakeld, maar de gateway ontbreekt of is onjuist.</li></ul>Houd er rekening mee dat DATA 0 automatisch in de Cloud wordt ingeschakeld als dit is geconfigureerd via de wizard Setup. |Om het probleem te achterhalen, gebruikt u subnet 0.0.0.0 of 256.256.256.256 en kijkt u vervolgens naar de uitvoer. Voer indien nodig de juiste waarden voor het subnetmasker, de gateway en het IPv6-voor voegsel in. |

## <a name="error-codes"></a>Foutcodes
Fouten worden weer gegeven in numerieke volg orde.

| Fout nummer | Fout tekst of beschrijving | Aanbevolen gebruikers actie |
|:--- |:--- |:--- |
| 10502 |Er is een fout opgetreden tijdens het openen van uw opslag account. |Wacht een paar minuten en probeer het opnieuw. Als de fout zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning om de volgende stappen uit te voeren. |
| 40017 |De back-upbewerking is mislukt omdat een volume dat is opgegeven in het back-upbeleid, niet op het apparaat is gevonden. |Voer de back-upbewerking opnieuw uit. Neem contact op met Microsoft Ondersteuning als de fout zich blijft voordoen. voor de volgende stappen. |
| 40018 |De back-upbewerking is mislukt omdat geen van de volumes die zijn opgegeven in het back-upbeleid op het apparaat zijn gevonden. |Voer de back-upbewerking opnieuw uit. Neem contact op met Microsoft Ondersteuning als de fout zich blijft voordoen. voor de volgende stappen. |
| 390061 |Het systeem is bezet of niet beschikbaar. |Wacht een paar minuten en probeer het opnieuw. Als de fout zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning om de volgende stappen uit te voeren. |
| 390143 |Er is een fout opgetreden met fout code 390143. (Onbekende fout.) |Als de fout zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning om de volgende stappen uit te voeren. |

## <a name="next-steps"></a>Volgende stappen
Als u het probleem niet kunt oplossen, [neemt u contact op met Microsoft ondersteuning](./storsimple-8000-contact-microsoft-support.md) voor hulp. 

[1]: /previous-versions/windows/powershell-scripting/dn688135(v=wps.630)
[2]: /previous-versions/windows/powershell-scripting/dn715782(v=wps.630)