---
title: Een Linux shell scripting Lab instellen met Azure Lab Services | Microsoft Docs
description: Meer informatie over het instellen van een Lab voor het leren van shell scripting op Linux.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ba14f2fb5263367014b57741c78d6e509df044b3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85444961"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Een Lab instellen om shell scripting op Linux te leren
Dit artikel laat u zien hoe u een lab kunt instellen om shell scripting op Linux te leren. Scripting is een nuttig onderdeel van systeem beheer waarmee beheerders terugkerende taken kunnen voor komen. In dit voorbeeld scenario bestrijkt de klasse traditionele bash-scripts en uitgebreide scripts. Uitgebreide scripts zijn scripts die bash-opdrachten en Ruby combi neren. Met deze benadering kan ruby gegevens rond en bash opdrachten door geven om met de shell te communiceren. 

Studenten die deze script klassen nemen, krijgen een virtuele Linux-machine voor het leren van de basis principes van Linux en kunnen ook vertrouwd raken met de bash-shell scripts. Voor de virtuele Linux-machine is toegang tot extern bureau blad ingeschakeld en met [gedit](https://help.gnome.org/users/gedit/stable/) en [Visual Studio code](https://code.visualstudio.com/) text editors geïnstalleerd.

## <a name="lab-configuration"></a>Lab-configuratie
Als u dit Lab wilt instellen, hebt u een Azure-abonnement nodig om aan de slag te gaan. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. Zodra u een Azure-abonnement hebt, kunt u een nieuw Lab-account maken in Azure Lab Services of een bestaand Lab-account gebruiken. Raadpleeg de volgende zelf studie voor het maken van een nieuw Lab-account: [zelf studie voor het instellen van een Lab-account](tutorial-setup-lab-account.md).

Nadat u het lab-account hebt gemaakt, schakelt u de volgende instellingen in het lab-account in: 

| Account instelling Lab | Instructies |
| ----------- | ------------ |  
| Marketplace-installatie kopieën | Schakel de Ubuntu Server 18,04 LTS-installatie kopie in voor gebruik binnen uw Lab-account. Zie [Marketplace-installatie kopieën die beschikbaar zijn voor Lab-makers opgeven](specify-marketplace-images.md)voor meer informatie. | 

Volg [deze zelf studie](tutorial-setup-classroom-lab.md) om een nieuw lab te maken en de volgende instellingen toe te passen:

| Lab-instellingen | Waarde/instructies | 
| ------------ | ------------------ |
| Grootte van virtuele machine (VM) | Klein  |
| VM-installatie kopie | Ubuntu Server 18.04 LTS|
| Verbinding met extern bureau blad inschakelen | Inschakelen. <p>Als u deze instelling inschakelt, kunnen docenten en studenten verbinding maken met hun virtuele machines met behulp van extern bureau blad (RDP). Zie voor meer informatie [extern bureau blad inschakelen voor virtuele Linux-machines in een lab in Azure Lab Services](how-to-enable-remote-desktop-linux.md). </p>|

## <a name="install-desktop-and-rdp"></a>Desktop en RDP installeren
Voor de Ubuntu Server 18,04 LTS-installatie kopie is de RDP-extern bureau blad-server niet standaard geïnstalleerd. Volg de instructies in de [extern bureaublad installeren en configureren om verbinding te maken met een virtuele Linux-machine in azure](../virtual-machines/linux/use-remote-desktop.md) om de pakketten te installeren die nodig zijn op de sjabloon computer om verbinding te maken via Remote Desktop Protocol (RDP).

## <a name="install-ruby"></a>Ruby installeren
Ruby is een open-source dynamische taal die kan worden gecombineerd met bash-scripts. In deze sectie wordt beschreven hoe u gebruikt `apt-get` om de meest recente versie van [ruby](https://www.ruby-lang.org/)te installeren.

1. Installeer updates door de volgende opdrachten uit te voeren:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Installeer [ruby](https://www.ruby-lang.org/).  Ruby is een open-source dynamische taal die kan worden gecombineerd met bash-scripts. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Ontwikkel hulpprogramma's installeren
In deze sectie wordt beschreven hoe u een aantal tekst editors installeert. Gedit is de standaard tekst editor voor de gnome-desktop omgeving. Het is ontworpen als een tekst editor voor algemeen gebruik. Visual Studio code is een tekst editor die ondersteuning biedt voor fout opsporing en broncode beheer integratie.

> [!NOTE]
> Er zijn verschillende tekst editors beschikbaar. Visual Studio code en gedit zijn slechts twee voor beelden.

1. Installeer [gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Installeer [Visual Studio Code](https://code.visualstudio.com/).  Visual Studio code kan worden geïnstalleerd met behulp van de snap Store.  Zie [Visual Studio code alternatieve down loads](https://code.visualstudio.com/#alt-downloads)(Engelstalig) voor alternatieve installatie opties.

    ```bash
    sudo snap install vscode --classic 
    ```

    De sjabloon wordt nu bijgewerkt en heeft zowel de programmeer taal als de ontwikkel hulpprogramma's die nodig zijn om het lab te volt ooien. De sjabloon afbeelding kan nu worden gepubliceerd naar het lab. Selecteer de knop **publiceren** op de sjabloon pagina om de sjabloon te publiceren naar het lab.  

## <a name="cost"></a>Kosten 
Als u de kosten van dit Lab wilt schatten, kunt u het volgende voor beeld gebruiken:
 
Voor een klasse van 25 studenten met 20 uur geplande en tien uur aan quota voor huis werk of-toewijzingen is de prijs voor het Lab: 

25 studenten * (20 + 10) uur * 20 Lab-eenheden * 0,01 USD per uur = 150 USD

Meer informatie over de prijzen vindt u in het volgende document: [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusie
In dit artikel werd uitgelegd hoe u stapsgewijs door de stappen voor het maken van een Lab voor script klassen. Hoewel dit artikel is gericht op het instellen van ruby-script hulpprogramma's op Linux-machines, kan dezelfde configuratie worden gebruikt voor andere script klassen zoals python op Linux.

## <a name="next-steps"></a>Volgende stappen
De volgende stappen zijn gebruikelijk voor het instellen van elk lab:

- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail registratie koppelingen naar studenten](how-to-configure-student-usage.md#send-invitations-to-users). 





