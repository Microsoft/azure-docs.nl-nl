---
title: De knooppuntconfiguratie aanpassen voor Azure Kubernetes Service (AKS)-knooppuntgroepen (preview)
description: Meer informatie over het aanpassen van de configuratie op Azure Kubernetes Service (AKS)-clusterknooppunten en knooppuntgroepen.
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: c2173118b58ca92d69286fb36014872c19058bd6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779971"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>Knooppuntconfiguratie aanpassen voor Azure Kubernetes Service (AKS)-knooppuntgroepen (preview)

Door uw knooppuntconfiguratie aan te passen, kunt u de instellingen van uw besturingssysteem of de kubelet-parameters configureren of afstemmen op de behoeften van de workloads. Wanneer u een AKS-cluster maakt of een knooppuntgroep aan uw cluster toevoegt, kunt u een subset van veelgebruikte instellingen voor het besturingssysteem en kubelet aanpassen. Als u instellingen buiten deze subset wilt configureren, gebruikt u een daemon die is ingesteld om de benodigde configuraties aan te passen zonder AKS-ondersteuning voor uw [knooppunten te verliezen.](support-policies.md#shared-responsibility)

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>De `CustomNodeConfigPreview` preview-functie registreren

Als u een AKS-cluster of -knooppuntgroep wilt maken waarmee de kubelet-parameters of besturingssysteeminstellingen kunnen worden aangepast, moet u de `CustomNodeConfigPreview` functievlag inschakelen voor uw abonnement.

Registreer de `CustomNodeConfigPreview` functievlag met behulp van [de opdracht az feature register,][az-feature-register] zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

Het duurt enkele minuten voordat de status Geregistreerd *we weergeven.* Controleer de registratiestatus met behulp van [de opdracht az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

Wanneer u klaar bent, vernieuwt u de registratie van de resourceprovider *Microsoft.ContainerService* met behulp van de [opdracht az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>De CLI-extensie aks-preview installeren

Als u een AKS-cluster of een knooppuntgroep wilt maken die de kubelet-parameters of os-instellingen kan aanpassen, hebt u de nieuwste Azure *CLI-extensie aks-preview* nodig. Installeer de *Azure CLI-extensie aks-preview* met behulp van de [opdracht az extension add.][az-extension-add] U kunt ook beschikbare updates installeren met behulp van [de opdracht az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>Aangepaste knooppuntconfiguratie gebruiken

### <a name="kubelet-custom-configuration"></a>Aangepaste Kubelet-configuratie

De ondersteunde Kubelet-parameters en geaccepteerde waarden worden hieronder vermeld.

| Parameter | Toegestane waarden/interval | Standaard | Beschrijving |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | none, static | geen | Met het statische beleid kunnen containers in [Gegarandeerde pods](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) met CPU-aanvragen met gehele getallen toegang krijgen tot exclusieve CPU's op het knooppunt. |
| `cpuCfsQuota` | de waarde True, false | true |  Het afdwingen van CPU CFS-quota in-/uitschakelen voor containers die CPU-limieten opgeven. | 
| `cpuCfsQuotaPeriod` | Interval in milliseconden (ms) | `100ms` | Hiermee stelt u de waarde voor de CPU CFS-quotumperiode in. | 
| `imageGcHighThreshold` | 0-100 | 85 | Het percentage schijfgebruik waarna de garbage collection van de afbeelding altijd wordt uitgevoerd. Minimaal schijfgebruik dat **garbage** collection activeert. Als u garbageverzameling van afbeeldingen wilt uitschakelen, stelt u in op 100. | 
| `imageGcLowThreshold` | 0-100, niet hoger dan `imageGcHighThreshold` | 80 | Het percentage schijfgebruik waarvoor de garbageverzameling van de afbeelding nooit wordt uitgevoerd. Minimaal schijfgebruik dat **garbage** collection kan activeren. |
| `topologyManagerPolicy` | none, best-effort, restricted, single-numa-node | geen | Optimaliseer NUMA-knooppuntuitlijning. Meer informatie kunt u [hier bekijken.](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/) Alleen kubernetes v1.18+. |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | Geen | Toegestane lijst met onveilige sysctls of onveilige sysctl-patronen. | 

### <a name="linux-os-custom-configuration"></a>Aangepaste configuratie voor Linux-besturingssysteem

Hieronder vindt u de ondersteunde besturingssysteeminstellingen en geaccepteerde waarden.

#### <a name="file-handle-limits"></a>Limieten voor bestandsing handle

Wanneer u veel verkeer af te bedienen, is het gebruikelijk dat het verkeer dat u aanbesteed afkomstig is van een groot aantal lokale bestanden. U kunt de onderstaande kernelinstellingen en ingebouwde limieten aanpassen zodat u meer kunt verwerken, ten koste van wat systeemgeheugen.

| Instelling | Toegestane waarden/interval | Standaard | Beschrijving |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192 - 12000500 | 709620 | Het maximale aantal bestandsing handles dat door de Linux-kernel wordt toegewezen, door deze waarde te verhogen, kunt u het maximum aantal toegestane geopende bestanden verhogen. |
| `fs.inotify.max_user_watches` | 781250 - 2097152 | 1048576 | Het maximum aantal bestandsde watchers dat door het systeem is toegestaan. Elk *horloge* is ongeveer 90 bytes op een 32-bits kernel en ongeveer 160 bytes op een 64-bits kernel. | 
| `fs.aio-max-nr` | 65536 - 6553500 | 65536 | De aio-nr toont het huidige systeembrede aantal asynchrone IO-aanvragen. Met aio-max-nr kunt u de maximale waarde wijzigen waar aio-nr in kan groeien. |
| `fs.nr_open` | 8192 - 20000500 | 1048576 | Het maximum aantal bestands-handles dat een proces kan toewijzen. |


#### <a name="socket-and-network-tuning"></a>Afstemmen van socket en netwerk

Voor agentknooppunten, waarvan wordt verwacht dat ze zeer grote aantallen gelijktijdige sessies verwerken, kunt u de subset van TCP- en netwerkopties hieronder gebruiken die u per knooppuntgroep kunt aanpassen. 

| Instelling | Toegestane waarden/interval | Standaard | Beschrijving |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096 - 3240000 | 16384 | Maximum aantal verbindingsaanvragen dat in de wachtrij kan worden geplaatst voor een bepaalde luisterende socket. Een bovengrens voor de waarde van de backlogparameter die wordt doorgegeven aan de [functie listen(2).](http://man7.org/linux/man-pages/man2/listen.2.html) Als het argument voor achterstand groter is dan de , wordt het op de stille volgorde `somaxconn` afgekapt tot deze limiet.
| `net.core.netdev_max_backlog` | 1000 - 3240000 | 1000 | Maximum aantal pakketten, in de wachtrij geplaatst aan de invoerzijde, wanneer de interface sneller pakketten ontvangt dan kernel ze kan verwerken. |
| `net.core.rmem_max` | 212992 - 134217728 | 212992 | De maximale grootte van de ontvangstsockockerbuffer in bytes. |
| `net.core.wmem_max` | 212992 - 134217728 | 212992 | De maximale grootte van de verzendsockockerbuffer in bytes. | 
| `net.core.optmem_max` | 20480 - 4194304 | 20480 | Maximale grootte van aanvullende buffer (optie geheugenbuffer) toegestaan per socket. Socketoptiegeheugen wordt in een paar gevallen gebruikt voor het opslaan van extra structuren met betrekking tot het gebruik van de socket. | 
| `net.ipv4.tcp_max_syn_backlog` | 128 - 3240000 | 16384 | Het maximum aantal verbindingsaanvragen in de wachtrij dat nog steeds geen bevestiging van de verbindingsclient heeft ontvangen. Als dit aantal wordt overschreden, begint de kernel aanvragen te laten vallen. |
| `net.ipv4.tcp_max_tw_buckets` | 8000 - 1440000 | 32768 | Maximaal aantal `timewait` sockets dat tegelijkertijd door het systeem wordt vastgehouden. Als dit aantal wordt overschreden, wordt de time-wait socket onmiddellijk vernietigd en wordt er een waarschuwing afgedrukt. |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | De tijdsduur dat een zwevende verbinding (waarnaar niet meer wordt verwezen door een toepassing) in de FIN_WAIT_2 blijft voordat deze wordt afgebroken aan de lokale kant. |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | Hoe vaak TCP berichten `keepalive` verzendt wanneer `keepalive` is ingeschakeld. |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | Hoeveel tests `keepalive` TCP verzendt, totdat wordt besloten dat de verbinding wordt verbroken. |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | Hoe vaak de tests worden verzonden. Vermenigvuldigd met de tijd die nodig is om een verbinding af te maken die niet `tcp_keepalive_probes` reageert, nadat de tests zijn gestart. | 
| `net.ipv4.tcp_tw_reuse` | 0 of 1 | 0 | Toestaan om `TIME-WAIT` sockets opnieuw te gebruiken voor nieuwe verbindingen wanneer het vanuit protocol oogpunt veilig is. | 
| `net.ipv4.ip_local_port_range` | Eerste: 1024 - 60999 en Laatste: 32768 - 65000] | Eerste: 32768 en Laatste: 60999 | Het lokale poortbereik dat wordt gebruikt door TCP- en UDP-verkeer om de lokale poort te kiezen. Bestaan uit twee getallen: het eerste getal is de eerste lokale poort die is toegestaan voor TCP- en UDP-verkeer op het agent-knooppunt, het tweede is het laatste lokale poortnummer. | 
| `net.ipv4.neigh.default.gc_thresh1`|  128 - 80000 | 4096 | Minimum aantal vermeldingen dat zich in de ARP-cache kan vinden. Garbageverzameling wordt niet geactiveerd als het aantal vermeldingen lager is dan deze instelling. | 
| `net.ipv4.neigh.default.gc_thresh2`|  512 - 90000 | 8192 | Het maximum aantal vermeldingen dat in de ARP-cache mag zijn. Deze instelling is misschien wel het belangrijkste, omdat de ARP-garbageverzameling ongeveer 5 seconden na het bereiken van dit zachte maximum wordt geactiveerd. |
| `net.ipv4.neigh.default.gc_thresh3`|  1024 - 100000 | 16384 | Hard maximum aantal vermeldingen in de ARP-cache. |
| `net.netfilter.nf_conntrack_max` | 131072 - 589824 | 131072 | `nf_conntrack` is een module die verbindingsgegevens voor NAT in Linux bij houdt. De `nf_conntrack` module gebruikt een hashtabel om de *vastgelegde verbindingsrecord* van het TCP-protocol vast te leggen. `nf_conntrack_max` is het maximum aantal knooppunten in de hash-tabel, dat wil zeggen, het maximum aantal verbindingen dat wordt ondersteund door de module of de grootte van de tabel voor het bijhouden `nf_conntrack` van verbindingen. | 
| `net.netfilter.nf_conntrack_buckets` | 65536 - 147456 | 65536 | `nf_conntrack` is een module die verbindingsgegevens voor NAT in Linux bij houdt. De `nf_conntrack` module gebruikt een hashtabel om de *vastgelegde verbindingsrecord* van het TCP-protocol vast te leggen. `nf_conntrack_buckets` is de grootte van de hash-tabel. | 

#### <a name="worker-limits"></a>Werkrollimieten

Net als bij bestandsdescriptorlimieten wordt het aantal werkwerkers of threads dat een proces kan maken, beperkt door zowel een kernelinstelling als gebruikerslimieten. De gebruikerslimiet voor AKS is onbeperkt. 

| Instelling | Toegestane waarden/interval | Standaard | Beschrijving |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | Processen kunnen werkthreads maken. Het maximum aantal threads dat kan worden gemaakt, wordt ingesteld met de kernelinstelling `kernel.threads-max` . | 

#### <a name="virtual-memory"></a>Virtueel geheugen

De onderstaande instellingen kunnen worden gebruikt om de werking van het subsysteem van het virtuele geheugen (VM) van de Linux-kernel en de `writeout` van vervuilde gegevens op schijf af te stemmen.

| Instelling | Toegestane waarden/interval | Standaard | Beschrijving |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530 - 262144 | 65530 | Dit bestand bevat het maximum aantal geheugenkaartgebieden dat een proces kan hebben. Geheugenkaartgebieden worden gebruikt als neveneffect van het aanroepen van , rechtstreeks door , en , en ook bij `malloc` het laden van gedeelde `mmap` `mprotect` `madvise` bibliotheken. | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | Deze percentagewaarde bepaalt de neiging van de kernel om het geheugen vrij te maken, dat wordt gebruikt voor het in deaching van map- en inode-objecten. |
| `vm.swappiness` | 0 - 100 | 60 | Dit besturingselement wordt gebruikt om te definiëren hoe agressief de kernel geheugenpagina's wisselt. Hogere waarden verhogen de agressiviteit, lagere waarden verlagen de hoeveelheid wisseling. Met de waarde 0 wordt de kernel geïnstrueerd om het wisselen pas te starten als de hoeveelheid vrije pagina's en bestandspagina's kleiner is dan de boven watermerk in een zone. | 
| `swapFileSizeMB` | 1 MB: grootte van de [tijdelijke schijf](../virtual-machines/managed-disks-overview.md#temporary-disk) (/dev/sdb) | Geen | SwapFileSizeMB geeft de grootte in MB van een wisselbestand aan op de agentknooppunten van deze knooppuntgroep. | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [Transparent Hugepages](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) is een Linux-kernelfunctie die is bedoeld om de prestaties te verbeteren door efficiënter gebruik te maken van de hardware voor geheugentoewijzing van uw processor. Wanneer deze functie is ingeschakeld, probeert de kernel waar mogelijk toe te wijzen en ontvangt een Linux-proces pagina's van 2 MB als de regio op natuurlijke wijze `hugepages` `mmap` is uitgelijnd. In bepaalde gevallen wanneer `hugepages` systeembreed is ingeschakeld, kunnen toepassingen uiteindelijk meer geheugenresources toewijzen. Een toepassing kan een grote regio zijn, maar slechts één byte ervan raken. In dat geval kan zonder goede reden een pagina van 2 MB worden toegewezen in plaats van een pagina `mmap` van 4k. Dit scenario is de reden waarom het mogelijk is om het hele systeem uit te schakelen `hugepages` of ze alleen binnen regio's te `MADV_HUGEPAGE madvise` hebben. | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | Deze waarde bepaalt of de kernel agressief gebruik moet maken van geheugencompactie om meer beschikbaar te `hugepages` maken. | 

> [!IMPORTANT]
> Voor een eenvoudige zoek- en leesbaarheid worden de instellingen van het besturingssysteem in dit document weergegeven met hun naam, maar moeten ze worden toegevoegd aan het configuratie-JSON-bestand of de AKS-API met behulp van de hoofdletterconventie [voor camelCase.](/dotnet/standard/design-guidelines/capitalization-conventions)

Maak een `kubeletconfig.json` bestand met de volgende inhoud:

```json
{
 "cpuManagerPolicy": "static",
 "cpuCfsQuota": true,
 "cpuCfsQuotaPeriod": "200ms",
 "imageGcHighThreshold": 90,
 "imageGcLowThreshold": 70,
 "topologyManagerPolicy": "best-effort",
 "allowedUnsafeSysctls": [
  "kernel.msg*",
  "net.*"
],
 "failSwapOn": false
}
```
Maak een `linuxosconfig.json` bestand met de volgende inhoud:

```json
{
 "transparentHugePageEnabled": "madvise",
 "transparentHugePageDefrag": "defer+madvise",
 "swapFileSizeMB": 1500,
 "sysctls": {
  "netCoreSomaxconn": 163849,
  "netIpv4TcpTwReuse": true,
  "netIpv4IpLocalPortRange": "32000 60000"
 }
}
```

Maak een nieuw cluster waarin de kubelet- en besturingssysteemconfiguraties worden opgegeven met behulp van de JSON-bestanden die u in de vorige stap hebt gemaakt. 

> [!NOTE]
> Wanneer u een cluster maakt, kunt u de kubelet-configuratie, besturingssysteemconfiguratie of beide opgeven. Als u een configuratie opgeeft bij het maken van een cluster, wordt die configuratie alleen toegepast op de knooppunten in de initiële knooppuntgroep. Instellingen die niet zijn geconfigureerd in het JSON-bestand behouden de standaardwaarde.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

Voeg een nieuwe knooppuntgroep toe die de Kubelet-parameters opgeeft met behulp van het JSON-bestand dat u hebt gemaakt.

> [!NOTE]
> Wanneer u een knooppuntgroep toevoegt aan een bestaand cluster, kunt u de kubelet-configuratie, besturingssysteemconfiguratie of beide opgeven. Als u een configuratie opgeeft bij het toevoegen van een knooppuntgroep, wordt deze configuratie alleen toegepast op de knooppunten in de nieuwe knooppuntgroep. Instellingen die niet zijn geconfigureerd in het JSON-bestand behouden de standaardwaarde.

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>Volgende stappen

- Meer [informatie over het configureren van uw AKS-cluster.](cluster-configuration.md)
- Meer informatie over [het upgraden van de knooppuntafbeeldingen](node-image-upgrade.md) in uw cluster.
- Zie [Een AKS Azure Kubernetes Service cluster upgraden](upgrade-cluster.md) voor meer informatie over het upgraden van uw cluster naar de nieuwste versie van Kubernetes.
- Zie de lijst met [veelgestelde vragen over AKS](faq.md) voor antwoorden op enkele veelgestelde vragen over AKS.

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why