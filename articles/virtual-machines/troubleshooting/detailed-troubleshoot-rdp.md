---
title: Gedetailleerde probleem oplossing voor extern bureau blad in azure | Microsoft Docs
description: Bekijk gedetailleerde stappen voor probleem oplossing voor problemen met extern bureau blad waar u geen virtuele Windows-machines kunt in azure
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: kan geen verbinding maken met extern bureau blad, problemen oplossen met extern bureau blad, extern bureau blad kan geen verbinding maken, fouten extern bureau blad, extern bureau blad oplossen, problemen met extern bureau blad
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: a17f3992663fe6de0751f7bc537297b9ba70cfc5
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98738132"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Gedetailleerde stappen voor probleemoplossing voor problemen met Extern bureaublad met Windows-VM's in Azure
In dit artikel vindt u gedetailleerde stappen voor het oplossen van problemen bij het vaststellen en oplossen van complexe Extern bureaublad fouten voor Azure virtual machines op basis van Windows.

> [!IMPORTANT]
> Als u de veelvoorkomende Extern bureaublad fouten wilt elimineren, leest u [het artikel basis probleem oplossing voor extern bureaublad](troubleshoot-rdp-connection.md) voordat u verdergaat.

Er kan een Extern bureaublad fout bericht worden weer gegeven dat niet overeenkomt met een van de specifieke fout berichten die worden behandeld in [de basis handleiding voor extern bureaublad probleem oplossing](troubleshoot-rdp-connection.md). Volg deze stappen om te bepalen waarom de Extern bureaublad (RDP)-client geen verbinding kan maken met de RDP-service op de virtuele Azure-machine.


Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en de stack overflow-forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en klik op **ondersteuning ophalen**. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.

## <a name="components-of-a-remote-desktop-connection"></a>Onderdelen van een Extern bureaublad verbinding
De volgende onderdelen zijn betrokken bij een RDP-verbinding:

