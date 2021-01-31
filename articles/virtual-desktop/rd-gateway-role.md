---
title: RD-gateway-functie implementeren virtueel bureau blad-Azure
description: Het implementeren van de RD-gateway rol in Windows virtueel bureau blad.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 71bd7d38727d99c05a15c54e5141c613960d9050
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220816"
---
# <a name="deploy-the-rd-gateway-role-in-windows-virtual-desktop-preview"></a>De functie RD-gateway implementeren in het virtuele bureau blad van Windows (preview-versie)

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als openbare preview-versie.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel wordt beschreven hoe u de RD-gateway rol (preview) gebruikt om Extern bureaublad-gateway-servers in uw omgeving te implementeren. U kunt de server functies op fysieke machines of virtuele machines installeren, afhankelijk van of u een on-premises, Cloud-of hybride omgeving maakt.

## <a name="install-the-rd-gateway-role"></a>De RD-gateway-functie installeren

1. Meld u aan bij de doel server met beheerders referenties.

2. Selecteer in **Serverbeheer** **beheren** en selecteer vervolgens **functies en onderdelen toevoegen**. Het installatie programma **functies en onderdelen toevoegen** wordt geopend.

3. In **voordat u begint**, selecteert u **volgende**.

4. Selecteer in **installatie type selecteren** de optie installatie die op de **functie of het onderdeel is gebaseerd** en selecteer vervolgens **volgende**.

5. Selecteer bij **doel server selecteren** **de optie een server uit de Server groep selecteren**. Voor **Server groep** selecteert u de naam van uw lokale computer. Selecteer **Volgende** als u klaar bent.

6. Selecteer in **Server functies selecteren** de  >  optie **extern bureaublad-services**. Selecteer **Volgende** als u klaar bent.

7. Selecteer in **extern bureaublad-services** **Volgende.**

8. Voor **Select Role Services** selecteert u alleen **extern bureaublad-gateway** wanneer u wordt gevraagd om vereiste onderdelen toe te voegen. Selecteer **onderdelen toevoegen**. Selecteer **Volgende** als u klaar bent.

9. Selecteer **volgende** voor **Services voor netwerk beleid en-toegang**.

10. Selecteer voor **webserver functie (IIS)** **volgende**.

11. Voor **Select Role Services** selecteert u **volgende**.

12. Selecteer **installeren** om de **installatie selecties te bevestigen**. Sluit het installatie programma niet tijdens het installatie proces.

## <a name="configure-rd-gateway-role"></a>RD-gateway rol configureren

Zodra de RD-gateway rol is geïnstalleerd, moet u deze configureren.

De RD-gateway-rol configureren:

1. Open de **Serverbeheer** en selecteer vervolgens **extern bureaublad-services**.

2. Ga naar **servers**, klik met de rechter muisknop op de naam van uw server en selecteer vervolgens **RD-gatewaybeheer**.

3. Klik in de RD-gatewaybeheer met de rechter muisknop op de naam van uw gateway en selecteer vervolgens **Eigenschappen**.

4. Open het tabblad **SSL-certificaat** , selecteer de optie **een certificaat importeren in de RD-gateway** bellen en selecteer vervolgens **Bladeren en certificaat importeren...**.

5. Selecteer de naam van uw PFX-bestand en selecteer vervolgens **openen**.

6. Voer het wacht woord voor het PFX-bestand in wanneer u hierom wordt gevraagd.

7. Nadat u het certificaat en de bijbehorende persoonlijke sleutel hebt geïmporteerd, moet de weer gave de sleutel kenmerken van het certificaat weer geven.

>[!NOTE]
>Omdat de RD-gateway rol openbaar moet zijn, raden we u aan een openbaar verleend certificaat te gebruiken. Als u een privé uitgegeven certificaat gebruikt, moet u ervoor zorgen dat alle clients met de vertrouwens keten van het certificaat vooraf worden geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

Als u maximale Beschik baarheid wilt toevoegen aan uw RD-gateway rol, raadpleegt u [hoge Beschik baarheid toevoegen aan de Webfront van RD Web en gateway](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha).