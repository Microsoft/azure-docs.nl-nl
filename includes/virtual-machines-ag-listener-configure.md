---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: fd635d4c0563c35979f8d85c33dfbde35f05f9e6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91401073"
---
De listener voor de beschikbaarheidsgroep is een IP-adres en netwerknaam waarnaar de SQL Server-beschikbaarheidsgroep luistert. Doe het volgende om de listener voor de beschikbaarheidsgroep te maken:

1. <a name="getnet"></a>Haal de naam op van het de clusternetwerkresource.

    a. Gebruik RDP om verbinding te maken met de Azure-VM die de primaire replica host. 

    b. Failoverclusterbeheer openen.

    c. Selecteer het knooppunt **Netwerken** en noteer de naam van het clusternetwerk. Gebruik deze naam in de variabele `$ClusterNetworkName` in het PowerShell-script. In de volgende afbeelding is de naam van het clusternetwerk **Clusternetwerk 1**:

   ![Naam van het clusternetwerk](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Voeg het clienttoegangspunt toe.  
    Het clienttoegangspunt is de netwerknaam die toepassingen gebruiken om verbinding te maken met de databases in een beschikbaarheidsgroep. Maak het clienttoegangspunt in Failoverclusterbeheer.

    a. Vouw de clusternaam uit en klik vervolgens op **Rollen**.

    b. Klik in het deelvenster **Rollen** met de rechtermuisknop op de beschikbaarheidsgroepnaam en selecteer **Resource toevoegen** > **Clienttoegangspunt**.

   ![Schermopname die de menuoptie Clienttoegangspunt weergeeft.](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. Maak in het venster **Naam** een naam voor deze nieuwe listener. 
   De naam van de nieuwe listener is de netwerknaam die toepassingen gebruiken om verbinding te maken met databases in de SQL Server-beschikbaarheidsgroep.

    d. Klik twee keer op **Volgende** en nog een keer op **Voltooien** om de listener te maken. Breng de listener of resource op dit moment nog niet online.

1. Haal de clusterrol van de beschikbaarheidsgroep offline. Klik in **Failoverclusterbeheer** onder **Rollen** met de rechtermuisknop op de rol en selecteer **Rol stoppen**.

1. <a name="congroup"></a>De IP-resource configureren voor de beschikbaarheidsgroep.

    a. Klik op het tabblad **Resources** en breid vervolgens het clienttoegangspunt uit dat u hebt gemaakt.  
    Het clienttoegangspunt is offline.

   ![Clienttoegangspunt](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Klik met de rechtermuisknop op de IP-resource en klik vervolgens op Eigenschappen. Noteer de naam van het IP-adres en gebruik het in de variabele `$IPResourceName` in het PowerShell-script.

    c. Klik onder **IP-adres** op **Vast IP-adres**. Stel het IP-adres in op hetzelfde adres dat je hebt gebruikt toen u het adres van de load balancer instelde in de Azure Portal.

   ![Schermopname dat laat zien waar u het IP-adres hebt ingesteld.](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Maak de SQL Server-beschikbaarheidsgroep resourceafhankelijk van het clienttoegangspunt.

    a. Klik in Failoverclusterbeheer op **Rollen** en klik vervolgens op uw beschikbaarheidsgroep.

    b. Klik in het tabblad **Resources** onder **Overige resources** met de rechtermuisknop op de beschikbaarheidsresourcegroep en klik vervolgens op **Eigenschappen**. 

    c. Voeg in het tabblad Afhankelijkheden de naam van het clienttoegangspunt toe (de listener).

   ![Schermopname die laat zien waar u de naam toevoegt in het tabblad Afhankelijkheden.](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Klik op **OK**.

1. <a name="listname"></a>Maak het clienttoegangspunt afhankelijk van het IP-adres.

    a. Klik in Failoverclusterbeheer op **Rollen** en klik vervolgens op uw beschikbaarheidsgroep. 

    b. Klik in het tabblad **Resources** met de rechtermuisknop op het clienttoegangspunt onder **Servernaam** en klik vervolgens op **Eigenschappen**. 

   ![Schermopname die de menuoptie Eigenschappen weergeeft voor de servernaam.](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Klik op het tabblad **Afhankelijkheden**. Verifieer dat het IP-adres een afhankelijkheid is. Als dat niet zo is, stelt u een afhankelijkheid in op het IP-adres. Als er meerdere resources zijn vermeld, verifieert u dat de IP-adressen OF-afhankelijkheden hebben, niet EN-afhankelijkheden. Klik op **OK**. 

   ![IP-resource](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >U kunt controleren of de afhankelijkheden correct zijn geconfigureerd. Ga in Failoverclusterbeheer naar Rollen, klik met de rechtermuisknop op de beschikbaarheidsgroep, klik op **Meer acties** en klik vervolgens op **Afhankelijkheidsrapport weergeven**. Wanneer de afhankelijkheden correct zijn geconfigureerd, is de beschikbaarheidsgroep afhankelijk van de netwerknaam en is de netwerknaam afhankelijk van het IP-adres. 


1. <a name="setparam"></a>Stel de clusterparameters in PowerShell in.

   a. Kopieer het volgende PowerShell-script naar een van uw SQL Server-exemplaren. Werk de variabelen voor uw omgeving bij.

   - `$ListenerILBIP` is het IP-adres dat u hebt gemaakt in de Azure load balancer voor de listener voor de beschikbaarheidsgroep.
    
   - `$ListenerProbePort` is de poort die u hebt geconfigureerd in de Azure load balancer voor de listener voor de beschikbaarheidsgroep.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Stel de clusterparameters in door het PowerShell-script uit te voeren in een van de clusterknooppunten.  

   > [!NOTE]
   > Als uw SQL Server-exemplaren zich in een andere regio bevinden, moet u het PowerShell-script twee keer uitvoeren. De eerste keer gebruikt u de `$ListenerILBIP` en `$ListenerProbePort` van de eerste regio. De tweede keer gebruikt u de `$ListenerILBIP` en `$ListenerProbePort` van de tweede regio. De clusternetwerknaam en de IP-resourcenaam van het cluster zijn ook verschillend voor elke regio.

1. Breng de clusterrol van de beschikbaarheidsgroep online. Klik in **Failoverclusterbeheer** onder **Rollen** met de rechtermuisknop op de rol en selecteer **Rol starten**.

Herhaal indien nodig de bovenstaande stappen om de clusterparameters in te stellen voor het IP-adres van het WSFC-cluster.

1. Haal de IP-adresnaam van het IP-adres van het WSFC-cluster op. Zoek in **Failoverclusterbeheer** onder **Clustercoreresources** naar de **Servernaam**.

1. Klik met de rechtermuisknop op **IP-adres** en selecteer **Eigenschappen**.

1. Kopieer de **Naam** van het IP-adres. Dat kan `Cluster IP Address` zijn. 

1. <a name="setwsfcparam"></a>Stel de clusterparameters in PowerShell in.
  
   a. Kopieer het volgende PowerShell-script naar een van uw SQL Server-exemplaren. Werk de variabelen voor uw omgeving bij.

   - `$ClusterCoreIP` is het IP-adres dat u hebt gemaakt in de Azure load balancer voor de WSFC-coreclusterresource. Het verschilt van het IP-adres voor de beschikbaarheidsgroeplistener.

   - `$ClusterProbePort` is de poort die u hebt geconfigureerd in de Azure load balancer voor de WSFC-statustest. Het verschilt van de test voor de beschikbaarheidsgroeplistener.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Stel de clusterparameters in door het PowerShell-script uit te voeren in een van de clusterknooppunten.  

>[!WARNING]
>De poort voor de statustest van de beschikbaarheidsgroeplistener moet anders zijn dan de poort van het IP-adres voor de statustest van de clustercore. In deze voorbeelden is de listenerpoort 59999 en is de poort voor de statustest van het IP-adres 58888. Voor beide poorten is een firewallregel nodig waarin binnenkomend verkeer wordt toegestaan.
