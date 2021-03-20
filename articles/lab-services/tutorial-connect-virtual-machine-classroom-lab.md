---
title: Toegang tot een leslokaallab in Azure Lab Services | Microsoft Docs
description: In deze zelfstudie hebt u toegang tot virtuele machines in een leslokaallab dat is ingesteld door een docent.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: dacfa34c0d3ab637ef513342bc5ce5fe81038e11
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85443465"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Zelfstudie: Toegang tot een leslokaallab in Azure Lab Services
In deze zelfstudie maakt u als student verbinding met een virtuele machine (VM) in een leslokaallab. 

In deze zelfstudie voert u de volgende acties uit:

> [!div class="checklist"]
> * Registreren voor het lab
> * De virtuele machine starten
> * Verbinding maken met de virtuele machine

## <a name="register-to-the-lab"></a>Registreren voor het lab

1. Navigeer naar de **registratie-URL** die u hebt ontvangen van de docent. U hoeft de registratie-URL niet meer te gebruiken nadat u de registratie hebt voltooid. In plaats daarvan gebruikt u deze URL: [https://labs.azure.com](https://labs.azure.com). Internet Explorer 11 wordt nog niet ondersteund. 

    ![Registreren voor het lab](./media/tutorial-connect-vm-in-classroom-lab/register-lab.png)
1. Meld u aan bij de service met uw schoolaccount om de registratie te voltooien. 

    > [!NOTE]
    > Een Microsoft-account is vereist voor het gebruik van Azure Lab Services. Als u uw niet-Microsoft-account zoals Yahoo of Google-accounts wilt gebruiken om u aan te melden bij de portal, volgt u de instructies voor het maken van een Microsoft-account dat wordt gekoppeld aan uw niet-Microsoft-account. Volg vervolgens de stappen om het registratieproces te voltooien. 
1. Controleer nadat u zich hebt geregistreerd of u de virtuele machines ziet voor het lab waartoe u toegang hebt. 

    ![Toegankelijke VM's](./media/tutorial-connect-vm-in-classroom-lab/accessible-vms.png)
1. Wacht totdat de virtuele machine gereed is. Let op de volgende velden op de VM-tegel:
    1. Boven aan de tegel ziet u de **naam van de testomgeving**.
    1. Aan de rechterkant ziet u het pictogram dat het **besturingssysteem (OS)** van de virtuele machine aangeeft. In dit voorbeeld is dit het Windows-besturingssysteem. 
    1. De voortgangsbalk op de tegel toont het aantal uren dat is gebruikt voor het aantal [quotumuren](how-to-configure-student-usage.md#set-quotas-for-users) die aan u zijn toegewezen. Deze tijd is de extra tijd die aan u is toegewezen, naast de geplande tijd voor het lab. 
    1. U ziet pictogrammen/knoppen onderaan de tegel om de virtuele machine te starten/stoppen en verbinding te maken met de virtuele machine. 
    1. Rechts van de knoppen ziet u de status van de virtuele machine. Controleer of de status van de virtuele machine **gestopt** is. 

        ![VM is gestopt](./media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>De virtuele machine starten
1. **Start** de VM door de eerste knop te selecteren, zoals in de volgende afbeelding. Dit proces duurt enige tijd.  

    ![De virtuele machine starten](./media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Controleer of de status van de virtuele machine **Uitvoeren** is. 

    ![VM wordt uitgevoerd](./media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    U ziet dat het pictogram van de eerste knop is gewijzigd om een **stop**-bewerking aan te geven. U kunt deze knop selecteren om de virtuele machine te stoppen. 

## <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

1. Selecteer de tweede knop zoals in de volgende afbeelding wordt weergegeven om **verbinding** te maken met de VM van het lab. 

    ![Verbinding maken met de virtuele machine](./media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Voer een van de volgende stappen uit: 
    1. Voor een virtuele **Windows**-machine, moet u het **RDP-bestand** opslaan op de harde schijf. Open het RDP-bestand om verbinding te maken met de virtuele machine. Gebruik de **gebruikersnaam** en het **wachtwoord** dat u van uw docent hebt gekregen om u aan te melden bij de computer. 
    3. Voor virtuele machines van **Linux** kunt u **SSH** of **RDP** (als deze is ingeschakeld) gebruiken om verbinding mee te maken. Zie voor meer informatie [verbinding met extern bureaublad inschakelen voor Linux-machines](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een leslokaallab geopend met behulp van de registratiekoppeling die u van uw docent krijgt.

Als eigenaar van een lab wilt u zien wie zich heeft geregistreerd bij uw lab en wilt u het gebruik van virtuele machines bijhouden. Ga verder met de volgende zelfstudie voor meer informatie over het bijhouden van het gebruik van het lab:

> [!div class="nextstepaction"]
> [Gebruik van een lab bijhouden](tutorial-track-usage.md) 
