---
title: Release opmerkingen bij Azure Stack Edge Pro 2101
description: Hierin worden essentiële openstaande problemen en oplossingen voor de Azure Stack Edge Pro-versie van 2101 beschreven.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/19/2021
ms.author: alkohli
ms.openlocfilehash: d0b7f871b2ea62c810a6d20f6e20a5e8d3f6306e
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791910"
---
# <a name="azure-stack-edge-2101-release-notes"></a>Release opmerkingen bij Azure Stack Edge 2101

De volgende opmerkingen bij de release identificeren de kritieke openstaande problemen en de opgeloste problemen voor de 2101-release voor uw Azure Stack edge-apparaten. Deze release opmerkingen zijn van toepassing op Azure Stack Edge Pro GPU, Azure Stack Edge Pro R en Azure Stack Edge mini-R-apparaten. Functies en problemen die overeenkomen met een specifiek model, worden waar van toepassing aangeroepen.

De release opmerkingen worden voortdurend bijgewerkt en als er kritieke problemen worden gedetecteerd die een tijdelijke oplossing vereisen, worden deze toegevoegd. Voordat u uw apparaat implementeert, moet u de informatie in de opmerkingen bij de release aandachtig door nemen.

Dit artikel is van toepassing op de **Azure stack Edge 2101** -release, die wordt toegewezen aan het software versie nummer **2.2.1473.2521**.

## <a name="whats-new"></a>Nieuwe functies

De volgende nieuwe functies zijn beschikbaar in de Azure Stack Edge 2101-release. 

