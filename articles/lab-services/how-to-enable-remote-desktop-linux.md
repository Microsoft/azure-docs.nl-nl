---
title: Grafische extern bureau blad inschakelen voor Linux in Azure Lab Services | Microsoft Docs
description: Meer informatie over het inschakelen van extern bureau blad voor virtuele Linux-machines in een lab in Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 34c940fec388bb0e79ab5e1db9be6d52fb223873
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94647950"
---
# <a name="enable-graphical-remote-desktop-for-linux-virtual-machines-in-azure-lab-services"></a>Grafische extern bureau blad inschakelen voor virtuele Linux-machines in Azure Lab Services
In dit artikel leest u hoe u de volgende taken kunt uitvoeren:

- Grafische extern bureau blad-sessies inschakelen voor een virtuele Linux-machine
- Verbinding maken met een virtuele Linux-machine met behulp van RDP (Remote Desktop Protocol) of X2Go extern bureau blad-clients

## <a name="set-up-graphical-remote-desktop-solution"></a>Grafische oplossing voor extern bureau blad instellen
Wanneer een lab wordt gemaakt op basis van een **Linux** -installatie kopie, wordt de **SSH** -toegang (Secure Shell) automatisch geconfigureerd zodat de docent via SSH verbinding kan maken met de sjabloon-VM vanaf de opdracht regel.  Op dezelfde manier kunnen studenten ook via SSH verbinding maken met hun virtuele machines wanneer de sjabloon-VM wordt gepubliceerd.

Als u verbinding wilt maken met een virtuele Linux-machine met een **GUI** (Graphical User Interface), raden wij u aan **RDP** of **X2Go** te gebruiken.  Bij beide opties moet de docent een aantal extra instellingen voor de VM van de sjabloon hebben:

### <a name="rdp-setup"></a>Setup van RDP
Als u RDP wilt gebruiken, moet de docent het volgende doen:
  - Verbinding met extern bureau blad inschakelen; Dit is specifiek vereist voor het openen van de poort van de virtuele machine voor RDP.
  - De RDP extern bureau blad-server installeren.
  - Installeer een Linux-grafische desktop omgeving (zoalsman, XFCE, enzovoort).

### <a name="x2go-setup"></a>X2Go-installatie
Voor het gebruik van X2Go moet de docent het volgende doen:
- Installeer de X2Go extern bureau blad-server.
- Installeer een Linux-grafische desktop omgeving (zoalsman, XFCE, enzovoort).

X2Go maakt gebruik van dezelfde poort die al is ingeschakeld voor SSH.  Als gevolg hiervan is er geen extra configuratie vereist voor het openen van een poort op de virtuele machine voor X2Go.

> [!NOTE]
> In sommige gevallen, zoals met Ubuntu LTS 18,04, levert X2Go betere prestaties.  Als u RDP gebruikt en u merkt dat er een latentie is bij interactie met de grafische desktop omgeving, kunt u overwegen X2Go te proberen, omdat dit de prestaties kan verbeteren.

