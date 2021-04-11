---
title: Veelvoorkomende problemen met de Azure Kubernetes-service oplossen
description: Meer informatie over het oplossen van veelvoorkomende problemen bij het gebruik van Azure Kubernetes service (AKS)
services: container-service
ms.topic: troubleshooting
ms.date: 06/20/2020
ms.openlocfilehash: 1d3dff19bd75bfa4e7564eb4b188ffe68d605025
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952028"
---
# <a name="aks-troubleshooting"></a>AKS-problemen oplossen

Wanneer u clusters van Azure Kubernetes service (AKS) maakt of beheert, kunnen er af en toe problemen optreden. In dit artikel worden enkele veelvoorkomende problemen beschreven en stappen voor probleem oplossing.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Waar vind ik informatie over problemen met fout opsporing in Kubernetes in het algemeen?

Probeer de [officiële hand leiding voor het oplossen van problemen met Kubernetes-clusters](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Er is ook een [hand leiding](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)voor het oplossen van problemen, gepubliceerd door een micro soft-Engineer voor het oplossen van problemen met peulen, knoop punten, clusters en andere functies.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Er verschijnt een `quota exceeded` fout melding tijdens het maken of upgraden. Wat moet ik doen? 

 [Vraag meer kernen](../azure-portal/supportability/resource-manager-core-quotas-request.md)aan.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Er `insufficientSubnetSize` treedt een fout op tijdens het implementeren van een AKS-cluster met geavanceerde netwerken. Wat moet ik doen?

Deze fout geeft aan dat een subnet dat wordt gebruikt voor een cluster niet langer beschik bare Ip's in de CIDR heeft voor een succes volle resource toewijzing. Voor Kubenet-clusters is de vereiste voldoende IP-adres ruimte voor elk knoop punt in het cluster. Voor Azure CNI-clusters is de vereiste voldoende IP-adres ruimte voor elk knoop punt en pod in het cluster.
Lees meer over het [ontwerp van Azure cni om ip's toe te wijzen aan een Peul](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

Deze fouten worden ook weer gegeven in [AKS Diagnostics](concepts-diagnostics.md), die proactief problemen ondervinden, zoals een ontoereikende subnet-grootte.

In de volgende drie (3) gevallen treedt er een fout op onvoldoende subnet-grootte op:

1. Schaal van AKS-of AKS-knooppunt pool
   1. Als Kubenet wordt gebruikt, `number of free IPs in the subnet` is de waarde **kleiner dan** de `number of new nodes requested` .
   1. Als u Azure CNI gebruikt, wanneer de `number of free IPs in the subnet` is **kleiner dan** de `number of nodes requested times (*) the node pool's --max-pod value` .

1. Upgrade van AKS-of AKS-knooppunt groep
   1. Als Kubenet wordt gebruikt, `number of free IPs in the subnet` is de waarde **kleiner dan** de `number of buffer nodes needed to upgrade` .
   1. Als u Azure CNI gebruikt, wanneer de `number of free IPs in the subnet` is **kleiner dan** de `number of buffer nodes needed to upgrade times (*) the node pool's --max-pod value` .
   
   Standaard hebben AKS-clusters een maximale piek waarde (upgrade buffer) van één (1), maar dit upgrade gedrag kan worden aangepast door de [maximale piek waarde van een knooppunt groep in te stellen, waardoor het aantal beschik bare Ip's dat nodig is om een upgrade te volt ooien, wordt verhoogd.

1. AKS maken of AKS van knooppunt groep toevoegen
   1. Als Kubenet wordt gebruikt, `number of free IPs in the subnet` is de waarde **kleiner dan** de `number of nodes requested for the node pool` .
   1. Als u Azure CNI gebruikt, wanneer de `number of free IPs in the subnet` is **kleiner dan** de `number of nodes requested times (*) the node pool's --max-pod value` .

De volgende beperking kan worden gemaakt door nieuwe subnetten te maken. De machtiging voor het maken van een nieuw subnet is vereist voor de oplossing omdat het CIDR-bereik van een bestaand subnet kan worden bijgewerkt.

1. Bouw een nieuw subnet met een groter CIDR-bereik dat voldoende is voor de bewerkings doelen:
   1. Maak een nieuw subnet met een nieuw, niet-overlappend bereik.
   1. Maak een nieuwe knooppunt groep in het nieuwe subnet.
   1. Het afvoer verschil van de oude knooppunt groep die zich in het oude subnet bevindt, wordt vervangen.
   1. Verwijder het oude subnet en de oude knooppunt groep.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Mijn Pod is vastgelopen in de CrashLoopBackOff-modus. Wat moet ik doen?

Er kunnen verschillende redenen zijn waarom de pod in die modus vastloopt. U kunt het volgende bekijken:

* De pod zelf, met behulp van `kubectl describe pod <pod-name>` .
* De logboeken, met behulp van `kubectl logs <pod-name>` .

Zie [fouten opsporen in toepassingen](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)voor meer informatie over het oplossen van problemen met Pod.

## <a name="im-receiving-tcp-timeouts-when-using-kubectl-or-other-third-party-tools-connecting-to-the-api-server"></a>Ik ontvang `TCP timeouts` het gebruik van `kubectl` of andere hulpprogram ma's van derden die verbinding maken met de API-server
AKS heeft HA-besturings plannen die verticaal schalen op basis van het aantal kernen om de serviceniveau doelstellingen (Slo's) en Service Level Agreements (Sla's) te garanderen. Als u een time-out voor verbindingen ondervindt, controleert u het onderstaande:

- **Is de time-out van alle API-opdrachten consistent of slechts een paar?** Als het slechts enkele is, zijn uw `tunnelfront` Pod of `aks-link` Pod, die verantwoordelijk is voor de communicatie van het beheer vlak van het knoop > punt, mogelijk niet actief. Zorg ervoor dat de knoop punten die deze pod host, niet meer worden gebruikt of niet onder spanning zijn. Overweeg ze te verplaatsen naar hun eigen [ `system` knooppunt groep](use-system-pools.md).
- **Hebt u alle vereiste poorten, FQDN-namen en IP-adressen die worden vermeld op de [AKS beperken van documenten](limit-egress-traffic.md)voor uitgaand verkeer voor komen?** Anders kunnen verschillende opdrachten aanroepen mislukken.
- **Wordt het huidige IP-adres gedekt door [API IP-toegestane bereiken](api-server-authorized-ip-ranges.md)?** Als u deze functie gebruikt en uw IP-adres niet is opgenomen in de bereiken, worden uw aanroepen geblokkeerd. 
- **Hebt u een client of toepassing die aanroepen naar de API-server lekt?** Zorg ervoor dat u controles gebruikt in plaats van frequent Get-aanroepen en dat de toepassingen van derden deze aanroepen niet lekken. Een fout in de Istio-mixer zorgt er bijvoorbeeld voor dat er een nieuwe API Server Watch-verbinding wordt gemaakt telkens wanneer een geheim intern wordt gelezen. Omdat dit gedrag met een regel matig interval plaatsvindt, kijken de verbindingen snel op en wordt de API-server uiteindelijk overbelast, ongeacht het schaal patroon. https://github.com/istio/istio/issues/19481
- **Hebt u veel releases in uw helm-implementaties?** Dit scenario kan ertoe leiden dat beide Tiller te veel geheugen gebruiken op de knoop punten, evenals een grote hoeveelheid `configmaps` , wat kan leiden tot onnodige pieken op de API-server. Overweeg om te configureren `--history-max` op en gebruik te maken van `helm init` de nieuwe helm 3. Meer informatie over de volgende problemen: 
    - https://github.com/helm/helm/issues/4821
    - https://github.com/helm/helm/issues/3500
    - https://github.com/helm/helm/issues/4543
- **[Is intern verkeer tussen knoop punten geblokkeerd?](#im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout)**

## <a name="im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout"></a>Ik ontvang `TCP timeouts` , zoals `dial tcp <Node_IP>:10250: i/o timeout`

Deze time-outs kunnen betrekking hebben op het interne verkeer tussen knoop punten die worden geblokkeerd. Controleer of dit verkeer niet wordt geblokkeerd, bijvoorbeeld door [netwerk beveiligings groepen](concepts-security.md#azure-network-security-groups) op het subnet voor de knoop punten van uw cluster.

## <a name="im-trying-to-enable-kubernetes-role-based-access-control-kubernetes-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Ik probeer op Kubernetes (op rollen gebaseerd toegangs beheer) in te scha kelen op een bestaand cluster. Hoe kan ik dat doen?

Het inschakelen van Kubernetes op basis van op rollen gebaseerd toegangs beheer (Kubernetes RBAC) op bestaande clusters wordt op dit moment niet ondersteund, moet worden ingesteld bij het maken van nieuwe clusters. Kubernetes RBAC is standaard ingeschakeld bij gebruik van CLI, portal of een API-versie die hoger is dan `2020-03-01` .

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Ik kan geen logboeken ophalen met behulp van kubectl-Logboeken of ik kan geen verbinding maken met de API-server. Ik krijg de fout melding van server: fout bij het kiezen van de back-end: Dial TCP.... Wat moet ik doen?

Zorg ervoor dat de poorten 22, 9000 en 1194 zijn geopend om verbinding te maken met de API-server. Controleer of de `tunnelfront` of `aks-link` pod wordt uitgevoerd in de *uitvoeren-systeem* naam ruimte met behulp van de `kubectl get pods --namespace kube-system` opdracht. Als dat niet het geval is, dwingt u het verwijderen van de pod af en wordt de computer opnieuw opgestart.

## <a name="im-getting-tls-client-offered-only-unsupported-versions-from-my-client-when-connecting-to-aks-api-what-should-i-do"></a>Ik krijg `"tls: client offered only unsupported versions"` Mijn client bij het maken van verbinding met de AKS-API. Wat moet ik doen?

De mini maal ondersteunde TLS-versie in AKS is TLS 1,2.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Ik probeer een upgrade uit te voeren of te schalen en `"Changing property 'imageReference' is not allowed"` Er wordt een fout bericht weer. Dit probleem Hoe kan ik oplossen?

Mogelijk krijgt u deze fout omdat u de tags in de agent knooppunten in het AKS-cluster hebt gewijzigd. Het wijzigen of verwijderen van tags en andere eigenschappen van resources in de MC_ *-resource groep kan leiden tot onverwachte resultaten. Als u de resources onder de groep MC_ * in het AKS-cluster wijzigt, wordt de serviceniveau doelstelling (SLO) verbroken.

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Er worden fouten weer ontvangen dat mijn cluster de status Mislukt heeft en het bijwerken of schalen werkt pas nadat het is opgelost

*Deze probleemoplossings hulp wordt omgeleid van https://aka.ms/aks-cluster-failed*

Deze fout treedt op wanneer clusters een mislukte status om meerdere redenen invoeren. Volg de onderstaande stappen om de status van het cluster fout op te lossen voordat u de eerder mislukte bewerking opnieuw probeert uit te voeren:

1. De status van het cluster kan `failed` niet worden hersteld `upgrade` en de bewerkingen kunnen niet worden `scale` uitgevoerd. Veelvoorkomende basis problemen en oplossingen zijn:
    * Schalen met **onvoldoende Compute-quota (CRP)**. U kunt dit oplossen door uw cluster eerst terug te schalen naar een stabiele doel status binnen het quotum. Voer vervolgens de volgende [stappen uit om een toename van het reken quotum aan te vragen](../azure-portal/supportability/resource-manager-core-quotas-request.md) voordat u opnieuw opschaalt na de initiële quotum limieten.
    * Het schalen van een cluster met geavanceerde netwerken en **onvoldoende subnet-bronnen (netwerken)**. U kunt dit oplossen door uw cluster eerst terug te schalen naar een stabiele doel status binnen het quotum. Voer vervolgens [de volgende stappen uit om een verhoging van het resource quotum aan te vragen](../azure-resource-manager/templates/error-resource-quota.md#solution) voordat u opnieuw opschaalt na de initiële quotum limieten.
2. Zodra de onderliggende oorzaak van de upgrade fout is opgelost, moet uw cluster de status voltooid hebben. Als de status is gecontroleerd, voert u de oorspronkelijke bewerking opnieuw uit.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade"></a>Er wordt een fout opgetreden bij het upgraden of schalen van de status die mijn cluster wordt bijgewerkt of waarvoor een upgrade is mislukt

*Deze probleemoplossings hulp wordt omgeleid van https://aka.ms/aks-pending-upgrade*

 U kunt niet tegelijkertijd een cluster of knooppunt groep upgraden en schalen. Elk bewerkings type moet in plaats daarvan op de doel resource worden voltooid vóór de volgende aanvraag voor die zelfde resource. Als gevolg hiervan zijn bewerkingen beperkt wanneer actieve upgrade of schaal bewerkingen worden uitgevoerd of geprobeerd. 

Om het probleem op te lossen `az aks show -g myResourceGroup -n myAKSCluster -o table` en de gedetailleerde status van het cluster op te halen. Op basis van het resultaat:

* Als het cluster actief wordt bijgewerkt, wacht u totdat de bewerking is voltooid. Als deze is geslaagd, voert u de bewerking opnieuw uit.
* Als het upgraden van het cluster is mislukt, volgt u de stappen in de vorige sectie.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Kan ik mijn cluster verplaatsen naar een ander abonnement of mijn abonnement met mijn cluster naar een nieuwe Tenant?

Als u uw AKS-cluster naar een ander abonnement of het abonnement van het cluster naar een nieuwe Tenant hebt verplaatst, werkt het cluster niet meer vanwege ontbrekende machtigingen voor het cluster identiteit. **AKS biedt geen ondersteuning voor het verplaatsen van clusters via abonnementen of tenants** vanwege deze beperking.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Ik ontvang fouten bij het gebruiken van functies waarvoor schaal sets voor virtuele machines zijn vereist

*Deze hulp bij het oplossen van problemen is gericht op aka.ms/aks-vmss-enablement*

Er kunnen fouten optreden die aangeven dat uw AKS-cluster niet is ingesteld op een schaalset voor virtuele machines, zoals in het volgende voor beeld:

**Agent pool `<agentpoolname>` is ingesteld op automatisch schalen als ingeschakeld, maar niet op Virtual Machine Scale sets**

Voor functies als cluster-automatische schaal of meerdere knooppunt Pools zijn virtuele-machine schaal sets als de vereist `vm-set-type` .

Volg de *voordat u begint* met de stappen in het juiste document om een AKS-cluster op de juiste manier te maken:

* [De automatische schaal functie van het cluster gebruiken](cluster-autoscaler.md)
* [Meerdere knooppunt groepen maken en gebruiken](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Welke naam beperkingen worden afgedwongen voor AKS-resources en-para meters?

*Deze hulp bij het oplossen van problemen is gericht op aka.ms/aks-naming-rules*

Naam beperkingen worden geïmplementeerd door zowel het Azure-platform als de AKS. Als een resource naam of-para meter een van deze beperkingen uitbreekt, wordt er een fout bericht weer gegeven waarin u wordt gevraagd een andere invoer op te geven. De volgende algemene richt lijnen zijn van toepassing:

* Cluster namen moeten 1-63 tekens lang zijn. De enige toegestane tekens zijn letters, cijfers, streepjes en onderstrepings teken. Het eerste en laatste teken moeten een letter of cijfer zijn.
* De naam van het AKS-knoop punt/*MC_* resource groep combineert de naam van de resource groep en de resource naam. De automatisch gegenereerde syntaxis van `MC_resourceGroupName_resourceName_AzureRegion` mag niet langer zijn dan 80 tekens. Als dat nodig is, vermindert u de lengte van de naam van de resource groep of de AKS-cluster naam. U kunt ook [de naam van de resource groep voor het knoop punt aanpassen](cluster-configuration.md#custom-resource-group-name)
* De *dnsPrefix* moet beginnen en eindigen met alfanumerieke waarden en moet tussen de 1-54 tekens lang zijn. Geldige tekens zijn alfanumerieke waarden en afbreek streepjes (-). De *dnsPrefix* mag geen speciale tekens bevatten, zoals een punt (.).
* Namen van AKS-knooppunt groepen moeten allemaal kleine letters zijn en 1-11 tekens bevatten voor Linux-knooppunt Pools en 1-6 tekens voor Windows-knooppunt groepen. De naam moet beginnen met een letter en de enige toegestane tekens zijn letters en cijfers.
* De *Administrator-gebruikers naam*, waarmee de gebruikers naam van de beheerder voor Linux-knoop punten wordt ingesteld, moet beginnen met een letter, mag alleen letters, cijfers, afbreek streepjes en onderstrepings tekens bevatten en een maximale lengte van 64 karakters hebben.

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Ik ontvang fouten bij het maken, bijwerken, schalen, verwijderen of upgraden van een cluster. deze bewerking is niet toegestaan omdat er een andere bewerking wordt uitgevoerd.

*Deze hulp bij het oplossen van problemen is gericht op aka.ms/aks-pending-operation*

Cluster bewerkingen zijn beperkt wanneer er nog een eerdere bewerking wordt uitgevoerd. Gebruik de opdracht om een gedetailleerde status van uw cluster op te halen `az aks show -g myResourceGroup -n myAKSCluster -o table` . Gebruik uw eigen resource groep en AKS cluster naam waar nodig.

Op basis van de uitvoer van de cluster status:

* Als het cluster zich in een andere inrichtings status bevindt dan *geslaagd* of *mislukt*, wacht u totdat de bewerking (*bijwerken/bijwerken/maken/schalen/verwijderen/migreren*) is voltooid. Wanneer de vorige bewerking is voltooid, voert u de laatste cluster bewerking opnieuw uit.

* Als de upgrade van het cluster is mislukt, volgt u de stappen [die worden beschreven waarbij ik fouten ontvang dat mijn cluster de status Mislukt heeft en dat de upgrade of schaal aanpassing pas werkt als het probleem is opgelost](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster"></a>Er is een fout ontvangen met de melding dat de Service-Principal niet is gevonden of ongeldig is wanneer ik een nieuw cluster probeer te maken.

Wanneer u een AKS-cluster maakt, hebt u een service-principal of beheerde identiteit nodig om namens u resources te maken. AKS kan automatisch een nieuwe service-principal maken op het moment van het maken van het cluster of een bestaand bericht ontvangen. Wanneer u een automatisch gemaakt, Azure Active Directory moet het worden door gegeven aan elke regio zodat het maken slaagt. Wanneer de doorgifte te lang duurt, mislukt de validatie van het cluster om te worden gemaakt omdat er hiervoor geen beschik bare service-principal kan worden gevonden. 

Gebruik de volgende tijdelijke oplossingen voor dit probleem:
* Gebruik een bestaande Service-Principal, die al is door gegeven in verschillende regio's en die bestaat om AKS te geven tijdens het maken van een cluster.
* Als u automatiserings scripts gebruikt, voegt u vertragingen toe tussen het maken van service-principals en het maken van AKS-clusters.
* Als u Azure Portal gebruikt, keert u terug naar de cluster instellingen tijdens het maken en voert u de validatie pagina na een paar minuten opnieuw uit.

## <a name="im-getting-aadsts7000215-invalid-client-secret-is-provided-when-using-aks-api-what-should-i-do"></a>Ik krijg het `"AADSTS7000215: Invalid client secret is provided."` gebruik van AKS-API. Wat moet ik doen?

Dit probleem wordt veroorzaakt door de verval datum van de Service-Principal-referenties. [Werk de referenties voor een AKS-cluster bij.](update-credentials.md)

## <a name="i-cant-access-my-cluster-api-from-my-automationdev-machinetooling-when-using-api-server-authorized-ip-ranges-how-do-i-fix-this-problem"></a>Ik heb geen toegang tot mijn cluster-API vanaf mijn Automation/dev-computer/-hulp programma bij het gebruik van een API server-toegestaan IP-bereik. Dit probleem Hoe kan ik oplossen?

Om dit probleem op te lossen, moet u ervoor zorgen dat er `--api-server-authorized-ip-ranges` een of meer IP-adressen of IP-bereik (en) van Automation/dev/tool-systemen worden gebruikt. Zie de sectie ' mijn IP zoeken ' in [beveiligde toegang tot de API-server met behulp van geautoriseerde IP-](api-server-authorized-ip-ranges.md)adresbereiken.

## <a name="im-unable-to-view-resources-in-kubernetes-resource-viewer-in-azure-portal-for-my-cluster-configured-with-api-server-authorized-ip-ranges-how-do-i-fix-this-problem"></a>Ik kan geen resources weer geven in de Kubernetes-resource viewer in Azure Portal voor mijn cluster dat is geconfigureerd met de API-server geautoriseerde IP-adresbereiken. Dit probleem Hoe kan ik oplossen?

De [Kubernetes-resource Viewer](kubernetes-portal.md) moet toegang hebben tot `--api-server-authorized-ip-ranges` de lokale client computer of het IP-adres bereik (van waaruit de portal wordt gebladerd). Zie de sectie ' mijn IP zoeken ' in [beveiligde toegang tot de API-server met behulp van geautoriseerde IP-](api-server-authorized-ip-ranges.md)adresbereiken.

## <a name="im-receiving-errors-after-restricting-egress-traffic"></a>Ik ontvang fouten na het beperken van het uitgaande verkeer

Bij het beperken van uitgaand verkeer van een AKS-cluster zijn er [vereiste en optionele aanbevolen](limit-egress-traffic.md) uitgaande poorten/netwerk regels en FQDN/toepassings regels voor AKS. Als uw instellingen conflicteren met een van deze regels, `kubectl` werken bepaalde opdrachten niet goed. Er worden ook fouten weer geven bij het maken van een AKS-cluster.

Controleer of de instellingen niet conflicteren met een van de vereiste of optionele aanbevolen uitgaande poorten/netwerk regels en FQDN/toepassings regels.

## <a name="im-receiving-429---too-many-requests-errors"></a>Ik ontvang berichten over "429-te veel aanvragen"

Wanneer een kubernetes-cluster op Azure (AKS of Nee) een regel matig omhoog/omlaag schaalt of gebruikmaakt van de cluster autoscaler (CA), kunnen deze bewerkingen leiden tot een groot aantal HTTP-aanroepen waardoor het toegewezen abonnements quotum wordt overschreden. De fouten zien er als volgt uit

```
Service returned an error. Status=429 Code=\"OperationNotAllowed\" Message=\"The server rejected the request because too many requests have been received for this subscription.\" Details=[{\"code\":\"TooManyRequests\",\"message\":\"{\\\"operationGroup\\\":\\\"HighCostGetVMScaleSet30Min\\\",\\\"startTime\\\":\\\"2020-09-20T07:13:55.2177346+00:00\\\",\\\"endTime\\\":\\\"2020-09-20T07:28:55.2177346+00:00\\\",\\\"allowedRequestCount\\\":1800,\\\"measuredRequestCount\\\":2208}\",\"target\":\"HighCostGetVMScaleSet30Min\"}] InnerError={\"internalErrorCode\":\"TooManyRequestsReceived\"}"}
```

Deze beperkings fouten worden [hier](../azure-resource-manager/management/request-limits-and-throttling.md) gedetailleerd beschreven [](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors)

De aanbeveling van het technische team van AKS is om ervoor te zorgen dat u versie ten minste 1.18. x gebruikt, die veel verbeteringen bevat. Meer informatie vindt u [hier](https://github.com/Azure/AKS/issues/1413) in deze [verbeteringen.](https://github.com/kubernetes-sigs/cloud-provider-azure/issues/247)

Deze beperkings fouten worden op abonnements niveau gemeten, maar kunnen ook worden uitgevoerd als:
- Er zijn toepassingen van derden die GET-aanvragen maken (bijvoorbeeld toepassingen bewaken, enzovoort). De aanbeveling is de frequentie van deze aanroepen te verlagen.
- Er zijn talloze AKS-clusters/-knooppunt groepen die gebruikmaken van virtuele-machine schaal sets. Probeer het aantal clusters te splitsen in verschillende abonnementen, met name als u verwacht dat ze zeer actief zijn (bijvoorbeeld een actieve cluster autoscaleer) of meerdere clients hebben (bijvoorbeeld Rancher, terraform, enzovoort).

## <a name="my-clusters-provisioning-status-changed-from-ready-to-failed-with-or-without-me-performing-an-operation-what-should-i-do"></a>De inrichtings status van mijn cluster is gewijzigd van gereed in mislukt met of zonder dat er een bewerking wordt uitgevoerd. Wat moet ik doen?

Als de inrichtings status van uw cluster wordt gewijzigd van *gereed* in *mislukt* met of zonder dat u bewerkingen uitvoert, maar de toepassingen op uw cluster blijven worden uitgevoerd, kan dit probleem automatisch worden opgelost door de service en kunnen uw toepassingen niet worden beïnvloed.

Als de inrichtings status van uw cluster *niet* actief blijft of als de toepassingen op uw cluster niet meer werken, [dient u een ondersteunings aanvraag](https://azure.microsoft.com/support/options/#submit)in.

## <a name="my-watch-is-stale-or-azure-ad-pod-identity-nmi-is-returning-status-500"></a>Mijn horloge is verouderd of de Azure AD pod-identiteit NMI retourneert de status 500

Als u gebruikmaakt van Azure Firewall zoals in dit [voor beeld](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall), kunt u dit probleem tegen komen als de lange levens duur van de TCP-verbindingen via een firewall met toepassings regels die momenteel een bug hebben (om te worden opgelost in Q1CY21) die ervoor zorgt dat de go `keepalives` wordt beëindigd op de firewall. Totdat dit probleem is opgelost, kunt u dit oplossen door een netwerk regel (in plaats van een toepassings regel) toe te voegen aan het IP-adres van de AKS-API-server.

## <a name="azure-storage-and-aks-troubleshooting"></a>Problemen met Azure Storage en AKS oplossen

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Fout bij het instellen van UID en `GID` in mountOptions voor Azure Disk

Azure Disk maakt gebruik van het ext4-, xfs-bestands systeem standaard en mountOptions, zoals UID = x, GID = x kan niet worden ingesteld op koppel tijd. Als u bijvoorbeeld probeert mountOptions UID = 999, GID = 999, wordt een fout weer geven als:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

U kunt het probleem oplossen door een van de volgende opties te doen:

* [Configureer de beveiligings context voor een pod door de](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) UID in runAsUser en GID in fsGroup in te stellen. Met de volgende instelling wordt bijvoorbeeld pod uitvoeren als root ingesteld, zodat deze toegankelijk is voor elk bestand:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > Aangezien GID en UID standaard als root of 0 zijn gekoppeld. Als GID of UID als niet-basis is ingesteld, bijvoorbeeld 1000, wordt Kubernetes gebruikt `chown` om alle mappen en bestanden op die schijf te wijzigen. Deze bewerking kan veel tijd in beslag nemen en kan het koppelen van de schijf erg traag maken.

* Gebruik `chown` in initContainers om in te stellen `GID` en `UID` . Bijvoorbeeld:

```yaml
initContainers:
- name: volume-mount
  image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Fout bij het loskoppelen van de Azure-schijf vanwege een probleem met de potentiële race voorwaarde en de lijst met ongeldige gegevens schijven

Wanneer een Azure-schijf niet kan worden ontkoppeld, wordt er Maxi maal zes keer opnieuw geprobeerd om de schijf los te koppelen met behulp van exponentiële back-ups. Het bevat ook een vergren deling op knooppunt niveau voor de gegevens schijf lijst van ongeveer drie minuten. Als de lijst met schijven hand matig wordt bijgewerkt tijdens die tijd, wordt de schijf lijst die wordt bewaard door de vergren deling op knooppunt niveau, verouderd en leidt dit tot instabiliteit van het knoop punt.

Dit probleem is opgelost in de volgende versies van Kubernetes:

| Kubernetes-versie | Vaste versie |
|--|:--:|
| 1.12 | 1.12.9 of hoger |
| 1.13 | 1.13.6 of hoger |
| 1,14 | 1.14.2 of hoger |
| 1,15 en hoger | N.v.t. |

Als u een versie van Kubernetes gebruikt die niet over de oplossing voor dit probleem beschikt en uw knoop punt beschikt over een verouderde schijf lijst, kunt u deze beperken door alle niet-bestaande schijven te ontkoppelen van de virtuele machine als een bulk bewerking. **Het afzonderlijk ontkoppelen van niet-bestaande schijven kan mislukken.**

### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Een groot aantal Azure-schijven veroorzaakt langzame koppeling/ontkoppeling

Wanneer het aantal koppel-en ontkoppelings bewerkingen van Azure disk dat is gericht op één knoop punt VM groter is dan 10, of groter is dan 3 bij het richten op een schaalset met één virtuele machine, kunnen ze langzamer zijn dan verwacht, omdat ze opeenvolgend worden uitgevoerd. Dit probleem is een bekende beperking en er zijn op dit moment geen tijdelijke oplossingen. [Gebruikers spraak item voor ondersteuning parallelle koppeling/ontkoppeling buiten getal.](https://feedback.azure.com/forums/216843-virtual-machines/suggestions/40444528-vmss-support-for-parallel-disk-attach-detach-for)..

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Fout bij het losmaken van Azure-schijf vanwege een mogelijke VM-virtuele machine met de status mislukt

In sommige gevallen kan het loskoppelen van een Azure-schijf gedeeltelijk mislukken en de VM van het knoop punt in een mislukte status laten staan.

Dit probleem is opgelost in de volgende versies van Kubernetes:

| Kubernetes-versie | Vaste versie |
|--|:--:|
| 1.12 | 1.12.10 of hoger |
| 1.13 | 1.13.8 of hoger |
| 1,14 | 1.14.4 of hoger |
| 1,15 en hoger | N.v.t. |

Als u een versie van Kubernetes gebruikt die niet de oplossing voor dit probleem heeft en uw knoop punt zich in een mislukte status bevindt, kunt u de status van de virtuele machine hand matig bijwerken door een van de onderstaande opties te gebruiken:

* Voor een cluster op basis van beschikbaarheids sets:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Voor een cluster op basis van VMSS:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Problemen met Azure Files en AKS oplossen

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Wat zijn de aanbevolen stabiele versies van Kubernetes voor Azure files?
 
| Kubernetes-versie | Aanbevolen versie |
|--|:--:|
| 1.12 | 1.12.6 of hoger |
| 1.13 | 1.13.4 of hoger |
| 1,14 | 1.14.0 of hoger |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Wat zijn de standaard mountOptions bij het gebruik van Azure Files?

Aanbevolen instellingen:

| Kubernetes-versie | File mode-en dirMode-waarde|
|--|:--:|
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 en hoger | 0777 |

Koppelings opties kunnen worden opgegeven voor het object Storage-klasse. In het volgende voor beeld wordt *0777* ingesteld:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Enkele aanvullende nuttige *mountOptions* -instellingen:

* `mfsymlinks` maakt het Azure Files koppelen (CIFS) ondersteuning voor symbolische koppelingen
* `nobrl` Hiermee wordt voor komen dat aanvragen voor het vergren delen van bytes naar de server worden verzonden. Deze instelling is nood zakelijk voor bepaalde toepassingen die worden onderbroken met de CIFS-stijl vergren delingen van een verplicht byte bereik. De meeste CIFS-servers bieden nog geen ondersteuning voor het aanvragen van advies-byte bereik vergrendelingen. Als u *nobrl* niet gebruikt, kunnen toepassingen die worden onderbroken met de CIFS-stijl die verplicht byte bereik zijn, fout berichten veroorzaken die vergelijkbaar zijn met:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Fout: kan de machtigingen niet wijzigen bij gebruik van Azure Files

Wanneer u PostgreSQL uitvoert op de Azure Files-invoeg toepassing, wordt er mogelijk een fout bericht weer gegeven die vergelijkbaar is met het volgende:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Deze fout wordt veroorzaakt door de Azure Files-invoeg toepassing met behulp van het CIFS/SMB-protocol. Wanneer u het CIFS/SMB-protocol gebruikt, kunnen de machtigingen voor bestanden en mappen niet worden gewijzigd na het koppelen.

U kunt dit probleem oplossen door `subPath` samen met de Azure Disk-invoeg toepassing te gebruiken. 

> [!NOTE] 
> Voor ext3/4-schijf type is er een map gevonden die niet meer bestaat dan waar de schijf is geformatteerd.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Azure Files heeft een hoge latentie vergeleken met de Azure-schijf bij het verwerken van veel kleine bestanden

In sommige gevallen, zoals bij het verwerken van veel kleine bestanden, kan er een hoge latentie optreden bij het gebruik van Azure Files in vergelijking met de Azure-schijf.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Fout bij het inschakelen van de instelling toegang vanaf geselecteerd netwerk toestaan voor het opslag account

Als u *toegang toestaan vanuit geselecteerd netwerk* wilt inschakelen voor een opslag account dat wordt gebruikt voor dynamische inrichting in AKS, krijgt u een fout melding wanneer aks een bestands share maakt:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Deze fout wordt veroorzaakt door het feit dat de Kubernetes *persistentvolume-controller* niet op het netwerk is gekozen wanneer de instelling *toegang vanaf het geselecteerde netwerk toestaan* is ingesteld.

U kunt het probleem oplossen met behulp van [statische inrichting met Azure files](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure Files kan niet opnieuw worden gekoppeld in Windows pod

Als een Windows-pod met een Azure Files-koppeling wordt verwijderd en vervolgens opnieuw wordt gemaakt op hetzelfde knoop punt, mislukt de koppeling. Deze fout is opgetreden vanwege een `New-SmbGlobalMapping` mislukte opdracht omdat de Azure files koppeling al aan het knoop punt is gekoppeld.

Het is bijvoorbeeld mogelijk dat er een fout wordt weer gegeven die vergelijkbaar is met:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Dit probleem is opgelost in de volgende versies van Kubernetes:

| Kubernetes-versie | Vaste versie |
|--|:--:|
| 1.12 | 1.12.6 of hoger |
| 1.13 | 1.13.4 of hoger |
| 1,14 en hoger | N.v.t. |

### <a name="azure-files-mount-fails-because-of-storage-account-key-changed"></a>Het Azure Files koppelen is mislukt omdat de sleutel van het opslag account is gewijzigd

Als de sleutel van uw opslag account is gewijzigd, ziet u mogelijk Azure Files koppel fouten.

U kunt dit verhelpen door het veld hand matig bij te werken `azurestorageaccountkey` in een Azure-bestands geheim met uw met base64 gecodeerde opslag account sleutel.

Als u de sleutel van uw opslag account wilt coderen in base64, kunt u gebruiken `base64` . Bijvoorbeeld:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Gebruik om uw Azure-geheim bestand bij te werken `kubectl edit secret` . Bijvoorbeeld:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Na een paar minuten probeert het agent knooppunt de Azure-bestands koppeling opnieuw uit te voeren met de bijgewerkte opslag sleutel.


### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>Het schalen van het cluster kan niet worden geschaald met de fout bij het herstellen van de grootte van de knooppunt groep

Als uw cluster automatisch schalen niet omhoog/omlaag wordt geschaald, ziet u een fout zoals hieronder in de [Logboeken][view-master-logs]van de cluster-automatische schaalr.

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Deze fout wordt veroorzaakt door een upstream voor de race van het cluster voor automatisch schalen. In een dergelijk geval eindigt cluster-automatisch schalen met een andere waarde dan die van het cluster. Als u deze status wilt weer geven, schakelt u de automatische [cluster-Scaler][cluster-autoscaler]uit en opnieuw in.

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Een trage schijf bijlage `GetAzureDiskLun` duurt 10 tot 15 minuten en er wordt een fout bericht weer gegeven

Bij Kubernetes-versies **ouder dan 1.15.0**, wordt er mogelijk een fout bericht weer gegeven, zoals **fout WaitForAttach kan geen LUN vinden voor de schijf**.  De tijdelijke oplossing voor dit probleem is om ongeveer 15 minuten te wachten en probeer het opnieuw.


### <a name="why-do-upgrades-to-kubernetes-116-fail-when-using-node-labels-with-a-kubernetesio-prefix"></a>Waarom worden upgrades naar Kubernetes 1,16 mislukt wanneer u knooppunt labels gebruikt met een kubernetes.io-voor voegsel

Vanaf Kubernetes [1,16](https://v1-16.docs.kubernetes.io/docs/setup/release/notes/) kan [alleen een gedefinieerde subset van labels met het voor voegsel kubernetes.io](https://v1-18.docs.kubernetes.io/docs/concepts/overview/working-with-objects/labels/) worden toegepast door de kubelet op knoop punten. AKS kan geen actieve labels namens u zonder toestemming worden verwijderd, omdat dit kan leiden tot uitval tijd voor werk belastingen.

Als gevolg hiervan kunt u het volgende doen om dit probleem te verhelpen:

1. Upgrade uw cluster besturings vlak naar 1,16 of hoger
2. Een nieuwe nodepoool toevoegen aan 1,16 of hoger zonder de niet-ondersteunde kubernetes.io-labels
3. De oude knooppunt groep verwijderen

AKS onderzoekt de mogelijkheid om actieve labels in een knooppunt groep te mutate om deze beperking te verbeteren.



<!-- LINKS - internal -->
[view-master-logs]: ./view-control-plane-logs.md
[cluster-autoscaler]: cluster-autoscaler.md