- **Algemene Beschik baarheid van Azure stack Edge Pro r-en Azure stack Edge mini-r-apparaten** : vanaf deze release worden Azure stack Edge Pro R-en Azure stack Edge mini-r-apparaten beschikbaar. Zie [Wat is Azure stack Edge Pro R](azure-stack-edge-j-series-overview.md) en [Wat is Azure stack Edge mini-r](azure-stack-edge-k-series-overview.md)voor meer informatie.  
- **Cloud beheer van virtual machines** -het starten van deze release kunt u de virtuele machines op het apparaat maken en beheren via de Azure Portal. Zie [Vm's implementeren via de Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)voor meer informatie.
- **Integratie met Azure monitor** : u kunt Azure monitor nu gebruiken om containers te bewaken vanuit de compute-toepassingen die op uw apparaat worden uitgevoerd. Het opslag Azure Monitor metrische gegevens wordt niet ondersteund in deze versie. Zie [Azure monitor op het apparaat inschakelen](azure-stack-edge-gpu-enable-azure-monitor.md)voor meer informatie.
- **Edge-container register** : in deze release is er een rand container register beschikbaar dat een opslag plaats biedt aan de rand van uw apparaat. U kunt dit REGI ster gebruiken om container installatie kopieën op te slaan en te beheren. Zie [Edge container Registry inschakelen](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)voor meer informatie. 
- **Virtueel particulier netwerk (VPN)** : gebruik VPN om een andere versleutelings code te bieden voor de gegevens die stromen tussen de apparaten en de Cloud. VPN is alleen beschikbaar op Azure Stack Edge Pro R en Azure Stack Edge mini-R. Zie [VPN configureren op uw apparaat](azure-stack-edge-mini-r-configure-vpn-powershell.md)voor meer informatie. 
- **Versleutelen op rest-sleutels draaien** : u kunt nu de sleutels voor versleuteling op rest draaien die worden gebruikt voor het beveiligen van de stations op het apparaat. Deze functie is alleen beschikbaar voor Azure Stack Edge Pro R-en Azure Stack Edge mini-R-apparaten. Zie voor meer informatie [code ring-at-rest-sleutels draaien](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#manage-access-to-device-data).
- **Proactieve logboek registratie** : door deze release te starten, kunt u proactieve logboek verzameling op uw apparaat inschakelen op basis van de status indicatoren van het systeem om problemen met apparaten efficiënt op te lossen. Zie [proactieve logboek verzameling op uw apparaat](azure-stack-edge-gpu-proactive-log-collection.md)voor meer informatie.


## <a name="known-issues-in-2101-release"></a>Bekende problemen in de 2101-release

De volgende tabel bevat een samen vatting van bekende problemen in de 2101-release.

| Nee. | Functie | Probleem | Tijdelijke oplossing/opmerkingen |
| --- | --- | --- | --- |
|**1.**|Preview-functies |Voor deze release zijn de volgende functies: lokale Azure Resource Manager, Vm's, Cloud beheer van Vm's, Azure-Kubernetes, VPN voor Azure Stack Edge Pro R en Azure Stack Edge mini R, service voor meerdere processen (MPS) voor Azure Stack Edge Pro GPU-zijn allemaal beschikbaar als preview-versie.  |Deze functies zijn algemeen beschikbaar in latere versies. |
|**2.**|Kubernetes-dash board | Het *https* -eind punt voor het Kubernetes-dash board met een SSL-certificaat wordt niet ondersteund. | |
|**3.**|Kubernetes |Het Edge-container register werkt niet als webproxy is ingeschakeld.|De functionaliteit is beschikbaar in een toekomstige versie. |
|**4.**|Kubernetes |Het Edge-container register werkt niet met IoT Edge-modules.| |
|**5.**|Kubernetes |Kubernetes biedt geen ondersteuning voor ': ' in namen van omgevings variabelen die worden gebruikt door .NET-toepassingen. Dit is ook vereist voor de module Event grid IoT Edge op Azure Stack edge-apparaat en andere toepassingen te laten functioneren. Zie [ASP.net core-documentatie](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration&view=aspnetcore-3.1&preserve-view=true#environment-variables)voor meer informatie.|Vervang ":" door dubbel onderstrepen. Zie [Kubernetes issue](https://github.com/kubernetes/kubernetes/issues/53201) (Engelstalig) voor meer informatie.|
|**6.** |Azure Arc + Kubernetes-cluster |Wanneer `yamls` een resource wordt verwijderd uit de Git-opslag plaats, worden de bijbehorende resources standaard niet verwijderd uit het Kubernetes-cluster.  |U moet `--sync-garbage-collection` in Arc OperatorParams instellen om het verwijderen van resources toe te staan wanneer deze uit de Git-opslag plaats worden verwijderd. Zie [een configuratie verwijderen](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters)voor meer informatie. |
|**7.**|NFS |Toepassingen die gebruikmaken van NFS-shares op uw apparaat om gegevens te schrijven, moeten exclusieve schrijf bewerkingen gebruiken. Als u exclusieve schrijf bewerking gebruikt, zorgt u ervoor dat de schrijf bewerkingen naar de schijf worden geschreven.| |
|**achtste.**|Compute-configuratie |De reken configuratie mislukt in netwerk configuraties waarbij gateways of switches of routers reageren op ARP-aanvragen (Address Resolution Protocol) voor systemen die niet bestaan in het netwerk.| |
|**9,4.**|Compute en Kubernetes |Als Kubernetes eerst op uw apparaat is ingesteld, worden alle beschik bare Gpu's claimen. Daarom is het niet mogelijk om Azure Resource Manager Vm's te maken met behulp van Gpu's nadat u de Kubernetes hebt ingesteld. |Als uw apparaat 2 Gpu's heeft, kunt u één virtuele machine maken die gebruikmaakt van de GPU en vervolgens Kubernetes configureren. In dit geval maakt Kubernetes gebruik van de resterende beschik bare 1 GPU. |


## <a name="known-issues-from-previous-releases"></a>Bekende problemen met eerdere releases

In de volgende tabel vindt u een overzicht van de bekende problemen die in de vorige releases worden uitgevoerd.

| Nee. | Functie | Probleem | Tijdelijke oplossing/opmerkingen |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | Voor het maken van SQL database is beheerders toegang vereist.   |Voer de volgende stappen uit in plaats van stap 1-2 in [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>Schakel in de lokale gebruikers interface van uw apparaat Compute interface in. Selecteer **reken > poort # > inschakelen voor compute > van toepassing.**</li><li>Down load `sqlcmd` op de client computer van https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Maak verbinding met het IP-adres van de compute-interface (de poort die is ingeschakeld), waarbij een ', 1401 ' aan het einde van het adres wordt toegevoegd.</li><li>De laatste opdracht ziet er als volgt uit: sqlcmd-S {Interface IP}, 1401-U SA-P "Strong! Passw0rd".</li>Hierna moeten stap 3-4 van de huidige documentatie identiek zijn. </li></ul> |
| **2.** |Vernieuwen| Incrementele wijzigingen in blobs die zijn hersteld via **vernieuwen** , worden niet ondersteund |Voor BLOB-eind punten kunnen gedeeltelijke updates van blobs na een vernieuwing ertoe leiden dat de updates niet worden geüpload naar de Cloud. Bijvoorbeeld volg orde van acties zoals:<ul><li>Maak een BLOB in de Cloud. Of verwijder een eerder geüploade blob van het apparaat.</li><li>Vernieuw de blob van de Cloud naar het apparaat met behulp van de functie voor vernieuwen.</li><li>Werk slechts een deel van de BLOB bij met Azure SDK REST Api's.</li></ul>Deze acties kunnen ertoe leiden dat de bijgewerkte secties van de BLOB niet worden bijgewerkt in de Cloud. <br>**Tijdelijke oplossing**: gebruik hulpprogram ma's zoals Robocopy of een gewone bestands kopie via Verkenner of opdracht regel om de hele blobs te vervangen.|
|**3.**|Beperking|Als er tijdens het beperken een nieuwe schrijf bewerking naar het apparaat niet is toegestaan, mislukt de schrijf bewerkingen door de NFS-client met de fout ' permission denied '.| De volgende fout wordt weer gegeven:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: kan directory ' test ' niet maken: de machtiging is geweigerd|
|**4.**|Opname Blob Storage|Wanneer u AzCopy versie 10 gebruikt voor inslikken van Blob-opslag, voert u AzCopy uit met het volgende argument: `Azcopy <other arguments> --cap-mbps 2000`| Als deze limieten niet worden verstrekt voor AzCopy, kan er een groot aantal aanvragen naar het apparaat worden verzonden, wat leidt tot problemen met de service.|
|**5.**|Gelaagde opslag accounts|Het volgende is van toepassing wanneer u gelaagde opslag accounts gebruikt:<ul><li> Alleen blok-blobs worden ondersteund. Pagina-blobs worden niet ondersteund.</li><li>Er is geen moment opname-of Copy API-ondersteuning.</li><li> De inslikken van een Hadoop-werk belasting `distcp` wordt niet ondersteund omdat de Kopieer bewerking intensief wordt gebruikt.</li></ul>||
|**6.**|NFS-share verbinding|Als er meerdere processen naar dezelfde share worden gekopieerd en het `nolock` kenmerk niet wordt gebruikt, worden er mogelijk fouten weer geven tijdens de kopie.|Het `nolock` kenmerk moet worden door gegeven aan de koppelings opdracht om bestanden te kopiëren naar de NFS-share. Bijvoorbeeld: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Kubernetes-cluster|Wanneer u een update toepast op uw apparaat waarop een kubernetes-cluster wordt uitgevoerd, worden de virtuele machines van kubernetes opnieuw opgestart en opnieuw opgestart. In dit geval worden alleen de peulen die zijn geïmplementeerd met de opgegeven replica's automatisch hersteld na een update.  |Als u afzonderlijke peulen buiten een replicatie controller hebt gemaakt zonder een replicaset op te geven, wordt deze peul niet automatisch hersteld na het bijwerken van het apparaat. U moet deze peul herstellen.<br>Een replicaset vervangt de peulen die worden verwijderd of beëindigd om een wille keurige reden, zoals een storing in het knoop punt of een onderbreking van de upgrade van knoop punten. Daarom wordt u aangeraden een replicaset te gebruiken, zelfs als uw toepassing slechts één Pod vereist.|
|**achtste.**|Kubernetes-cluster|Kubernetes op Azure Stack Edge Pro wordt alleen ondersteund met helm v3 of hoger. Voor meer informatie gaat u naar [Veelgestelde vragen: verwijderen van Tiller](https://v3.helm.sh/docs/faq/).|
|**9,4.**|Kubernetes met Azure Arc |Voor de GA-release is Azure Arc enabled Kubernetes bijgewerkt van versie 0.1.18 naar 0.2.9. Omdat de Azure Arc enabled Kubernetes-update niet wordt ondersteund op Azure Stack edge-apparaat, moet u de Kubernetes van Azure-Arc opnieuw implementeren.|Volg deze stappen:<ol><li>[Software-en Kubernetes-updates voor apparaten Toep assen](azure-stack-edge-gpu-install-update.md).</li><li>Maak verbinding met de [Power shell-interface van het apparaat](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Verwijder de bestaande Azure Arc-agent. Type: `Remove-HcsKubernetesAzureArcAgent` .</li><li>Implementeer [Azure Arc op een nieuwe resource](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Gebruik geen bestaande Azure Arc-resource.</li></ol>|
|**6.**|Kubernetes met Azure Arc|Implementaties van Azure Arc worden niet ondersteund als webproxy is geconfigureerd op uw Azure Stack Edge Pro-apparaat.||
|**9.**|Kubernetes |Poort 31000 is gereserveerd voor Kubernetes-dash board. Poort 31001 is gereserveerd voor het Edge-container register. Op dezelfde manier worden in de standaard configuratie de IP-adressen 172.28.0.1 en 172.28.0.10 gereserveerd voor respectievelijk de Kubernetes-service en de basis-DNS-service.|Gebruik geen gereserveerde Ip's.|
|**12.**|Kubernetes |Kubernetes ondersteunt momenteel geen LoadBalancer-Services met meerdere protocollen. Bijvoorbeeld een DNS-service die luistert op TCP en UDP. |Om deze beperking van Kubernetes met MetalLB te omzeilen, kunnen twee services (een voor TCP, een voor UDP) worden gemaakt op dezelfde pod selector. Deze services gebruiken dezelfde uitwisselings sleutel en spec. loadBalancerIP om hetzelfde IP-adres te delen. Ip's kunnen ook worden gedeeld als u meer services hebt dan beschik bare IP-adressen. <br> Zie [IP-adres delen](https://metallb.universe.tf/usage/#ip-address-sharing)voor meer informatie.|
|**13.**|Kubernetes-cluster|Bestaande Azure IoT Edge Marketplace-modules kunnen wijzigingen vereisen om te worden uitgevoerd op IoT Edge op Azure Stack edge-apparaat.|Zie voor meer informatie Azure IoT Edge modules van Marketplace aanpassen zodat ze op Azure Stack edge-apparaat kunnen worden uitgevoerd.<!-- insert link-->|
|**15.**|Kubernetes |BIND koppelingen op basis van een bestand worden niet ondersteund met Azure IoT Edge op Kubernetes op Azure Stack edge-apparaat.|IoT Edge maakt gebruik van een Vertaal laag om `ContainerCreate` Opties om te zetten in Kubernetes-constructies. Het `Binds` is niet mogelijk om toewijzingen te maken naar een `hostpath` map en daarom BIND koppelingen op basis van een bestand kunnen niet worden gebonden aan paden in IOT Edge containers. Als dat mogelijk is, wijst u de bovenliggende map toe.|
|**15.**|Kubernetes |Als u uw eigen certificaten voor IoT Edge en deze toevoegt op uw Azure Stack edge-apparaat nadat de berekening op het apparaat is geconfigureerd, worden de nieuwe certificaten niet opgehaald.|U kunt dit probleem omzeilen door de certificaten te uploaden voordat u Compute op het apparaat configureert. Als de compute al is geconfigureerd, [maakt u verbinding met de Power shell-interface van het apparaat en voert u IOT Edge opdrachten uit](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Opnieuw opstarten `iotedged` en `edgehub` peulen.|
|**18.**|Certificaten |In bepaalde gevallen kan de status van het certificaat in de lokale gebruikers interface enkele seconden duren voordat deze wordt bijgewerkt. |De volgende scenario's in de lokale gebruikers interface kunnen worden beïnvloed.<ul><li>De kolom **status** op de pagina **certificaten** .</li><li>De tegel **beveiliging** op de pagina **aan de slag** .</li><li>**Configuratie** tegel op de pagina **overzicht** .</li></ul>  |
|**Nr.**|IoT Edge |Modules die via IoT Edge worden geïmplementeerd, kunnen geen gebruik van het doelnet werken. | |
|**18,0.**|Compute + Kubernetes |Compute/Kubernetes biedt geen ondersteuning voor NTLM-webproxy. ||
|**19.**|Compute + web proxy + update |Als u Compute hebt geconfigureerd met web proxy, kan de compute-update mislukken. |U wordt aangeraden reken kracht vóór de update uit te scha kelen. |
|**20.**|Kubernetes + update |Eerdere software versies, zoals 2008 releases, hebben een update probleem voor de race situatie waardoor de update mislukt met ClusterConnectionException. |Het gebruik van de nieuwere builds helpt dit probleem te voor komen. Als dit probleem zich blijft voordoen, kunt u de tijdelijke oplossing het beste opnieuw proberen.|


<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->


## <a name="next-steps"></a>Volgende stappen

- [Uw apparaat bijwerken](azure-stack-edge-gpu-install-update.md)