> [!IMPORTANT]
>  Voor sommige Marketplace-installatie kopieën is al een grafische bureaublad omgeving en een extern bureau blad-server geïnstalleerd.  De [Data Science virtual machine voor Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) heeft bijvoorbeeld al [xfce en X2Go-server geïnstalleerd en geconfigureerd om client verbindingen te accepteren](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#x2go).

## <a name="enable-remote-desktop-connection-for-rdp"></a>Verbinding met extern bureau blad inschakelen voor RDP

Deze stap is alleen nodig om verbinding te maken met behulp van RDP.  Als u van plan bent X2Go te gebruiken, kunt u door gaan naar de volgende sectie, omdat X2Go de SSH-poort gebruikt.

1.  Tijdens het maken van het lab heeft de docent de mogelijkheid om **verbinding met extern bureaublad in te scha kelen**.  De docent moet deze optie **inschakelen** om de poort op de virtuele Linux-machine te openen die nodig is voor een externe RDP-bureaublad sessie.  Als deze optie niet is **ingeschakeld**, wordt alleen de poort voor SSH geopend.
  
    ![Scherm opname van het venster ' nieuw Lab ' met de optie ' Verbinding met extern bureaublad inschakelen '.](./media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

2. Selecteer in het vak **verbinding met extern bureaublad bericht inschakelen** de optie **door gaan met extern bureaublad**. 

    ![Verbinding met extern bureau blad inschakelen voor een Linux-installatie kopie](./media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

## <a name="install-rdp-or-x2go"></a>RDP of X2Go installeren

Nadat het lab is gemaakt, moet de docent ervoor zorgen dat er een grafische bureaublad omgeving en extern bureau blad-server op de sjabloon-VM zijn geïnstalleerd.  Docenten moeten eerst via SSH verbinding maken met de VM van de sjabloon om de pakketten te installeren voor:
- De RDP-of X2Go extern bureau blad-server.
- Een grafische desktop omgeving, zoalsman, XFCE, enzovoort.

Nadat dit is ingesteld, kan de docent verbinding maken met de VM van de sjabloon met behulp van de **Microsoft extern bureaublad RDP-** client of de **X2Go** -client.

Volg de onderstaande stappen om de sjabloon-VM in te stellen:

1. Als u de **sjabloon aanpassen** op de werk balk ziet, selecteert u deze. Selecteer vervolgens **door gaan** in het dialoog venster **sjabloon aanpassen** . Met deze actie wordt de VM van de sjabloon gestart.  

    ![Sjabloon aanpassen](./media/how-to-enable-remote-desktop-linux/customize-template.png)
1. Nadat de sjabloon van de virtuele machine is gestart, kunt u **verbinding maken sjabloon** selecteren en vervolgens **via SSH verbinding maken** op de werk balk. 

    ![Verbinding maken met sjabloon via RDP nadat het lab is gemaakt](./media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
1. U ziet het volgende: verbinding maken met het dialoog venster **virtuele machine** . Selecteer de knop **kopiëren** naast het tekstvak om deze naar het klem bord te kopiëren. Sla de gegevens van de SSH-verbinding op. Gebruik deze verbindings gegevens van een SSH-terminal (zoals [putty](https://www.putty.org/)) om verbinding te maken met de virtuele machine.
 
    ![SSH-connection string](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. U kunt RDP of X2Go installeren samen met de grafische desktop omgeving van uw keuze.  Raadpleeg de volgende instructies:
    - [RDP installeren en configureren](../virtual-machines/linux/use-remote-desktop.md)
    - [X2Go installeren en configureren](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop)

## <a name="connect-to-the-template-vm-via-the-gui"></a>Verbinding maken met de sjabloon-VM via de gebruikers interface

Nadat de sjabloon-VM is ingesteld, kan de docent via de gebruikers interface verbinding maken via de client van de **Microsoft extern bureaublad (RDP)** of de **X2Go** -client.  De client die u gebruikt, is afhankelijk van of RDP of X2Go is geconfigureerd als de externe bureau blad-server op de sjabloon-VM.  

### <a name="microsoft-remote-desktop-rdp-client"></a>Microsoft Extern bureaublad-client (RDP)

De Microsoft Extern bureaublad-client (RDP) wordt gebruikt om verbinding te maken met een sjabloon-VM waarvoor RDP is geconfigureerd.  De Extern bureaublad-client kan worden gebruikt in Windows, Chromebooks, Macs en meer.  Raadpleeg het artikel over [extern bureaublad-clients](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) voor meer informatie.

Volg de onderstaande stappen op basis van het type computer dat wordt gebruikt om verbinding te maken met de VM van de sjabloon:

- Windows
  1. Klik op **verbinding maken met sjabloon** op de werk balk van uw Lab en selecteer **verbinding maken via RDP** om verbinding te maken met de VM van de sjabloon. 
  1. Sla het RDP-bestand op en gebruik het om verbinding te maken met de sjabloon-VM met behulp van de Extern bureaublad-client. 
  1. Normaal gesp roken is de Extern bureaublad-client al geïnstalleerd en geconfigureerd in Windows.  Als gevolg hiervan hoeft u alleen op het RDP-bestand te klikken om het te openen en de externe sessie te starten.

- Mac
  1. Klik op **verbinding maken met sjabloon** op de werk balk van uw Lab en selecteer vervolgens **verbinding maken via RDP** om het RDP-bestand op te slaan.  
  1. Raadpleeg vervolgens de procedure voor het [maken van verbinding met een virtuele machine met RDP op een Mac](connect-virtual-machine-mac-remote-desktop.md).

- Chromebook
  1. Klik op **verbinding maken met sjabloon** op de werk balk van uw Lab en selecteer vervolgens **verbinding maken via RDP** om het RDP-bestand op te slaan.  
  1. Raadpleeg vervolgens de procedure voor het [maken van verbinding met een virtuele machine met behulp van RDP in een Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).

### <a name="x2go-client"></a>X2Go-client

De X2Go-client wordt gebruikt om verbinding te maken met een sjabloon-VM waarvoor X2Go is geconfigureerd.  Volg de stappen in het artikel How-to [to Connect to a virtual machine using X2Go om](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-x2go)de SSH-verbindings gegevens van de sjabloon VM te gebruiken.

## <a name="next-steps"></a>Volgende stappen
Nadat een docent RDP of X2Go op hun sjabloon-VM heeft ingesteld, kunnen studenten via de gebruikers interface van het externe bureau blad of SSH verbinding maken met hun virtuele machines.

Zie voor meer informatie:
 - [Verbinding maken met een Linux-VM](how-to-use-remote-desktop-linux-student.md)