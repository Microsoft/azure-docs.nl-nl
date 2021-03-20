---
title: Verbinding maken met een virtuele Linux-machine in Azure Lab Services | Microsoft Docs
description: Meer informatie over het gebruik van extern bureau blad voor virtuele Linux-machines in een lab in Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d13868477ff2e3378d87d7785789a7498ed17e59
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85443414"
---
# <a name="connect-to-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Verbinding maken met virtuele Linux-machines in een leslokaal Lab van Azure Lab Services
In dit artikel wordt beschreven hoe studenten in een Lab verbinding kunnen maken met een virtuele Linux-machine (VM) met behulp van:
- SSH-terminal (Secure Shell-Protocol)
- GUI (Graphical User Interface) extern bureau blad

> [!IMPORTANT] 
> SSH wordt automatisch geconfigureerd zodat zowel studenten als docent kunnen SSH-Vm's in virtuele Linux-machines zonder extra configuratie. Als studenten echter verbinding moeten maken met behulp van een GUI extern bureau blad, moet de docent mogelijk extra Setup uitvoeren.  Zie [extern bureau blad inschakelen voor virtuele Linux-machines](how-to-enable-remote-desktop-linux.md)voor meer informatie.

## <a name="connect-to-the-student-vm-using-ssh"></a>Verbinding maken met de student-VM met SSH

1. Wanneer een student zich rechtstreeks aanmeldt bij de Labs-Portal ( `https://labs.azure.com` ) of door een registratie koppeling () te gebruiken `https://labs.azure.com/register/<registrationCode>` , wordt er een tegel voor elk lab waartoe de student toegang heeft, weer gegeven. 
   
1. Schakel op de tegel de knop in om de virtuele machine te starten als deze de status gestopt heeft. 

