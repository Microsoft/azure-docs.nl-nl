---
title: De configuratie van het knoop punt voor Azure Kubernetes service (AKS)-knooppunt Pools aanpassen (preview-versie)
description: Meer informatie over het aanpassen van de configuratie van cluster knooppunten en knooppunt groepen van Azure Kubernetes service (AKS).
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 7b39242a7d7208b33a070e86088b25e9414ead04
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714626"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>De knooppunt configuratie voor Azure Kubernetes service (AKS)-knooppunt Pools aanpassen (preview-versie)

Door de knooppunt configuratie aan te passen, kunt u de instellingen van uw besturings systeem (OS) of de kubelet-para meters voor overeenstemming met de behoeften van de werk belastingen configureren of afstemmen. Wanneer u een AKS-cluster maakt of een knooppunt groep aan uw cluster toevoegt, kunt u een subset van veelgebruikte instellingen voor besturings systeem en kubelet aanpassen. Als u de instellingen boven deze subset wilt configureren, [gebruikt u een daemon-set om de benodigde configuraties aan te passen zonder verbroken AKS-ondersteuning voor uw knoop punten](support-policies.md#shared-responsibility).

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>De `CustomNodeConfigPreview` Preview-functie registreren

Als u een AKS-cluster of knooppunt groep wilt maken die de kubelet-para meters of de instellingen van het besturings systeem kan aanpassen, moet u de `CustomNodeConfigPreview` functie vlag inschakelen voor uw abonnement.

Registreer de `CustomNodeConfigPreview` functie vlag met behulp van de opdracht [AZ feature REGI ster][az-feature-register] , zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

Het duurt enkele minuten voordat de status is *geregistreerd*. Controleer de registratie status met behulp van de opdracht [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

Als u klaar bent, vernieuwt u de registratie van de resource provider *micro soft. container service* met de opdracht [AZ provider REGI ster][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>De CLI-extensie aks-preview installeren

Als u een AKS-cluster of een knooppunt groep wilt maken die de kubelet-para meters of de instellingen van het besturings systeem kan aanpassen, hebt u de nieuwste *AKS-preview* Azure cli-extensie nodig. Installeer de Azure CLI *-extensie AKS-preview* met behulp van de opdracht [AZ extension add][az-extension-add] . Of installeer alle beschik bare updates met behulp van de opdracht [AZ extension update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>Aangepaste knooppunt configuratie gebruiken

### <a name="kubelet-custom-configuration"></a>Aangepaste configuratie van Kubelet

De ondersteunde Kubelet-para meters en geaccepteerde waarden worden hieronder weer gegeven.

| Parameter | Toegestane waarden/interval | Standaard | Beschrijving |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | geen, statisch | geen | Met het statische beleid kunnen containers in [gegarandeerde peulen](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) met integer CPU toegang tot exclusieve cpu's op het knoop punt aanvragen. |
| `cpuCfsQuota` | de waarde True, false | true |  Het afdwingen van CPU CFS-quota voor containers in-of uitschakelen die CPU-limieten opgeven. | 
| `cpuCfsQuotaPeriod` | Interval in milliseconden (MS) | `100ms` | Hiermee wordt de waarde voor de quotum periode voor CPU-CFS ingesteld. | 
| `imageGcHighThreshold` | 0-100 | 85 | Het percentage schijf gebruik waarna de garbagecollection van de installatie kopie altijd wordt uitgevoerd. Mini maal schijf gebruik waarmee garbagecollection **wordt** geactiveerd. Als u het permanent ophalen van afbeeldingen wilt uitschakelen, stelt u in op 100. | 
| `imageGcLowThreshold` | 0-100, niet hoger dan `imageGcHighThreshold` | 80 | Het percentage schijf gebruik voordat de installatie kopie verzameling nooit wordt uitgevoerd. Mini maal schijf gebruik waarmee garbagecollection **kan worden** geactiveerd. |
| `topologyManagerPolicy` | geen, best-effort, beperkt, single-Numa-knoop punt | geen | Optimaliseer de uitlijning van het NUMA-knoop [punt meer informatie](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/). Alleen kubernetes v 1.18 +. |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | Geen | Lijst met toegestane onveilige sysctls of onveilige sysctl-patronen. | 

### <a name="linux-os-custom-configuration"></a>Aangepaste configuratie van Linux-besturings systeem

De ondersteunde instellingen voor het besturings systeem en de geaccepteerde waarden worden hieronder weer gegeven.

#### <a name="file-handle-limits"></a>Limieten voor bestands ingangen

Wanneer u veel verkeer hebt, is het gebruikelijk dat het verkeer dat u bedient, afkomstig is uit een groot aantal lokale bestanden. U kunt de onderstaande kernel-instellingen en ingebouwde limieten verfijnen zodat u meer kunt verwerken, tegen de kosten van systeem geheugen.

| Instelling | Toegestane waarden/interval | Standaard | Beschrijving |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192-12000500 | 709620 | Maximum aantal bestands ingangen dat door de Linux-kernel wordt toegewezen door deze waarde te verhogen, kunt u het Maxi maal toegestane aantal geopende bestanden verhogen. |
| `fs.inotify.max_user_watches` | 781250-2097152 | 1048576 | Het maximum aantal bestands controles dat door het systeem is toegestaan. Elk *horloge* is ongeveer 90 bytes op een 32-bits kernel en ongeveer 160 bytes op een 64-bits kernel. | 
| `fs.aio-max-nr` | 65536-6553500 | 65536 | De AIO-nr. toont het huidige systeembrede aantal asynchrone io-aanvragen. AIO-Max-nr. Hiermee kunt u de maximum waarde wijzigen die door AIO-nr kan worden uitgebreid naar. |
| `fs.nr_open` | 8192-20000500 | 1048576 | Het maximum aantal bestands ingangen dat een proces kan toewijzen. |


#### <a name="socket-and-network-tuning"></a>Socket en netwerk afstemmen

Voor agent knooppunten, die naar verwachting een zeer groot aantal gelijktijdige sessies verwerken, kunt u de subset van TCP-en netwerk opties hieronder gebruiken die u per knooppunt groep kunt verfijnen. 

| Instelling | Toegestane waarden/interval | Standaard | Beschrijving |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096-3240000 | 16384 | Het maximum aantal verbindings aanvragen dat voor een bepaalde luisterende socket in de wachtrij kan worden geplaatst. Een bovengrens voor de waarde van de para meter achterstand die wordt door gegeven aan de functie [Listen (2)](http://man7.org/linux/man-pages/man2/listen.2.html) . Als het argument achterstand groter is dan de `somaxconn` , wordt het op de achtergrond afgebroken tot deze limiet.
| `net.core.netdev_max_backlog` | 1000-3240000 | 1000 | Het maximum aantal pakketten, in de wachtrij gezet aan de invoer zijde, wanneer de interface pakketten sneller ontvangt dan de kernel kan verwerken. |
| `net.core.rmem_max` | 212992-134217728 | 212992 | De maximale buffer grootte voor de ontvangst socket in bytes. |
| `net.core.wmem_max` | 212992-134217728 | 212992 | De maximale buffer grootte voor het verzenden van de socket in bytes. | 
| `net.core.optmem_max` | 20480-4194304 | 20480 | De maximum grootte van de ondersteunende buffer (optie geheugen buffer) die per socket is toegestaan. Het geheugen voor Socket-opties wordt in enkele gevallen gebruikt voor het opslaan van extra structuren met betrekking tot het gebruik van de socket. | 
| `net.ipv4.tcp_max_syn_backlog` | 128-3240000 | 16384 | Het maximum aantal verbindings aanvragen in de wachtrij waarvoor nog geen bevestiging is ontvangen van de client die verbinding maakt. Als dit aantal wordt overschreden, wordt de kernel gestart met het verwijderen van aanvragen. |
| `net.ipv4.tcp_max_tw_buckets` | 8000-1440000 | 32768 | Maxi maal aantal `timewait` sockets dat tegelijkertijd door het systeem wordt gehouden. Als dit aantal wordt overschreden, wordt de time-wait-socket onmiddellijk vernietigd en wordt er een waarschuwing afgedrukt. |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | De tijds duur die een zwevende verbinding (niet langer waarnaar wordt verwezen door een toepassing) blijft in de FIN_WAIT_2 status voordat deze wordt afgebroken aan het lokale einde. |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | Hoe vaak TCP berichten verzendt `keepalive` Wanneer `keepalive` is ingeschakeld. |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | Het aantal `keepalive` tests dat door TCP wordt verzonden, totdat wordt beslist dat de verbinding is verbroken. |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | Hoe vaak de tests worden verzonden. Vermenigvuldigd met `tcp_keepalive_probes` het is de tijd om een verbinding die niet reageert af te breken nadat de test is gestart. | 
| `net.ipv4.tcp_tw_reuse` | 0 of 1 | 0 | Toestaan dat sockets opnieuw worden gebruikt `TIME-WAIT` voor nieuwe verbindingen wanneer het veilig is vanuit het protocol. | 
| `net.ipv4.ip_local_port_range` | Eerste: 1024-60999 en laatste: 32768-65000] | Eerste: 32768 en laatste: 60999 | Het lokale poort bereik dat door TCP-en UDP-verkeer wordt gebruikt om de lokale poort te kiezen. Bestaande uit twee getallen: het eerste nummer is de eerste lokale poort die is toegestaan voor TCP-en UDP-verkeer op het knoop punt van de agent, de tweede is het laatste lokale poort nummer. | 
| `net.ipv4.neigh.default.gc_thresh1`|  128-80000 | 4096 | Minimum aantal vermeldingen dat zich in de ARP-cache bevinden. Garbagecollection wordt niet geactiveerd als het aantal items onder deze instelling valt. | 
| `net.ipv4.neigh.default.gc_thresh2`|  512-90000 | 8192 | Voorlopig maximum aantal vermeldingen dat zich in de ARP-cache bevinden. Deze instelling is weliswaar de belangrijkste, omdat de ARP-Garbage Collection ongeveer 5 seconden na het bereiken van dit tijdelijke maximum wordt geactiveerd. |
| `net.ipv4.neigh.default.gc_thresh3`|  1024-100000 | 16384 | Het maximum aantal vermeldingen in de ARP-cache. |
| `net.netfilter.nf_conntrack_max` | 131072-589824 | 131072 | `nf_conntrack` is een module die verbindings vermeldingen voor NAT in Linux traceert. De `nf_conntrack` module gebruikt een hash-tabel voor het vastleggen van de *bestaande verbindings* record van het TCP-protocol. `nf_conntrack_max` is het maximum aantal knoop punten in de hashtabel, dat wil zeggen, het maximum aantal verbindingen dat wordt ondersteund door de `nf_conntrack` module of de grootte van de tabel voor het bijhouden van de verbinding. | 
| `net.netfilter.nf_conntrack_buckets` | 65536-147456 | 65536 | `nf_conntrack` is een module die verbindings vermeldingen voor NAT in Linux traceert. De `nf_conntrack` module gebruikt een hash-tabel voor het vastleggen van de *bestaande verbindings* record van het TCP-protocol. `nf_conntrack_buckets` is de grootte van de hash-tabel. | 

#### <a name="worker-limits"></a>Werkrollimieten

Net als bestands descriptor limieten wordt het aantal werk nemers of threads dat door een proces kan worden gemaakt, beperkt door een kernel-instelling en gebruikers limieten. De gebruikers limiet voor AKS is onbeperkt. 

| Instelling | Toegestane waarden/interval | Standaard | Beschrijving |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | Processen kunnen worker-threads oplopen. Het maximum aantal threads dat kan worden gemaakt, wordt ingesteld met de kernel-instelling `kernel.threads-max` . | 

#### <a name="virtual-memory"></a>Virtueel geheugen

De onderstaande instellingen kunnen worden gebruikt voor het afstemmen van de werking van het subsysteem virtuele geheugen (VM) van de Linux-kernel en `writeout` van gewijzigde gegevens op schijf.

| Instelling | Toegestane waarden/interval | Standaard | Beschrijving |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530-262144 | 65530 | Dit bestand bevat het maximum aantal geheugen toewijzings gebieden dat door een proces kan worden uitgevoerd. Geheugen toewijzings gebieden worden gebruikt als een neven effect van het aanroepen van `malloc` , direct door `mmap` , `mprotect` , en en `madvise` ook bij het laden van gedeelde bibliotheken. | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | Met deze percentage waarde bepaalt u de tendens van de kernel om het geheugen vrij te maken. dit wordt gebruikt voor het opslaan in cache van Directory-en inode-objecten. |
| `vm.swappiness` | 0 - 100 | 60 | Dit besturings element wordt gebruikt om te definiëren hoe agressieve geheugen pagina's door de kernel worden gewisseld. Hogere waarden verhogen de scherpte, lagere waarden verminderen de hoeveelheid swap. Een waarde van 0 geeft aan dat de kernel geen swap hoeft te initiëren totdat de hoeveelheid beschik bare pagina's met bestands ondersteuning kleiner is dan de bovengrens in een zone. | 
| `swapFileSizeMB` | 1 MB-grootte van de [tijdelijke schijf](../virtual-machines/managed-disks-overview.md#temporary-disk) (/dev/sdb) | Geen | SwapFileSizeMB Hiermee wordt de grootte in MB van een wissel bestand gemaakt op de agent knooppunten van deze knooppunt groep. | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [Transparante Hugepages](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) is een Linux-kernel-functie die is bedoeld om de prestaties te verbeteren door efficiënter gebruik te maken van de hardware voor geheugen toewijzing van de processor. Wanneer deze functie is ingeschakeld, worden de kernel `hugepages` -pogingen waar mogelijk toegewezen, waarna elk Linux-proces 2 MB pagina's ontvangt als de `mmap` regio 2 MB op natuurlijke wijze is uitgelijnd. In bepaalde gevallen waarbij `hugepages` System Wide is ingeschakeld, kunnen toepassingen uiteindelijk meer geheugen resources toewijzen. Een toepassing kan `mmap` een grote regio zijn, maar er mag maar 1 byte van worden gebruikt. in dat geval kan een pagina van 2 MB worden toegewezen in plaats van een pagina van 4.000 pagina's om een goede reden. In dit scenario is het mogelijk om het hele systeem uit te scha kelen `hugepages` of alleen in regio's te hebben `MADV_HUGEPAGE madvise` . | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | Deze waarde bepaalt of de kernel agressief gebruik van geheugen moet maken om meer beschikbaar te maken `hugepages` . | 

> [!IMPORTANT]
> Voor een gemakkelijke Zoek-en lees baarheid worden de besturingssysteem instellingen in dit document weer gegeven met hun naam, maar moeten ze worden toegevoegd aan het JSON-bestand van de configuratie of de AKS-API met behulp van [camelCase-kapitalisatie Conventie](/dotnet/standard/design-guidelines/capitalization-conventions).

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

Maak een nieuw cluster met de kubelet-en besturingssysteem configuraties met behulp van de JSON-bestanden die in de vorige stap zijn gemaakt. 

> [!NOTE]
> Wanneer u een cluster maakt, kunt u de configuratie van de kubelet, de configuratie van het besturings systeem of beide opgeven. Als u een configuratie opgeeft wanneer u een cluster maakt, wordt deze configuratie alleen toegepast op de knoop punten in de eerste knooppunt groep. Instellingen die niet in het JSON-bestand zijn geconfigureerd, behouden de standaard waarde.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

Voeg een nieuwe knooppunt groep toe met de Kubelet-para meters met behulp van het JSON-bestand dat u hebt gemaakt.

> [!NOTE]
> Wanneer u een knooppunt groep toevoegt aan een bestaand cluster, kunt u de kubelet-configuratie, de configuratie van het besturings systeem of beide opgeven. Als u een configuratie opgeeft wanneer u een knooppunt groep toevoegt, wordt die configuratie alleen toegepast op de knoop punten in de nieuwe groep van het knoop punt. Instellingen die niet in het JSON-bestand zijn geconfigureerd, behouden de standaard waarde.

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie [over het configureren van uw AKS-cluster](cluster-configuration.md).
- Meer informatie over [het upgraden van de knooppunt installatie kopieën](node-image-upgrade.md) in uw cluster.
- Zie [een Azure Kubernetes service-cluster (AKS) bijwerken](upgrade-cluster.md) voor meer informatie over het upgraden van uw cluster naar de nieuwste versie van Kubernetes.
- Raadpleeg de lijst met [Veelgestelde vragen over AKS](faq.md) om antwoorden te vinden op enkele veelgestelde vragen over AKS.

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
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why