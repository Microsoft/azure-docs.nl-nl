---
title: Voor bereiding van zelfstandige cluster implementatie
description: Documentatie met betrekking tot het voorbereiden van de omgeving en het maken van de cluster configuratie, moet worden overwogen voordat een cluster wordt geïmplementeerd dat is bedoeld voor het verwerken van een productiewerk belasting.
ms.topic: conceptual
ms.date: 9/11/2018
ms.openlocfilehash: 18b8b0ce8c0e877bf9dd274596b19f85b1febe12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790949"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>De implementatie van uw Service Fabric zelfstandige cluster plannen en voorbereiden

<a id="preparemachines"></a>Voer de volgende stappen uit voordat u het cluster maakt.

## <a name="plan-your-cluster-infrastructure"></a>De cluster infrastructuur plannen
U staat op het punt een Service Fabric cluster te maken op de computers die u ' Own ' hebt, zodat u kunt bepalen welke soorten fouten het cluster moet overlaten. Hebt u bijvoorbeeld aparte energie lijnen of Internet verbindingen nodig die aan deze computers zijn verstrekt? Houd ook rekening met de fysieke beveiliging van deze computers. Waar bevinden de machines zich en wie moet er toegang toe hebben? Nadat u deze beslissingen hebt genomen, kunt u de computers logisch toewijzen aan verschillende fout domeinen (Zie de volgende stap). De planning van de infra structuur voor productie clusters is meer betrokken dan voor test clusters.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Het aantal fout domeinen en upgrade domeinen bepalen
Een [ *fout domein* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) is een fysieke fout eenheid en is rechtstreeks gerelateerd aan de fysieke infra structuur in de data centers. Een fout domein bestaat uit hardware-onderdelen (computers, switches, netwerken en meer) die een Single Point of Failure delen. Hoewel er geen 1:1-toewijzing is tussen fout domeinen en racks, kunt u zonder problemen praten elk rek beschouwen als een fout domein.

Wanneer u Fd's in ClusterConfig.jsopgeeft, kunt u de naam voor elke FD kiezen. Service Fabric ondersteunt hiërarchische Fd's, zodat u uw infrastructuur topologie kunt weer spie gelen.  De volgende Fd's zijn bijvoorbeeld geldig:

* "faultDomain": "FD:/Room1/Rack1/machine1"
* "faultDomain": "FD:/FD1"
* "faultDomain": "FD:/Room1/Rack1/PDU1/M1"

Een *upgrade domein* (UD) is een logische eenheid van knoop punten. Tijdens Service Fabric georganisatiede upgrades (een toepassings upgrade of een upgrade van een cluster) worden alle knoop punten in een UD omlaag gezet om de upgrade uit te voeren terwijl er knoop punten in andere UDs beschikbaar blijven zijn om aanvragen te kunnen verwerken. De firmware-upgrades die u op uw computers uitvoert, voldoen niet aan de UDs, dus u moet deze op één computer tegelijk uitvoeren.

De eenvoudigste manier om deze concepten te bedenken is om Fd's te beschouwen als de eenheid van ongeplande fout en UDs als eenheid voor gepland onderhoud.

Wanneer u een UDs in ClusterConfig.jsopgeeft, kunt u de naam voor elke UD kiezen. De volgende namen zijn bijvoorbeeld geldig:

* "upgrade Domain": "UD0"
* "upgrade Domain": "UD1A"
* "upgrade Domain": "DomainRed"
* "upgrade Domain": "Blue"

Zie [een service Fabric cluster beschrijven](service-fabric-cluster-resource-manager-cluster-description.md)voor meer informatie over Fd's en UDS.

Een cluster in productie moet ten minste drie Fd's omvatten om te worden ondersteund in een productie omgeving, als u volledige controle hebt over het onderhoud en beheer van de knoop punten, dat wil zeggen dat u verantwoordelijk bent voor het bijwerken en vervangen van machines. Voor clusters die in omgevingen worden uitgevoerd (dat wil zeggen, Amazon Web Services VM-instanties) waarbij u geen volledige controle over de computers hebt, moet u Mini maal vijf Fd's in uw cluster hebben. Elke FD kan een of meer knoop punten bevatten. Dit is om te voor komen dat problemen die worden veroorzaakt door computer upgrades en-updates, afhankelijk van hun tijds duur, de uitvoering van toepassingen en services in clusters kunnen belemmeren.

## <a name="determine-the-initial-cluster-size"></a>De initiële cluster grootte bepalen

Over het algemeen wordt het aantal knoop punten in uw cluster bepaald op basis van uw bedrijfs behoeften, dat wil zeggen, hoeveel Services en containers op het cluster worden uitgevoerd en hoeveel bronnen u nodig hebt om uw workloads te houden. Voor productie clusters kunt u het beste ten minste vijf knoop punten in uw cluster hebben, met een spanning van 5 Fd's. Zoals hierboven is beschreven, kunt u, als u volledige controle over uw knoop punten hebt, drie Fd's beslaan, drie knoop punten ook de taak laten uitvoeren.

