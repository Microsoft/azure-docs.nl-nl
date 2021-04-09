---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 867cdc97ff91d5932230b733dee4d7660d499c39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026633"
---
#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>De minimale StorSimple-apparaatinstelling voltooien

   > [!NOTE]
   > U kunt de naam van het apparaat niet wijzigen nadat de minimale apparaatinstelling is voltooid.
   
1. Selecteer en klik op uw apparaat in de lijst in tabelvorm met apparaten op de blade **Apparaten**. Het apparaat heeft de status **Gereed voor configuratie**. De blade **Apparaat configureren** wordt geopend.

     ![Netwerkinterfaces voor de minimale StorSimple-apparaatinstelling](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig1.png)

2. Doe het volgende op de blade **Apparaat** configureren:
   
   1. Geef een **beschrijvende naam** voor het apparaat op. De standaardapparaatnaam bevat informatie zoals het model en serienummer van het apparaat. U kunt een beschrijvende naam van maximaal 64 tekens toewijzen om uw apparaat te beheren.
   2. Stel de **tijdzone** in op basis van de geografische regio waarin het apparaat wordt geïmplementeerd. Het apparaat gebruikt deze tijdzone gebruiken voor alle geplande bewerkingen.
   3. Onder de **DATA 0-INSTELLINGEN**:

       1. Uw DATA 0-netwerkinterface wordt weergegeven als ingeschakeld met de netwerkinstellingen (IP, subnet, gateway) geconfigureerd via de installatiewizard. DATA 0 wordt ook automatisch ingeschakeld voor de cloud, evenals voor iSCSI.

       2. Geef de vaste IP-adressen voor Controller 0 en Controller 1 op. **De vaste IP-adressen voor de controllers moeten beschikbare IP-adressen binnen het subnet zijn die toegankelijk zijn voor het IP-adres van het apparaat.** Als de DATA 0-interface is geconfigureerd voor IPv4, moeten de vaste IP-adressen worden opgegeven in de IPv4-indeling. Als u een voorvoegsel voor IPv6-configuratie hebt opgegeven, worden de vaste IP-adressen automatisch in deze velden ingevuld.

            ![Minimale installatie van StorSimple netwerk interfaces 2](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig2.png)

            De vaste IP-adressen voor de controller worden gebruikt voor het uitvoeren van de updates op het apparaat en voor garbagecollection. Daarom moeten de vaste IP-adressen routeerbaar zijn en verbinding kunnen maken met internet. U kunt controleren of uw vaste IP-adressen voor de controller routeerbaar zijn met de cmdlet [Test-HcsmConnection][Test]. In het volgende voorbeeld is te zien dat de vaste IP-adressen voor de controller worden doorgestuurd naar internet en dat deze toegang hebben tot de Microsoft Update-servers.

            ![Test-HcsmConnection met routeerbare IP-adressen](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig3.png)

1. Klik op **OK**. De apparaatconfiguratie wordt gestart. Wanneer de configuratie is voltooid, krijgt u een melding. De status van het apparaat verandert in **Online** op de blade **Apparaten**.

    ![StorSimple mini maal instellen netwerk interfaces van het apparaat 3](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig4.png)

<!--Link reference-->
[Test]: /previous-versions/windows/powershell-scripting/dn715782(v=wps.630)