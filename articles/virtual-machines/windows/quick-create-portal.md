---
title: 'Quickstart: Een Windows-VM maken in de Azure-portal | Microsoft Docs'
description: In deze snelstartgids leert u hoe u de Azure Portal  gebruikt om een virtuele Windows-machine te maken
author: cynthn
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 46119b9f7cdac47920d4bba8e00c3fc56b0edc78
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "87494516"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Quickstart: Een virtuele Windows-machine maken in de Azure-portal

Virtuele Azure-machines (VM's) kunnen gemaakt worden via Azure Portal. Deze methode biedt een gebruikersinterface op basis van een browser voor het maken van VM's en van alle verwante resources. In deze quickstart wordt beschreven hoe u de Azure-portal gebruikt voor het implementeren van een virtuele machine (VM) in Azure waarop Windows Server 2019 wordt uitgevoerd. Om uw VM in actie te zien, voert u een externe bureaubladsessie voor de virtuele machine uit en installeert u de IIS-webserver.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-virtual-machine"></a>Virtuele machine maken

1. Typ **virtuele machines** in de zoekopdracht.
1. Selecteer **virtuele machines** onder **Services**.
1. Selecteer **Toevoegen** op de pagina **virtuele machines**. 
1. Zorg ervoor dat op het tabblad **Basics** onder **Projectgegevens** het juiste abonnement is geselecteerd, en kies **Nieuwe maken** om een nieuwe resourcegroep te maken. Typ *myResourceGroup* als de naam. 

    ![Schermopname van het gedeelte met projectgegevens waarin wordt aangegeven waar u het Azure-abonnement en de resourcegroep voor de virtuele machine selecteert](./media/quick-create-portal/project-details.png)

1. Typ onder **Instantiegegevens** *myVM* bij **Naam van virtuele machine** en kies *East US* bij **Regio**. Kies daarna *Windows Server 2019 Datacenter* bij **Installatiekopie**. Houd voor de rest de standaardinstellingen aan.

    ![Schermopname van het gedeelte Instantiegegevens waarin u een naam voor de virtuele machine opgeeft en de bijbehorende regio, installatiekopie en grootte selecteert](./media/quick-create-portal/instance-details.png)

1. Geeft onder **Administrator-account**, een gebruikersnaam op, bijvoorbeeld *azureuser*, en een wachtwoord. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Schermopname van het gedeelte Beheerdersaccount waarin u een gebruikersnaam en wachtwoord opgeeft voor de beheerder](./media/quick-create-portal/administrator-account.png)

1. Onder **Regels voor binnenkomende poort** kiest u **​​Geselecteerde poorten toestaan** en selecteert u **RDP (3389)** en **HTTP (80)** in de vervolgkeuzelijst.

    ![Schermopname van het gedeelte Regels voor binnenkomende poorten waarin u selecteert voor welke poorten binnenkomende verbindingen toegestaan zijn](./media/quick-create-portal/inbound-port-rules.png)

1. Laat de resterende standaardwaarden staan ​​en selecteer vervolgens de knop **Beoordelen en maken** aan de onderkant van de pagina.

    ![Schermopname met de knop Controleren en maken onderaan de pagina](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Verbinding maken met de virtuele machine

Maak via een extern bureaublad verbinding met de virtuele machine. Deze instructies geven aan hoe u vanaf een Windows-computer verbinding maakt met uw VM. Op een Mac hebt u een RDP-client nodig, zoals deze [Extern-bureaubladclient](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) uit de Mac App Store.

1. Selecteer de knop **Verbinden** op de overzichtspagina van uw virtuele machine. 

    ![Schermopname van de overzichtspagina van de virtuele machine met de locatie van de knop Verbinden](./media/quick-create-portal/portal-quick-start-9.png)
    
2. Laat op de pagina **Verbinding maken met virtuele machine** de standaardopties staan om via een IP-adres verbinding te maken via poort 3389 en klik op **RDP-bestand downloaden**.

2. Open het gedownloade RDP-bestand en klik op **Verbinden** wanneer dit wordt gevraagd. 

3. Selecteer in het venster **Windows-beveiliging** **Meer opties** en vervolgens **Een ander account gebruiken**. Typ de gebruikersnaam als **lokalehost**\\*gebruikersnaam*, voer het wachtwoord in dat u hebt gemaakt voor de virtuele machine, en klik vervolgens op **OK**.

4. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Klik op **Ja** of **Doorgaan** om de verbinding te maken.

## <a name="install-web-server"></a>Webserver installeren

Als u uw VM in actie wilt zien, installeert u de IIS-webserver. Open een PowerShell-prompt op de virtuele machine en voer de volgende opdracht uit:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Wanneer u klaar bent, sluit u de externe-bureaubladverbinding met de virtuele machine.


## <a name="view-the-iis-welcome-page"></a>De welkomstpagina van IIS weergeven

Selecteer de VM in de portal en gebruik in het overzicht van de VM de knop **Klik om te kopiëren** rechts van het IP-adres om het adres te kopiëren. Plak het adres vervolgens in een tabblad van de browser. De standaardwelkomstpagina van IIS wordt geopend en deze ziet er als volgt uit:

![Schermopname van de standaardsite van IIS in een browser](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de VM niet meer nodig hebt, kunt u de resourcegroep, de machine zelf én alle gerelateerde resources verwijderen. 

Selecteer de resourcegroep voor de virtuele machine en klik op **Verwijderen**. Bevestig de naam van de resourcegroep om het verwijderen van de resources te voltooien.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een eenvoudige virtuele machine geïmplementeerd, een netwerkpoort geopend voor internetverkeer en een eenvoudige webserver geïnstalleerd. Voor meer informatie over virtuele machines in Azure, gaat u verder met de zelfstudie voor virtuele Windows-machines.

> [!div class="nextstepaction"]
> [Zelfstudies over virtuele Windows-machines](./tutorial-manage-vm.md)
