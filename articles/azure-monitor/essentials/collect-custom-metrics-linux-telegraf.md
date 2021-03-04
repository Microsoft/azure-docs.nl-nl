---
title: Aangepaste metrische gegevens voor Linux-VM verzamelen met de InfluxData-Telegraf-agent
description: Instructies voor het implementeren van de InfluxData-telegrafie-agent op een virtuele Linux-machine in Azure en het configureren van de agent voor het publiceren van metrische gegevens naar Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.openlocfilehash: 204124240c6831ebb2c1df436736f475c48d98a8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048933"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Aangepaste metrische gegevens verzamelen voor een virtuele Linux-machine met de InfluxData-Telegraf-agent

U kunt met behulp van Azure Monitor aangepaste metrische gegevens verzamelen via de telemetrie van uw toepassing, een agent die wordt uitgevoerd op uw Azure-resources of zelfs buiten-de bewakings systemen. Vervolgens kunt u ze rechtstreeks naar Azure Monitor verzenden. In dit artikel vindt u instructies voor het implementeren van de [InfluxData](https://www.influxdata.com/) -telegrafie-agent op een virtuele Linux-machine in Azure en het configureren van de agent voor het publiceren van metrische gegevens naar Azure monitor. 

## <a name="influxdata-telegraf-agent"></a>InfluxData-Telegraf-agent 

[Telegrafie](https://docs.influxdata.com/telegraf/) is een door de toepassing aangedreven agent die het verzamelen van metrische gegevens van meer dan 150 verschillende bronnen mogelijk maakt. Afhankelijk van de werk belastingen die op uw virtuele machine worden uitgevoerd, kunt u de agent configureren voor het gebruik van gespecialiseerde invoer invoeg toepassingen voor het verzamelen van metrische gegevens. Voor beelden zijn MySQL, NGINX en Apache. Met behulp van uitvoer invoeg toepassingen kan de agent vervolgens naar bestemmingen schrijven die u kiest. De telegrafie agent heeft rechtstreeks geïntegreerd met de Azure Monitor aangepaste metrische gegevens REST API. Het ondersteunt een Azure Monitor-uitvoer-invoeg toepassing. Door deze invoeg toepassing te gebruiken, kan de agent specifieke metrische gegevens van uw Linux-machine verzamelen en als aangepaste metrische gegevens naar Azure Monitor verzenden. 

 ![Overzicht van de Telegraaf agent](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

> [!NOTE]  
> Aangepaste metrische gegevens worden niet ondersteund in alle regio's. [Hier](./metrics-custom-overview.md#supported-regions) worden ondersteunde regio's vermeld

## <a name="send-custom-metrics"></a>Aangepaste metrische gegevens verzenden 

Voor deze zelf studie implementeren we een virtuele Linux-machine waarop het Ubuntu 16,04 LTS-besturings systeem wordt uitgevoerd. De telegrafie-agent wordt ondersteund voor de meeste Linux-besturings systemen. Zowel de Debian-als de RPM-pakketten zijn beschikbaar in combi natie met niet-verpakkende Linux-bestanden op de [InfluxData-Download Portal](https://portal.influxdata.com/downloads). Zie deze [telegrafi-installatie handleiding](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) voor aanvullende installatie-instructies en-opties. 

Meld u aan bij [Azure Portal](https://portal.azure.com).

> [!NOTE]  
> Als u de klassieke waarschuwings regels wilt migreren en een bestaande virtuele Linux-machine wilt gebruiken, moet u ervoor zorgen dat aan de virtueel-machine een **door** het systeem toegewezen identiteit is ingesteld op on.

Een nieuwe virtuele Linux-machine maken: 

1. Selecteer de optie **een resource maken** in het navigatie deel venster aan de linkerkant. 
1. Zoek naar de **virtuele machine**.  
1. Selecteer **Ubuntu 16,04 LTS** en selecteer **maken**. 
1. Geef een VM-naam op, bijvoorbeeld **MyTelegrafVM**.  
1. Zorg ervoor dat het schijf type is ingesteld op **SSD**. Geef vervolgens een **gebruikers naam** op, bijvoorbeeld **azureuser**. 
1. Selecteer **wacht woord** bij **verificatie type**. Voer vervolgens een wacht woord in dat u later wilt gebruiken voor SSH in deze VM. 
1. Kies voor het maken van een **nieuwe resource groep**. Geef vervolgens een naam op, bijvoorbeeld **myResourceGroup**. Kies uw **locatie**. Selecteer vervolgens **OK**. 

    ![Maken van een Ubuntu-VM](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Selecteer een grootte voor de VM. U kunt bijvoorbeeld filteren op **Rekentype** of **Schijftype**. 

    ![Overzicht van de televirtual machine-grootte Telegraaf agent](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. Selecteer op de pagina **instellingen** in de   >  **beveiligings groep netwerk netwerk**  >  **open bare binnenkomende poorten**, selecteer **http** en **SSH (22)**. Laat de overige standaardwaarden staan en selecteer **OK**. 

1. Selecteer **Maken** op de overzichtspagina om de implementatie van de VM te starten. 

1. De VM wordt aan het dashboard van de Azure Portal vastgemaakt. Nadat de implementatie is voltooid, wordt de samen vatting van de VM automatisch geopend. 

1. Ga in het deel venster VM naar het tabblad **identiteit** . Zorg ervoor dat op de virtuele machine een door het systeem toegewezen identiteit is ingesteld op **aan**. 
 
    ![Voor beeld van telegrafie VM-identiteit](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine 

Maak een SSH-verbinding met de VM. Selecteer de knop **Verbinden** op de overzichtspagina van uw VM. 

![Overzichts pagina telegrafi VM](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

Laat op de pagina **Verbinding maken met virtuele machine** de standaardopties staan om verbinding te maken met de DNS-naam via poort 22. Bij **Aanmelden met een lokaal VM-account** wordt een verbindings opdracht weer gegeven. Selecteer de knop om de opdracht te kopiëren. Het volgende voorbeeld laat zien hoe de SSH-verbindingsopdracht eruitziet: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Plak de opdracht SSH-verbinding in een shell, zoals Azure Cloud Shell of bash op Ubuntu in Windows, of gebruik een SSH-client van uw keuze om de verbinding te maken. 

## <a name="install-and-configure-telegraf"></a>Telegraf installeren en configureren 

Als u het telegrafe Debian-pakket wilt installeren op de VM, voert u de volgende opdrachten uit vanuit uw SSH-sessie: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Met het configuratie bestand van de telegrafie worden de bewerkingen van telegrafie gedefinieerd. Standaard wordt een voorbeeld configuratie bestand geïnstalleerd op het pad **/etc/Telegraf/Telegraf.conf**. In het voorbeeld configuratie bestand worden alle mogelijke invoeg toepassingen voor invoer en uitvoer weer gegeven. We gaan echter een aangepast configuratie bestand maken en de agent gebruiken door de volgende opdrachten uit te voeren: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> Met de voor gaande code worden slechts twee invoer invoeg toepassingen ingeschakeld: **CPU** en **mem**. U kunt meer invoer invoeg toepassingen toevoegen, afhankelijk van de werk belasting die op uw computer wordt uitgevoerd. Voor beelden zijn docker, MySQL en NGINX. Zie de sectie **aanvullende configuratie** voor een volledige lijst met invoer-invoeg toepassingen. 

Ten slotte zorgen we ervoor dat de agent wordt gestopt en gestart door de volgende opdrachten uit te voeren, zodat de agent met de nieuwe configuratie kan beginnen: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
De agent verzamelt nu metrische gegevens uit elk van de opgegeven invoer-invoeg toepassingen en stuurt deze naar Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Uw telegrafeer metrische gegevens in de Azure Portal afzetten 

1. Open de [Azure Portal](https://portal.azure.com). 

1. Ga naar het tabblad nieuwe **monitor** . Selecteer vervolgens **metrische gegevens**.  

     ![Monitor-metrische gegevens (preview-versie)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Selecteer uw virtuele machine in de resource kiezer.

     ![Grafiek met metrische gegevens](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Selecteer de **Telegraf/CPU-** naam ruimte en selecteer de **usage_system** metrische gegevens. U kunt kiezen of u wilt filteren op de dimensies op deze metrische gegevens of ze wilt splitsen.  

     ![Naam ruimte en metriek selecteren](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Aanvullende configuratie 

De voor gaande procedure bevat informatie over het configureren van de telegrafie-agent voor het verzamelen van metrische gegevens van een aantal elementaire invoer-invoeg toepassingen. De Telegraf-agent biedt ondersteuning voor meer dan 150 invoer invoeg toepassingen, met een aantal aanvullende configuratie opties. InfluxData heeft een [lijst met ondersteunde invoeg toepassingen](https://docs.influxdata.com/telegraf/v1.15/plugins/inputs/) gepubliceerd en instructies voor [het configureren ervan](https://docs.influxdata.com/telegraf/v1.15/administration/configuration/).  

Daarnaast hebt u in dit overzicht de telegrafa-agent gebruikt voor het verzenden van metrische gegevens over de virtuele machine waarop de agent is geïmplementeerd. De telegrafie-agent kan ook worden gebruikt als Collector en doorstuur server van metrische gegevens voor andere resources. Zie [Azure monitor aangepaste metrische uitvoer voor telegrafie voor](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md)meer informatie over het configureren van de agent voor het verzenden van metrische gegevens voor andere Azure-resources.  

## <a name="clean-up-resources"></a>Resources opschonen 

Wanneer u deze niet meer nodig hebt, kunt u de resource groep, de virtuele machine en alle gerelateerde resources verwijderen. Als u dit wilt doen, selecteert u de resource groep voor de virtuele machine en selecteert u **verwijderen**. Bevestig vervolgens de naam van de resource groep die u wilt verwijderen. 

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [aangepaste metrische gegevens](./metrics-custom-overview.md).