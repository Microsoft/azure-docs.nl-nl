---
title: Instellingen voor automatisch starten configureren voor een virtuele machine in Azure DevTest Labs | Microsoft Docs
description: Meer informatie over het configureren van instellingen voor automatisch starten van Vm's in een lab. Met deze instelling kunnen Vm's in het lab automatisch volgens een schema worden gestart.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 83e7b0836273a59eaaf66471bd0cb42d63ccf1c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91328476"
---
# <a name="auto-startup-lab-virtual-machines"></a>Virtuele machines van Lab automatisch opstarten  
Met Azure DevTest Labs kunt u virtuele machines in uw Lab zo configureren dat deze automatisch worden gestart en afgesloten op basis van een schema. Voor informatie over het configureren van instellingen voor automatisch afsluiten, Zie [beleid voor automatisch afsluiten beheren voor een lab in azure DevTest Labs](devtest-lab-auto-shutdown.md). 

In tegens telling tot automatisch afsluiten, waarbij alle virtuele machines worden opgenomen wanneer het beleid is ingeschakeld, vereist het beleid voor automatisch starten dat een test gebruiker een virtuele machine expliciet selecteert en zich aanmeldt bij deze planning. Op die manier kunt u niet eenvoudig worden uitgevoerd in de situatie waarin ongewenste Vm's per ongeluk automatisch worden gestart en onverwachte uitgaven veroorzaken.

In dit artikel leest u hoe u het beleid voor automatisch starten configureert voor een lab.

## <a name="configure-autostart-settings-for-a-lab"></a>Instellingen voor automatisch starten configureren voor een Lab 
1. Ga naar de start pagina voor uw Lab. 
2. Selecteer **configuratie en beleid** in het menu links. 

    ![Scherm opname van het menu ' configuratie en beleid ' in het DevTest Lab.](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. Voer de volgende stappen uit op de pagina **configuratie en beleid** :
    
    1. Selecteer **aan** **Als u wilt dat virtuele machines kunnen worden gepland om automatisch te worden gestart** om de functie Auto start voor dit lab in te scha kelen. 
    2. Selecteer een begin tijd (bijvoorbeeld: 8:00:00 uur) voor het veld **begin** van de planning. 
    3. Selecteer een **tijd zone** die u wilt gebruiken. 
    4. Selecteer **de dagen van de week** waarop de vm's automatisch moeten worden gestart. 
    5. Selecteer vervolgens **Opslaan** op de werk balk om de instellingen op te slaan. 

        ![Instellingen voor automatisch starten](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Met dit beleid wordt automatisch starten niet automatisch toegepast op virtuele machines in het lab. Als u zich wilt **Aanmelden** voor afzonderlijke virtuele machines, gaat u naar de pagina virtuele machine en schakelt u **automatisch starten** voor die vm in.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>Automatisch starten inschakelen voor een virtuele machine in het lab
De volgende procedure bevat stappen voor het aanbrengen van een virtuele machine in het auto start-beleid van het lab. 

1. Selecteer de virtuele **machine** in de lijst **mijn virtuele machines** op de start pagina van uw test omgeving. 

    ![Menu configuratie en beleid](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. Selecteer op de pagina **virtuele machine** **automatisch starten** in het menu links of in de lijst **schema's** . 

    ![Menu automatisch starten selecteren](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. Selecteer op de pagina automatisch **starten** de optie **aan** om **te plannen dat deze virtuele machine kan worden gepland voor automatische start** .

    ![Automatisch starten voor de virtuele machine inschakelen](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. Selecteer vervolgens **Opslaan** op de werk balk om de instelling op te slaan. 


## <a name="next-steps"></a>Volgende stappen
Zie [beleid voor automatisch afsluiten beheren voor een lab in azure DevTest Labs](devtest-lab-auto-shutdown.md) voor meer informatie over het beleid voor automatisch afsluiten van de configuratie van een lab.