2. Selecteer **Verbinding maken**. U ziet twee opties om verbinding te maken met de VM: **SSH** en **RDP**.

    ![VM van student-verbindings opties](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

3. Selecteer de **SSH** -optie en u ziet het dialoog venster **verbinding maken met de virtuele machine** :  

    ![SSH-connection string](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Klik op de knop **kopiëren** naast het tekstvak om de gegevens van de SSH-verbinding naar het klem bord te kopiëren. 

5. Sla de gegevens van de SSH-verbinding, zoals in het tekst paneel, op zodat u deze verbindings gegevens in de volgende stap kunt gebruiken.

6. Maak verbinding met uw virtuele machine via een SSH-terminal (zoals [putty](https://www.putty.org/)).

## <a name="connect-to-the-student-vm-using-gui-remote-desktop"></a>Verbinding maken met de student-VM met behulp van GUI extern bureau blad
De docent kan ervoor kiezen om Vm's te configureren zodat studenten ook verbinding kunnen maken met behulp van een GUI extern bureau blad.  In dit geval moeten studenten zich afvragen van hun docent, ongeacht of ze verbinding willen maken met hun virtuele machines met behulp van de **Microsoft extern bureaublad (RDP)** of de **X2Go** -client toepassing.  Met beide toepassingen kan een student extern verbinding maken met hun VM en het grafische Linux-bureau blad op de lokale computer weer geven.

### <a name="connect-to-the-student-vm-using-microsoft-remote-desktop-rdp"></a>Verbinding maken met de student-VM met behulp van Microsoft Extern bureaublad (RDP)
Studenten kunnen Microsoft Extern bureaublad (RDP) gebruiken om verbinding te maken met hun Linux-Vm's nadat hun docent hun Lab heeft ingesteld met RDP-en GUI-pakketten voor een Linux-grafische desktop omgeving (zoalsman, XFCE, enzovoort). Hier volgen de stappen om verbinding te maken: 

1. Controleer op de tegel voor uw virtuele machine of de virtuele machine wordt uitgevoerd en klik op **verbinding maken**. U ziet twee opties om verbinding te maken met de VM: **SSH** en **RDP**.

    ![VM van student-verbindings opties](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)
2. Selecteer de optie **RDP** .  Wanneer het RDP-bestand op uw computer wordt gedownload, slaat u het op uw virtuele machine op.

3. Als u verbinding maakt vanaf een Windows-computer, is de Microsoft Extern bureaublad-client (RDP) meestal al geïnstalleerd en geconfigureerd.  Als gevolg hiervan hoeft u alleen op het RDP-bestand te klikken om het te openen en de externe sessie te starten.

    In plaats daarvan raadpleegt u de volgende stappen als u verbinding maakt vanuit een Mac-of Chromebook:
   - [Maak verbinding met een virtuele machine met behulp van RDP op een Mac](connect-virtual-machine-mac-remote-desktop.md).
   - [Maak verbinding met een virtuele machine met behulp van RDP in een Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).  

### <a name="connect-to-the-student-vm-using-x2go"></a>Verbinding maken met de student-VM met behulp van X2Go
Studenten kunnen X2Go gebruiken om verbinding te maken met hun Linux-Vm's nadat hun docent hun Lab heeft ingesteld met X2Go en de GUI-pakketten voor een Linux-grafische desktop omgeving (zoalsman, XFCE, enzovoort).

Studenten moeten erachter komen uit hun docent welke Linux grafische desktop omgeving hun docent heeft geïnstalleerd.  Deze informatie is nodig in de volgende stappen om verbinding te maken met behulp van de X2Go-client.

1. Installeer de [X2Go-client](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) op uw lokale computer.

1. Volg de instructies in de [eerste sectie](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh) om de SSH-verbindings gegevens voor uw virtuele machine te kopiëren.  U hebt deze informatie nodig om verbinding te maken met behulp van de X2Go-client.

1. Zodra u de gegevens van de SSH-verbinding hebt, opent u de X2Go-client en selecteert u **sessie**  >  **nieuwe** sessie.
   ![X2Go nieuwe sessie maken](./media/how-to-use-classroom-lab/x2go-new-session.png)

1. Voer de waarden in het deel venster **sessie voorkeuren** in op basis van uw SSH-verbindings gegevens.  Zo ziet uw verbindings informatie er ongeveer als volgt uit:

    ```bash
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
    ```

    In dit voor beeld worden de volgende waarden ingevoerd:

   - **Sessie naam** : Geef een naam op, bijvoorbeeld de naam van de virtuele machine.
   - **Host** : de id van uw virtuele machine; bijvoorbeeld **`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`** .
   - **Aanmelden** : de gebruikers naam voor uw virtuele machine; bijvoorbeeld **student**.
   - **SSH-poort** : de unieke poort die aan uw VM is toegewezen; bijvoorbeeld **12345**.
   - **Sessie type** : Selecteer de Linux-grafische desktop omgeving waarin uw docent uw VM heeft geconfigureerd.  U moet deze informatie van uw docent ophalen.

    Klik ten slotte op **OK** om de sessie te maken.

    ![X2Go-sessie voorkeuren](./media/how-to-use-classroom-lab/x2go-session-preferences.png)

1.  Klik op uw sessie in het rechterdeel venster.

    ![X2Go start nieuwe sessie](./media/how-to-use-classroom-lab/x2go-start-session.png)

    > [!NOTE] 
    > Als u wordt gevraagd om een soortgelijk bericht, selecteert u **Ja** om door te gaan met het invoeren van uw wacht woord: **de authenticiteit van de host [ `00000000-0000-0000-0000-000000000000.eastus2.cloudapp.eastus.cloudapp.azure.com` ]: 12345 kan niet tot stand worden gebracht.  ECDSA Key-vinger afdruk is SHA256:00000000000000000000000000000000000000000000. weet u zeker dat u wilt door gaan met verbinding maken (Ja/Nee)?**

2. Wanneer u hierom wordt gevraagd, voert u uw wacht woord in en klikt u op **OK**.  U kunt nu extern verbinding maken met de GUI-bureaublad omgeving van uw VM.

## <a name="next-steps"></a>Volgende stappen
Zie [extern bureau blad inschakelen voor virtuele Linux-machines](how-to-enable-remote-desktop-linux.md)voor meer informatie over het inschakelen van de functie verbinding met extern bureau blad voor Linux vm's in een leslokaal Lab. 