![Een diagram van de onderdelen die betrokken zijn bij een Extern bureaublad RDP-verbinding.](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Voordat u verder gaat, kan worden nagegaan wat er is gewijzigd sinds de laatste geslaagde Extern bureaublad verbinding met de virtuele machine. Bijvoorbeeld:

* Het open bare IP-adres van de virtuele machine of de Cloud service met de VM (ook wel het [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)-adres voor virtuele IP-adressen genoemd) is gewijzigd. De RDP-fout kan zijn veroorzaakt doordat het *oude IP-adres* voor de DNS-naam nog steeds is geregistreerd voor de DNS-client cache. Flush uw DNS-client cache en probeer opnieuw verbinding te maken met de virtuele machine. Of probeer rechtstreeks verbinding te maken met de nieuwe VIP.
* U gebruikt een toepassing van derden om uw Extern bureaublad verbindingen te beheren in plaats van de verbinding die wordt gegenereerd door de Azure Portal. Controleer of de toepassings configuratie de juiste TCP-poort voor het Extern bureaublad verkeer bevat. U kunt deze poort controleren op een klassieke virtuele machine in de [Azure Portal](https://portal.azure.com)door te klikken op de instellingen van de VM > eind punten.

## <a name="preliminary-steps"></a>Voorbereidende stappen
Voordat u verdergaat met gedetailleerde probleem oplossing,

* Controleer de status van de virtuele machine in de Azure Portal op duidelijke problemen.
* Volg de [stappen voor het oplossen van veelvoorkomende RDP-fouten in de eenvoudige hand leiding voor het oplossen van problemen](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* Voor aangepaste installatie kopieën zorgt u ervoor dat uw VHD correct is voor bereid voordat u deze uploadt. Zie [een Windows VHD of VHDX voorbereiden voor het uploaden naar Azure](../windows/prepare-for-upload-vhd-image.md)voor meer informatie.


Probeer na de volgende stappen opnieuw verbinding te maken met de virtuele machine via Extern bureaublad.

## <a name="detailed-troubleshooting-steps"></a>Gedetailleerde stappen voor het oplossen van problemen
De Extern bureaublad-client kan de Extern bureaublad-service mogelijk niet bereiken op de Azure VM vanwege problemen met de volgende bronnen:

* [Client computer Extern bureaublad](#source-1-remote-desktop-client-computer)
* [Intranet edge-apparaat](#source-2-organization-intranet-edge-device)
* [Cloud service-eind punt en toegangs beheer lijst (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Netwerkbeveiligingsgroepen](#source-4-network-security-groups)
* [Azure VM op basis van Windows](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Bron 1: client computer Extern bureaublad
Controleer of de computer Extern bureaublad verbindingen kan maken met een andere on-premises, Windows-computer.

![Een diagram van de onderdelen in een Extern bureaublad (RDP)-verbinding met de RDP-client gemarkeerd en een pijl die verwijst naar een andere on-premises computer die een verbinding aangeeft.](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Als dat niet het geval is, controleert u op de volgende instellingen op uw computer:

* Een lokale firewall instelling die Extern bureaublad verkeer blokkeert.
* Lokaal geïnstalleerde client proxy software die Extern bureaublad verbindingen verhindert.
* Lokaal geïnstalleerde netwerk bewakings software die het Extern bureaublad verbindingen verhindert.
* Andere soorten beveiligings software die verkeer bewaken of toestaan/weigeren van specifieke typen verkeer die Extern bureaublad verbindingen verhinderen.

In al deze gevallen schakelt u de software tijdelijk uit en probeert u verbinding te maken met een on-premises computer via Extern bureaublad. Als u de werkelijke oorzaak op deze manier kunt achterhalen, gebruikt u uw netwerk beheerder om de software-instellingen te corrigeren om Extern bureaublad verbindingen toe te staan.

## <a name="source-2-organization-intranet-edge-device"></a>Bron 2: intranet edge-apparaat van organisatie
Controleer of een computer die rechtstreeks is verbonden met het Internet, Extern bureaublad verbindingen kan maken met uw virtuele Azure-machine.

![Een diagram van de onderdelen in een Extern bureaublad RDP-verbinding met een RDP-client die is verbonden met Internet gemarkeerd en een pijl die verwijst naar een virtuele machine van Azure met een verbinding.](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Als u niet beschikt over een computer die rechtstreeks is verbonden met internet, maakt en test u met een nieuwe virtuele machine van Azure in een resource groep of Cloud service. Zie [een virtuele machine met Windows maken in azure](../windows/quick-create-portal.md)voor meer informatie. Na de test kunt u de virtuele machine en de resource groep of de Cloud service verwijderen.

Als u een Extern bureaublad verbinding kunt maken met een computer die rechtstreeks is verbonden met internet, controleert u het intranet rand apparaat van uw organisatie voor:

* Een interne firewall die HTTPS-verbindingen met Internet blokkeert.
* Een proxy server die Extern bureaublad verbindingen verhindert.
* Indringings detectie of netwerk bewakings software die wordt uitgevoerd op apparaten in uw Edge-netwerk dat Extern bureaublad verbindingen verhindert.

Werk samen met uw netwerk beheerder om de instellingen van het intranet edge-apparaat van uw organisatie te corrigeren om Extern bureaublad verbindingen op basis van HTTPS op Internet toe te staan.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Bron 3: eind punt van Cloud service en ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Voor virtuele machines die zijn gemaakt met behulp van het klassieke implementatie model, controleert u of een andere virtuele machine van Azure die zich in dezelfde Cloud service of een virtueel netwerk bevindt, Extern bureaublad verbindingen met uw virtuele Azure-machine kan maken.

![Een diagram van de onderdelen in een Extern bureaublad-verbinding (RDP) met één Azure-VM gemarkeerd en een pijl die naar een andere Azure-VM verwijst binnen dezelfde Cloud service die een verbinding aangeeft.](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> Ga naar [bron 4: netwerk beveiligings groepen](#source-4-network-security-groups)voor virtuele machines die zijn gemaakt in Resource Manager.

Als u nog geen virtuele machine in dezelfde Cloud service of een virtueel netwerk hebt, maakt u er een. Volg de stappen in [een virtuele machine met Windows maken in azure](../windows/quick-create-portal.md). Verwijder de virtuele test machine nadat de test is voltooid.

Als u verbinding kunt maken via Extern bureaublad naar een virtuele machine in dezelfde Cloud service of een virtueel netwerk, controleert u de volgende instellingen:

* De eindpunt configuratie voor Extern bureaublad verkeer op de doel-VM: de particuliere TCP-poort van het eind punt moet overeenkomen met de TCP-poort waarop de Extern bureaublad-service van de virtuele machine luistert (de standaard waarde is 3389).
* De ACL voor het eind punt van Extern bureaublad verkeer op de doel-VM: met Acl's kunt u het toegestane of geweigerde binnenkomende verkeer van Internet opgeven op basis van het bron-IP-adres. Onjuist geconfigureerde Acl's kunnen binnenkomende Extern bureaublad verkeer naar het eind punt verhinderen. Controleer uw Acl's om ervoor te zorgen dat binnenkomend verkeer van uw open bare IP-adressen van uw proxy of een andere rand server wordt toegestaan. Zie [Wat is een Network Access Control List (ACL)?](/previous-versions/azure/virtual-network/virtual-networks-acl) voor meer informatie.

Als u wilt controleren of het eind punt de oorzaak van het probleem is, verwijdert u het huidige eind punt en maakt u een nieuwe, en kiest u een wille keurige poort in het bereik 49152 – 65535 voor het externe poort nummer. Zie [eind punten instellen voor een virtuele machine](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=/azure/virtual-machines/windows/classic/toc.json)voor meer informatie.

## <a name="source-4-network-security-groups"></a>Bron 4: netwerk beveiligings groepen
Met netwerk beveiligings groepen kunt u meer nauw keurige controle van toegestaan binnenkomend en uitgaand verkeer. U kunt regels maken die subnetten en Cloud Services bespannen in een virtueel Azure-netwerk.

Gebruik [IP-stroom controleren](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) om te controleren of het verkeer van of naar een virtuele machine wordt geblokkeerd door een regel in een netwerk beveiligings groep. U kunt ook de juiste regels voor beveiligings groepen controleren om ervoor te zorgen dat inkomende NSG regel bestaat en de prioriteit van de RDP-poort (standaard 3389) wordt weer gegeven. Zie [using effectief security rules to Troubleshooting VM Traffic Flow](../../virtual-network/diagnose-network-traffic-filter-problem.md)voor meer informatie.

## <a name="source-5-windows-based-azure-vm"></a>Bron 5: Azure VM op basis van Windows
![Een diagram van de onderdelen in een Extern bureaublad (RDP)-verbinding met een Azure VM, gemarkeerd in een Cloud service en een bericht dat het mogelijk een ' mogelijke bron van problemen ' is. Een blauwe lijn geeft aan dat de regels van de netwerk beveiligings groep het verkeer van of naar de virtuele Azure-machine kunnen blok keren.](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Volg de instructies in [dit artikel](./reset-rdp.md). In dit artikel wordt de Extern bureaublad-service op de virtuele machine opnieuw ingesteld:

* Schakel de Windows Firewall standaard regel voor ' Extern bureaublad ' in (TCP-poort 3389).
* Schakel Extern bureaublad verbindingen in door de register waarde HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections in te stellen op 0.

Probeer de verbinding vanaf uw computer opnieuw. Als u nog steeds geen verbinding kunt maken via Extern bureaublad, controleert u op de volgende mogelijke problemen:

* De Extern bureaublad-service wordt niet uitgevoerd op de doel-VM.
* De Extern bureaublad-service luistert niet op TCP-poort 3389.
* Windows Firewall of een andere lokale firewall heeft een regel voor uitgaande verbindingen waardoor Extern bureaublad verkeer wordt voor komen.
* Indringings detectie of netwerk bewakings software die wordt uitgevoerd op de virtuele machine van Azure, verhindert Extern bureaublad verbindingen.

Voor virtuele machines die zijn gemaakt met het klassieke implementatie model, kunt u een externe Azure PowerShell-sessie gebruiken voor de virtuele machine van Azure. Eerst moet u een certificaat installeren voor de Cloud service die als host fungeert voor de virtuele machine. Ga naar [beveiligde externe Power shell-toegang configureren voor Azure virtual machines](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) en down load het **InstallWinRMCertAzureVM.ps1** script bestand op uw lokale computer.

Installeer vervolgens Azure PowerShell als u dat nog niet hebt gedaan. Zie [Azure PowerShell installeren en configureren](/powershell/azure/).

Open vervolgens een Azure PowerShell opdracht prompt en wijzig de huidige map in de locatie van het **InstallWinRMCertAzureVM.ps1** script bestand. Als u een Azure PowerShell script wilt uitvoeren, moet u het juiste uitvoerings beleid instellen. Voer de opdracht **Get-ExecutionPolicy** uit om het huidige beleids niveau te bepalen. Zie [Set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy)voor meer informatie over het instellen van het juiste niveau.

Vul vervolgens de naam van uw Azure-abonnement, de naam van de Cloud service en de naam van uw virtuele machine in (Verwijder de < en > tekens) en voer deze opdrachten uit.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

U kunt de juiste abonnements naam ophalen uit de eigenschap *subscriptionname* van de weer gave van de opdracht **Get-abonnement** . U kunt de naam van de Cloud service voor de virtuele machine ophalen uit de kolom *ServiceName* in de weer gave van de opdracht **Get-AzureVM** .

Controleer of u het nieuwe certificaat hebt. Open een module Certificaten voor de huidige gebruiker en zoek in de map **vertrouwde basis certificerings basiscertificeringsinstanties\certificaten** . U ziet een certificaat met de DNS-naam van uw Cloud service in de kolom verleend aan (voor beeld: cloudservice4testing.cloudapp.net).

Vervolgens initieert u een externe Azure PowerShell-sessie met behulp van deze opdrachten.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Nadat u geldige beheerders referenties hebt ingevoerd, ziet u een bericht dat lijkt op het volgende Azure PowerShell prompt:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

Het eerste deel van deze prompt is de naam van de Cloud service die de doel-VM bevat, die kan verschillen van ' cloudservice4testing.cloudapp.net '. U kunt nu Azure PowerShell-opdrachten voor deze Cloud service geven om de vermelde problemen te onderzoeken en de configuratie te corrigeren.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>De Extern bureaublad-services luisterende TCP-poort hand matig corrigeren
Voer de volgende opdracht uit bij de prompt voor de externe Azure PowerShell sessie.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

De eigenschap Port bereik geeft het huidige poort nummer weer. Wijzig, indien nodig, het Extern bureaublad poort nummer weer in de standaard waarde (3389) met behulp van deze opdracht.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Controleer of de poort is gewijzigd in 3389 met behulp van deze opdracht.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Sluit de externe Azure PowerShell-sessie met behulp van deze opdracht.

```powershell
Exit-PSSession
```

Controleer of het Extern bureaublad-eind punt voor de virtuele machine van Azure ook TCP-poort 3398 als interne poort gebruikt. Start de Azure-VM opnieuw op en probeer opnieuw verbinding te Extern bureaublad.

## <a name="additional-resources"></a>Aanvullende bronnen
[Het opnieuw instellen van een wacht woord of de Extern bureaublad-service voor virtuele Windows-machines](./reset-rdp.md)

[Azure PowerShell installeren en configureren](/powershell/azure/)

[Problemen met SSH-verbindingen (Secure Shell) met een op Linux gebaseerde Azure Virtual Machine oplossen](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json)

[Problemen oplossen met toegang tot een toepassing die wordt uitgevoerd op een virtuele Azure-machine](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json)
