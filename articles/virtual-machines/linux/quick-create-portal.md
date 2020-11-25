---
title: 'Snelstart: Een virtuele Linux-machine maken op de Azure-portal'
description: In deze snelstartgids leert u hoe u de Azure-portal gebruikt om een virtuele Linux-machine te maken.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/25/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 405cb107711845a6699e09bc09a1d2d9f3005cd6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016043"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Quickstart: Een virtuele Linux-machine maken in Azure-portal

Virtuele Azure-machines (VM's) kunnen gemaakt worden via Azure Portal. De Azure-portal is een gebruikersinterface op basis van een browser voor het maken van Azure-resources. In deze snelstart wordt beschreven hoe u de Azure-portal gebruikt voor het implementeren van een virtuele Linux-machine (VM) waarop Ubuntu 18.04 LTS wordt uitgevoerd. Wanner u uw virtuele machine in actie wilt zien, voert u ook SSH voor de virtuele machine uit en installeert u de NGINX-webserver.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com) als u dat nog niet hebt gedaan.

## <a name="create-virtual-machine"></a>Virtuele machine maken

1. Typ **virtuele machines** in de zoekopdracht.
1. Selecteer **virtuele machines** onder **Services**.
1. Selecteer **Toevoegen** op de pagina **virtuele machines**. De pagina **Een virtuele machine maken** wordt geopend.
1. Zorg ervoor dat op het tabblad **Basics** onder **Projectgegevens** het juiste abonnement is geselecteerd, en kies **Nieuwe maken** om een nieuwe resourcegroep te maken. Typ *myResourceGroup* als de naam.*. 

    ![Schermafbeelding van het gedeelte Projectgegevens waarin wordt weergegeven waar u het Azure-abonnement en de resourcegroep voor de virtuele machine selecteert](./media/quick-create-portal/project-details.png)

1. Typ onder **Exemplaardetails***myVM* als **Naam van de virtuele machine** en kies *VS - oost* als de **Regio** en kies *Ubuntu 18.04 LTS* voor uw **Installatiekopie**. Houd voor de rest de standaardinstellingen aan.

    ![Schermafbeelding van het gedeelte Instantiegegevens waarin u een naam voor de virtuele machine opgeeft en hiervoor de regio, installatiekopie en grootte selecteert](./media/quick-create-portal/instance-details.png)

1. Selecteer **Openbare SSH-sleutel** onder **Beheerdersaccount**.

1. Typ in **Gebruikersnaam** *azureuser*.

1. Voor **Openbare SSH-sleutel bron**, laat u de standaardwaarde van **Nieuwe sleutelpaar genereren** en typt u vervolgens *myKey* voor de **Naam van sleutelpaar**.

    ![Schermafbeelding van het gedeelte Beheerdersaccount waarin u een verificatietype selecteert en de beheerdersreferenties opgeeft](./media/quick-create-portal/administrator-account.png)

1. Onder **Regels voor binnenkomende poort** > **Openbare binnenkomende poorten**, kiest u **​​Geselecteerde poorten toestaan** en selecteert u vervolgens **SSH (22)** en  **HTTP (80)** in de vervolgkeuzelijst. 

    ![Schermafbeelding van het gedeelte Regels voor binnenkomende poorten waarin u selecteert voor welke poorten binnenkomende verbindingen toegestaan zijn](./media/quick-create-portal/inbound-port-rules.png)

1. Laat de resterende standaardwaarden staan ​​en selecteer vervolgens de knop **Beoordelen en maken** aan de onderkant van de pagina.

1. Op de pagina **Een virtuele machine maken** ziet u de details van de virtuele machine die u gaat maken. Wanneer u klaar bent, selecteert u **Maken**.

1. Wanneer het venster **Nieuw sleutelpaar genereren** wordt geopend, selecteert u **Persoonlijke sleutel downloaden en resource maken**. Het sleutelbestand wordt gedownload als **myKey.pem**. Zorg ervoor dat u weet waar het bestand `.pem` is gedownload. U hebt het pad in de volgende stap nodig.

1. Nadat de implementatie klaar is, selecteert u **Ga naar resource**.

1. Selecteer op de pagina voor de nieuwe virtuele machine het openbare IP-adres en kopieer het naar het klembord.


    ![Schermafbeelding waarin wordt getoond hoe u het IP-adres voor de virtuele machine kopieert](./media/quick-create-portal/ip-address.png)

## <a name="connect-to-virtual-machine"></a>Verbinding maken met de virtuele machine

Maak een SSH-verbinding met de VM.

1. Als u zich op een Mac-of Linux-computer bevindt, opent u een Bash-opdracht. Als u zich op een Windows-computer bevindt, opent u een PowerShell-opdracht. 

1. Open bij de opdracht een SSH-verbinding met uw virtuele machine. Vervang het IP-adres door het van uw virtuele machine en vervang het pad naar de `.pem` door het pad naar de locatie waar het sleutelbestand is gedownload.

```console
ssh -i .\Downloads\myKey1.pem azureuser@10.111.12.123
```

> [!TIP]
> De SSH-sleutel die u hebt gemaakt, kan de volgende keer dat u een virtuele machine in Azure maakt, worden gebruikt. Selecteer de **Een sleutel die is opgeslagen in Azure gebruiken** voor **SSH-bron met openbare sleutel** de volgende keer dat u een VM maakt. U hebt de persoonlijke sleutel al op uw computer geïnstalleerd, dus u hoeft niets te downloaden.

## <a name="install-web-server"></a>Webserver installeren

Als u uw VM in actie wilt zien, installeert u de NGINX-webserver. Werk vanuit uw SSH-sessie de pakketbronnen bij en installeer het meest recente NGINX-pakket.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Wanneer u klaar bent, typt u `exit` om de SSH-sessie te verlaten.


## <a name="view-the-web-server-in-action"></a>De webserver in actie zien

Gebruik een webbrowser naar keuze om de standaard NGINX-welkomstpagina weer te geven. Voer het openbare IP-adres van uw VM in als het webadres. Het openbare IP-adres kan worden gevonden op de overzichtspagina van de virtuele machine of als onderdeel van de SSH-verbindingsreeks die u eerder hebt gebruikt.

![Schermafbeelding waarin de NGINX-standaardsite in een browser wordt weergegeven](./media/quick-create-portal/nginx.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de VM niet meer nodig hebt, kunt u de resourcegroep, de machine zelf én alle gerelateerde resources verwijderen. Wanneer u dit wilt doen, selecteert u de resourcegroep voor de virtuele machine en selecteert u **Verwijderen**. Vervolgens bevestigt u de naam van de resourcegroep die u wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een eenvoudige virtuele machine geïmplementeerd, een netwerkbeveiligingsgroep en -regel gemaakt en een eenvoudige webserver geïnstalleerd. Voor meer informatie over virtuele machines in Azure, gaat u verder met de zelfstudie voor virtuele Linux-machines.

> [!div class="nextstepaction"]
> [Zelfstudies over virtuele Linux-machines](./tutorial-manage-vm.md)