Test clusters waarop stateful werk belastingen worden uitgevoerd, moeten drie knoop punten hebben, terwijl alleen clusteren waarvoor stateless werk belastingen worden uitgevoerd, slechts één knoop punt nodig hebben. U moet er ook voor kunnen bepalen dat er meer dan één knoop punt op een bepaalde computer. In een productie omgeving ondersteunt Service Fabric echter slechts één knoop punt per fysieke of virtuele machine.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>De computers voorbereiden die fungeren als knoop punten

Hier vindt u de aanbevolen specificaties voor computers in een Service Fabric cluster:

* Mini maal 16 GB RAM-geheugen
* Mini maal 40 GB beschik bare schijf ruimte
* Een 4-core of meer CPU
* Connectiviteit met een beveiligd netwerk of netwerken voor alle computers
* Windows Server-besturings systeem geïnstalleerd (geldige versies: 2012 R2, 2016, 1709 of 1803). Service Fabric versie 6.4.654.9590 en hoger ondersteunt ook server 2019 en 1809.
* [.NET Framework 4.5.1 of hoger](https://www.microsoft.com/download/details.aspx?id=40773), volledige installatie
* [Windows PowerShell 3.0](/powershell/scripting/windows-powershell/install/installing-windows-powershell)
* De [RemoteRegistry-service](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754820(v=ws.11)) moet op alle computers worden uitgevoerd
* **Service Fabric installatie station moet een NTFS-bestands systeem zijn**
* ***Prestatie logboeken* voor windows-Services & waarschuwingen en *Windows-gebeurtenis logboek* moet [zijn ingeschakeld](/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc755249(v=ws.11))**.

> [!IMPORTANT]
> De Cluster beheerder die het cluster implementeert en configureert, moet over [beheerders bevoegdheden](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) beschikken voor elk van de computers. U kunt Service Fabric niet installeren op een domeincontroller.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Het zelfstandige pakket voor de Service Fabric voor Windows Server downloaden
[Down load link-service Fabric standalone package-Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) en pak het pakket uit, hetzij naar een implementatie computer die geen deel uitmaakt van het cluster, of op een van de computers die deel uitmaken van het cluster.

## <a name="modify-cluster-configuration"></a>Cluster configuratie wijzigen
Als u een zelfstandig cluster wilt maken, moet u een zelfstandige cluster configuratie maken ClusterConfig.jsin het bestand, waarin de specificatie van het cluster wordt beschreven. U kunt het configuratie bestand baseren op de sjablonen die op de onderstaande koppeling zijn gevonden. <br>
[Zelfstandige cluster configuraties](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Zie [configuratie-instellingen voor zelfstandige Windows-clusters](service-fabric-cluster-manifest.md)voor meer informatie over de secties in dit bestand.

Open een van de ClusterConfig.jsop bestanden van het pakket dat u hebt gedownload en wijzig de volgende instellingen:

| **Configuratie-instelling** | **Beschrijving** |
| --- | --- |
| **NodeTypes** |Met knooppunt typen kunt u uw cluster knooppunten scheiden in verschillende groepen. Een cluster moet ten minste één NodeType hebben. Alle knoop punten in een groep hebben de volgende algemene kenmerken: <br> **Naam** : dit is de naam van het knooppunt type. <br>**Eindpunt poorten** : Dit zijn verschillende benoemde eind punten (poorten) die zijn gekoppeld aan dit knooppunt type. U kunt elk gewenst poort nummer gebruiken, zolang ze niet conflicteren met iets anders in dit manifest en niet al worden gebruikt door een andere toepassing die wordt uitgevoerd op de machine/VM. <br> **Eigenschappen van plaatsing** : deze beschrijven de eigenschappen voor dit knooppunt type die u als plaatsings beperkingen voor de systeem services of uw services gebruikt. Deze eigenschappen zijn door de gebruiker gedefinieerde sleutel-waardeparen die extra meta gegevens voor een bepaald knoop punt bieden. Voor beelden van knooppunt eigenschappen zijn of het knoop punt een harde schijf of een grafische kaart heeft, het aantal aandrijf assen op de vaste schijf, kernen en andere fysieke eigenschappen. <br> **Capaciteit** : capaciteit van knoop punten definiëren de naam en het bedrag van een bepaalde resource dat een bepaald knoop punt beschikbaar is voor gebruik. Een knoop punt kan bijvoorbeeld bepalen dat het capaciteit heeft voor een metriek met de naam ' MemoryInMb ' en dat deze 2048 MB standaard beschikbaar is. Deze capaciteit wordt tijdens runtime gebruikt om ervoor te zorgen dat services waarvoor bepaalde hoeveel heden resources zijn vereist, worden geplaatst op de knoop punten waarop deze resources beschikbaar zijn in de vereiste aantallen.<br>**IsPrimary** -als u meer dan één NodeType hebt gedefinieerd, moet u ervoor zorgen dat slechts één van beide is ingesteld op primair met de waarde *True*, waar de systeem services worden uitgevoerd. Alle andere knooppunt typen moeten worden ingesteld op de waarde *False* |
| **Knooppunten** |Dit zijn de Details voor elk van de knoop punten die deel uitmaken van het cluster (knooppunt type, knooppunt naam, IP-adres, fout domein en upgrade domein van het knoop punt). De computers waarop u het cluster wilt maken, moeten hier worden vermeld met hun IP-adressen. <br> Als u hetzelfde IP-adres voor alle knoop punten gebruikt, wordt er een cluster met één doos gemaakt, dat u kunt gebruiken voor test doeleinden. Gebruik geen clusters met één doos voor het implementeren van productie werkbelastingen. |

Nadat de cluster configuratie alle instellingen voor de omgeving heeft geconfigureerd, kan deze worden getest op de cluster omgeving (stap 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Configuratie van de omgeving

Wanneer een cluster beheerder een Service Fabric zelfstandige cluster configureert, moet de omgeving worden ingesteld met de volgende criteria: <br>
1. De gebruiker die het cluster maakt, moet over beheerders rechten beschikken voor alle computers die als knoop punten in het cluster configuratie bestand worden weer gegeven.
2. De computer van waaruit het cluster wordt gemaakt, evenals elke cluster knooppunt machine moet:
   * Service Fabric SDK verwijderen
   * Service Fabric runtime verwijderen
   * De Windows Firewall service (MpsSvc) is ingeschakeld
   * De Remote Registry-service (extern REGI ster) ingeschakeld hebben
   * Het delen van bestanden (SMB) is ingeschakeld
   * De benodigde poorten zijn geopend, op basis van de configuratie poorten van het cluster
   * De benodigde poorten zijn geopend voor de Windows SMB-en Remote Registry-service: 135, 137, 138, 139 en 445
   * Een netwerk verbinding met elkaar hebben
3. Geen van de cluster knooppunt computers moet een domein controller zijn.
4. Als het cluster dat moet worden geïmplementeerd een veilig cluster is, controleert u of de vereiste beveiligings vereisten aanwezig zijn en goed zijn geconfigureerd op basis van de configuratie.
5. Als de cluster machines niet via internet toegankelijk zijn, stelt u het volgende in de cluster configuratie in:
   * Telemetrie uitschakelen: onder *Eigenschappen* set *"enableTelemetry": False*
   * Schakel de automatische infrastructuur versie voor het downloaden van & meldingen uit dat de huidige cluster versie bijna de ondersteuning ondervindt: Klik onder *Eigenschappen* instellen op *' fabricClusterAutoupgradeEnabled ': False*
   * Als het netwerk Internet toegang is beperkt tot allowlisted-domeinen, zijn de onderstaande domeinen vereist voor automatische upgrade: go.microsoft.com download.microsoft.com

6. Stel de juiste Service Fabric anti virus-uitsluitingen in:

| **Uitgesloten mappen van anti virus** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (uit de cluster configuratie) |
| FabricLogRoot (uit de cluster configuratie) |

| **Uitgesloten processen van anti virus** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>Omgeving valideren met behulp van de testconfiguration-script
Het TestConfiguration.ps1 script kan worden gevonden in het zelfstandige pakket. Dit wordt gebruikt als Best Practices Analyzer voor het valideren van enkele van de bovenstaande criteria en moet worden gebruikt als een Sanity om te controleren of een cluster kan worden geïmplementeerd in een bepaalde omgeving. Als er een fout optreedt, raadpleegt u de lijst onder [omgeving instellen](service-fabric-cluster-standalone-deployment-preparation.md) voor het oplossen van problemen.

Dit script kan worden uitgevoerd op elke computer met beheerders toegang tot alle computers die worden vermeld als knoop punten in het cluster configuratie bestand. De computer waarop dit script wordt uitgevoerd, hoeft geen deel uit te maken van het cluster.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True
```

Momenteel valideert deze configuratie test module de beveiligings configuratie niet, zodat deze onafhankelijk moet worden uitgevoerd.

> [!NOTE]
> We nemen voortdurend verbeteringen aan om deze module robuuster te maken, dus als er sprake is van een defecte of ontbrekende situatie die momenteel niet wordt onderschept door de testconfiguration, moet u ons laten weten wat de [ondersteunings kanalen](./service-fabric-support.md)zijn.
>
>

## <a name="next-steps"></a>Volgende stappen
* [Een zelfstandig cluster maken dat wordt uitgevoerd in Windows Server](service-fabric-cluster-creation-for-windows-server.md)
