---
title: Toegang tot een leslokaallab in Azure Lab Services | Microsoft Docs
description: Meer informatie over hoe u zich registreert bij een leslokaal Lab, alle Labs bekijken die u kunt openen, een virtuele machine in de test omgeving starten/stoppen en verbinding maken met de virtuele machine.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 6d5585082473a363df5f25f331475536544ae828
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433135"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Toegang tot een leslokaallab in Azure Lab Services
In dit artikel wordt beschreven hoe u zich registreert bij een leslokaal Lab, alle lessen bekijkt die u kunt openen, een virtuele machine in het Lab wilt starten/stoppen en verbinding kunt maken met de virtuele machine. 

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
    1. U ziet pictogrammen/knoppen onderaan de tegel om de virtuele machine te starten/stoppen en verbinding te maken met de virtuele machine. 
    1. Rechts van de knoppen ziet u de status van de virtuele machine. Controleer of de status van de virtuele machine **gestopt** is.

        ![VM is gestopt](./media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>De virtuele machine starten of stoppen
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
    1. Als u een **Mac** gebruikt om verbinding te maken met de VM van het lab, volgt u de instructies in de volgende sectie. 

## <a name="progress-bar"></a>Voortgangs balk 
De voortgangsbalk op de tegel toont het aantal uren dat is gebruikt voor het aantal [quotumuren](how-to-configure-student-usage.md#set-quotas-for-users) die aan u zijn toegewezen. Deze tijd is de extra tijd die aan u is toegewezen, naast de geplande tijd voor het lab. De kleur van de voortgangs balk en de tekst onder de voortgangs balk varieert per van de volgende scenario's:

- Als een klasse wordt uitgevoerd (binnen de planning van de klasse), wordt de voortgangs balk grijs weer gegeven om aan te geven dat de quota uren niet worden gebruikt. 

    ![Voortgangs balk in grijze kleur](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Als er geen quotum is toegewezen (nul uur), wordt de tekst die **beschikbaar is tijdens klassen alleen** weer gegeven in plaats van de voortgangs balk. 
    
    ![Status wanneer er geen quotum is ingesteld](./media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Als u **geen quotum** hebt, is de kleur van de voortgangs balk **rood**. 

    ![Voortgangs balk met rode kleur](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- De kleur van de voortgangs balk is **blauw** wanneer deze zich buiten het geplande tijdstip voor het lab bevindt en een deel van de quota tijd is gebruikt. 

    ![Voortgangs balk met blauwe kleur](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-labs"></a>Bekijk alle Labs
Nadat u zich bij de Labs hebt geregistreerd, kunt u alle Labs bekijken door de volgende stappen uit te voeren: 

1. Navigeer naar [https://labs.azure.com](https://labs.azure.com) . Internet Explorer 11 wordt nog niet ondersteund. 
2. Meld u aan bij de service met het gebruikersaccount waarmee u zich hebt geregistreerd voor het lab. 
3. Controleer of u alle labs ziet waarvoor u toegangsrechten hebt. 

    ![Alle labs weergeven](./media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Labaccounts maken en beheren als beheerder](how-to-manage-lab-accounts.md)
- [Labs maken en beheren als labeigenaar](how-to-manage-classroom-labs.md)
- [Sjablonen instellen en publiceren als labeigenaar](how-to-create-manage-template.md)
- [Het gebruik van een lab configureren en beheren als labeigenaar](how-to-configure-student-usage.md)
 