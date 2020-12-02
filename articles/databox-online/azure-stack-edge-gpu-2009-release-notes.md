---
title: Release opmerkingen bij Azure Stack Edge Pro GA | Microsoft Docs
description: Hierin worden essentiële openstaande problemen en oplossingen beschreven voor de release van de algemene Beschik baarheid van Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 4aa25024273d62fe5b292d329f6470a828b952a7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449535"
---
# <a name="azure-stack-edge-pro-with-gpu-general-availability-ga-release-notes"></a>Release opmerkingen voor Azure Stack Edge Pro met GPU-algemene Beschik baarheid (GA)

In de volgende release opmerkingen worden de essentiële openstaande problemen en de opgeloste problemen voor de release van de algemene Beschik baarheid (GA) voor uw Azure Stack Edge Pro-apparaten met GPU geïdentificeerd.

De release opmerkingen worden voortdurend bijgewerkt en als er kritieke problemen worden gedetecteerd die een tijdelijke oplossing vereisen, worden deze toegevoegd. Lees de informatie in de release opmerkingen aandachtig door voordat u uw Azure Stack Edge Pro-apparaat implementeert.

Dit artikel is van toepassing op de **Azure stack Edge Pro 2010** -release die wordt toegewezen aan het software versie nummer **2.1.1377.2170**.

## <a name="whats-new"></a>Nieuwe functies

De volgende nieuwe functies zijn beschikbaar in de Azure Stack Edge 2010-release. 

- **Opslag klassen** : in deze release zijn opslag klassen beschikbaar waarmee u opslag dynamisch kunt inrichten. Zie [Kubernetes Storage Management op uw Azure stack Edge Pro GPU-apparaat](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning)voor meer informatie. 
- **Kubernetes dash board met metrische gegevens server** : in deze release wordt een Kubernetes-dash board toegevoegd met een invoeg toepassing voor metrische Server gegevens. U kunt het dash board gebruiken om een overzicht te krijgen van de toepassingen die worden uitgevoerd op uw Azure Stack Edge Pro-apparaat, de status van Kubernetes-cluster bronnen weer te geven en eventuele fouten te bekijken die op het apparaat zijn opgetreden. De metrics-server aggregeert het CPU-en geheugen gebruik in Kubernetes bronnen op het apparaat. Zie [Kubernetes dash board gebruiken om uw Azure stack Edge Pro GPU-apparaat te bewaken](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)voor meer informatie.
- **Azure Arc enabled Kubernetes op Azure stack Edge Pro** : als u deze release start, kunt u de werk belasting van toepassingen implementeren op uw Azure stack Edge Pro-apparaat via Azure Arc enabled Kubernetes. Azure Arc is een Hybrid management tool waarmee u toepassingen kunt implementeren in uw Kubernetes-clusters. Zie [workloads implementeren via Azure Arc op uw Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)voor meer informatie.  

## <a name="known-issues"></a>Bekende problemen 

De volgende tabel bevat een overzicht van bekende problemen voor het Azure Stack Edge Pro-apparaat.

| Nee. | Functie | Probleem | Tijdelijke oplossing/opmerkingen |
| --- | --- | --- | --- |
|**1.**|Preview-functies |Voor deze GA-versie zijn de volgende functies: lokale Azure Resource Manager, Vm's, Kubernetes, Azure-Arc ingeschakeld Kubernetes, multi-process service (MPS) voor GPU-zijn allemaal beschikbaar als Preview voor uw Azure Stack Edge Pro-apparaat.  |Deze functies zijn algemeen beschikbaar in een latere versie. |
| **2.** |Azure Stack Edge Pro + Azure SQL | Voor het maken van SQL database is beheerders toegang vereist.   |Voer de volgende stappen uit in plaats van stap 1-2 in [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>Schakel in de lokale gebruikers interface van uw apparaat Compute interface in. Selecteer **reken > poort # > inschakelen voor compute > van toepassing.**</li><li>Down load `sqlcmd` op de client computer van https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Maak verbinding met het IP-adres van de compute-interface (de poort die is ingeschakeld), waarbij een ', 1401 ' aan het einde van het adres wordt toegevoegd.</li><li>De laatste opdracht ziet er als volgt uit: sqlcmd-S {Interface IP}, 1401-U SA-P "Strong! Passw0rd".</li>Hierna moeten stap 3-4 van de huidige documentatie identiek zijn. </li></ul> |
| **3.** |Vernieuwen| Incrementele wijzigingen in blobs die zijn hersteld via **vernieuwen** , worden niet ondersteund |Voor BLOB-eind punten kunnen gedeeltelijke updates van blobs na een vernieuwing ertoe leiden dat de updates niet worden geüpload naar de Cloud. Bijvoorbeeld volg orde van acties zoals:<ul><li>Maak een BLOB in de Cloud. Of verwijder een eerder geüploade blob van het apparaat.</li><li>Vernieuw de blob van de Cloud naar het apparaat met behulp van de functie voor vernieuwen.</li><li>Werk slechts een deel van de BLOB bij met Azure SDK REST Api's.</li></ul>Deze acties kunnen ertoe leiden dat de bijgewerkte secties van de BLOB niet worden bijgewerkt in de Cloud. <br>**Tijdelijke oplossing**: gebruik hulpprogram ma's zoals Robocopy of een gewone bestands kopie via Verkenner of opdracht regel om de hele blobs te vervangen.|
|**4.**|Beperking|Als er tijdens het beperken geen nieuwe schrijf bewerkingen zijn toegestaan in het apparaat, mislukt de schrijf bewerkingen die zijn uitgevoerd door de NFS-client met de fout ' permission denied '.| De volgende fout wordt weer gegeven:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: kan directory ' test ' niet maken: de machtiging is geweigerd|
|**5.**|Opname Blob Storage|Wanneer u AzCopy versie 10 gebruikt voor inslikken van Blob-opslag, voert u AzCopy uit met het volgende argument: `Azcopy <other arguments> --cap-mbps 2000`| Als deze limieten niet worden verstrekt voor AzCopy, kan het mogelijk zijn om een groot aantal aanvragen naar het apparaat te verzenden en om problemen met de service op te lossen.|
|**6.**|Gelaagde opslag accounts|Het volgende is van toepassing wanneer u gelaagde opslag accounts gebruikt:<ul><li> Alleen blok-blobs worden ondersteund. Pagina-blobs worden niet ondersteund.</li><li>Er is geen moment opname-of Copy API-ondersteuning.</li><li> De inslikken van een Hadoop-werk belasting `distcp` wordt niet ondersteund omdat de Kopieer bewerking intensief wordt gebruikt.</li></ul>||
|**7.**|NFS-share verbinding|Als er meerdere processen naar dezelfde share worden gekopieerd en het `nolock` kenmerk niet wordt gebruikt, worden er mogelijk fouten weer geven tijdens de kopie.|Het `nolock` kenmerk moet worden door gegeven aan de koppelings opdracht om bestanden te kopiëren naar de NFS-share. Bijvoorbeeld: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**achtste.**|Kubernetes-cluster|Wanneer u een update toepast op uw apparaat waarop een kubernetes-cluster wordt uitgevoerd, worden de virtuele machines van kubernetes opnieuw opgestart en opnieuw opgestart. In dit geval worden alleen de peulen die zijn geïmplementeerd met de opgegeven replica's automatisch hersteld na een update.  |Als u afzonderlijke peulen buiten een replicatie controller hebt gemaakt zonder een replicaset op te geven, wordt deze peul niet automatisch hersteld na het bijwerken van het apparaat. U moet deze peul herstellen.<br>Een replicaset vervangt de peulen die worden verwijderd of beëindigd om een wille keurige reden, zoals een storing in het knoop punt of een onderbreking van de upgrade van knoop punten. Daarom wordt u aangeraden een replicaset te gebruiken, zelfs als uw toepassing slechts één Pod vereist.|
|**9,4.**|Kubernetes-cluster|Kubernetes op Azure Stack Edge Pro wordt alleen ondersteund met helm v3 of hoger. Voor meer informatie gaat u naar [Veelgestelde vragen: verwijderen van Tiller](https://v3.helm.sh/docs/faq/).|
|**6.**|Kubernetes met Azure Arc |Voor de GA-release is Azure Arc enabled Kubernetes bijgewerkt van versie 0.1.18 naar 0.2.9. Omdat de Azure Arc enabled Kubernetes-update niet wordt ondersteund op Azure Stack edge-apparaat, moet u de Kubernetes van Azure-Arc opnieuw implementeren.|Volg deze stappen:<ol><li>[Software-en Kubernetes-updates voor apparaten Toep assen](azure-stack-edge-gpu-install-update.md).</li><li>Maak verbinding met de [Power shell-interface van het apparaat](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Verwijder de bestaande Azure Arc-agent. Type: `Remove-HcsKubernetesAzureArcAgent` .</li><li>Implementeer [Azure Arc op een nieuwe resource](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Gebruik geen bestaande Azure Arc-resource.</li></ol>|
|**9.**|Kubernetes met Azure Arc|Implementaties van Azure Arc worden niet ondersteund als webproxy is geconfigureerd op uw Azure Stack Edge Pro-apparaat.||
|**12.**|Kubernetes |Poort 31000 is gereserveerd voor Kubernetes-dash board. Op dezelfde manier worden in de standaard configuratie de IP-adressen 172.28.0.1 en 172.28.0.10 gereserveerd voor respectievelijk de Kubernetes-service en de basis-DNS-service.|Gebruik geen gereserveerde Ip's.|
|**13.**|Kubernetes |Kubernetes ondersteunt momenteel geen LoadBalancer-Services met meerdere protocollen. Bijvoorbeeld een DNS-service die luistert op TCP en UDP. |Om deze beperking van Kubernetes met MetalLB te omzeilen, kunnen twee services (een voor TCP, een voor UDP) worden gemaakt op dezelfde pod selector. Deze services gebruiken dezelfde uitwisselings sleutel en spec. loadBalancerIP om hetzelfde IP-adres te delen. Ip's kunnen ook worden gedeeld als u meer services hebt dan beschik bare IP-adressen. <br> Zie [IP-adres delen](https://metallb.universe.tf/usage/#ip-address-sharing)voor meer informatie.|
|**15.**|Kubernetes-cluster|Bestaande Azure IoT Edge Marketplace-modules kunnen wijzigingen vereisen om te worden uitgevoerd op IoT Edge op Azure Stack edge-apparaat.|Zie voor meer informatie Azure IoT Edge modules van Marketplace aanpassen zodat ze op Azure Stack edge-apparaat kunnen worden uitgevoerd.<!-- insert link-->|
|**15.**|Kubernetes |Op bestanden gebaseerde BIND koppelingen worden niet ondersteund met Azure IoT Edge op Kubernetes op Azure Stack edge-apparaat.|IoT Edge maakt gebruik van een Vertaal laag om `ContainerCreate` Opties om te zetten in Kubernetes-constructies. Het maken `Binds` van Maps to hostpath Directory en daarom op bestanden gebaseerde BIND koppelingen kunnen niet worden gebonden aan paden in IOT Edge containers. Als dat mogelijk is, wijst u de bovenliggende map toe.|
|**18.**|Kubernetes |Als u uw eigen certificaten voor IoT Edge en deze toevoegt op uw Azure Stack edge-apparaat nadat de berekening op het apparaat is geconfigureerd, worden de nieuwe certificaten niet opgehaald.|U kunt dit probleem omzeilen door de certificaten te uploaden voordat u Compute op het apparaat configureert. Als de compute al is geconfigureerd, [maakt u verbinding met de Power shell-interface van het apparaat en voert u IOT Edge opdrachten uit](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Opnieuw opstarten `iotedged` en `edgehub` peulen.|
|**Nr.**|Certificaten |In bepaalde gevallen kan de status van het certificaat in de lokale gebruikers interface enkele seconden duren voordat deze wordt bijgewerkt. |De volgende scenario's in de lokale gebruikers interface kunnen worden beïnvloed.<ul><li>De kolom **status** op de pagina **certificaten** .</li><li>De tegel **beveiliging** op de pagina **aan de slag** .</li><li>**Configuratie** tegel op de pagina **overzicht** .</li></ul>  |
|**Nr.**|IoT Edge |Modules die via IoT Edge worden geïmplementeerd, kunnen geen gebruik van het doelnet werken. | |
|**18,0.**|Compute + Kubernetes |Compute/Kubernetes biedt geen ondersteuning voor NTLM-webproxy. ||
|**19.**|Compute + web proxy + update |Als u Compute hebt geconfigureerd met web proxy, kan de compute-update mislukken. |U wordt aangeraden reken kracht vóór de update uit te scha kelen. |

<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->




## <a name="next-steps"></a>Volgende stappen

- [De implementatie van Azure Stack Edge Pro-apparaat voorbereiden met GPU](azure-stack-edge-gpu-deploy-prep.md)