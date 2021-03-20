---
title: Een zelf-hostende Gateway implementeren naar Kubernetes | Microsoft Docs
description: Meer informatie over het implementeren van een zelf-hostend gateway onderdeel van Azure API Management op Kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 023c2c89b90d6ddc71abc95db325dcdeb7684a2d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89500127"
---
# <a name="deploy-a-self-hosted-gateway-to-kubernetes"></a>Een zelf-hostende gateway implementeren op Kubernetes

In dit artikel worden de stappen beschreven voor het implementeren van het zelf-hostende gateway onderdeel van Azure API Management naar een Kubernetes-cluster.

## <a name="prerequisites"></a>Vereisten

- Voltooi de volgende snelstartgids: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
- Maak een Kubernetes-cluster.
   > [!TIP]
   > [Clusters met één knoop punt](https://kubernetes.io/docs/setup/#learning-environment) werken goed voor ontwikkelings-en evaluatie doeleinden. Gebruik [Kubernetes Certified](https://kubernetes.io/partners/#conformance) multi-node clusters on-premises of in de Cloud voor productie werkbelastingen.
- [Richt een zelf-hostende gateway bron in uw API Management-exemplaar in](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Implementeren naar Kubernetes

1. Selecteer **gateways** onder **implementatie en infra structuur**.
2. Selecteer de zelf-hostende gateway resource die u wilt implementeren.
3. Selecteer **implementatie**.
4. Er is automatisch een toegangs token in het tekstvak **token** voor u gegenereerd, op basis van de standaard waarden voor de **verval datum** en de **geheime sleutel** . Als dat nodig is, kiest u waarden in een van beide of beide besturings elementen om een nieuw token te genereren.
5. Selecteer het tabblad **Kubernetes** onder **implementatie scripts**.
6. Selecteer de koppeling van het **\<gateway-name\> . yml** -bestand en down load het yaml-bestand.
7. Selecteer het pictogram **kopiëren** in de rechter benedenhoek van het tekstvak **implementeren** om de `kubectl` opdrachten op het klem bord op te slaan.
8. Plak opdrachten in het venster Terminal (of Command). Met de eerste opdracht maakt u een Kubernetes-geheim dat het toegangs token bevat dat in stap 4 is gegenereerd. Met de tweede opdracht wordt het configuratie bestand dat in stap 6 is gedownload, toegepast op het Kubernetes-cluster en wordt verwacht dat het bestand zich in de huidige map bedient.
9. Voer de opdrachten uit om de benodigde Kubernetes-objecten in de [standaard naam ruimte](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) te maken en zelf-hostende gateways te starten vanaf de [container installatie kopie](https://aka.ms/apim/sputnik/dhub) die is gedownload van de micro soft-container Registry.
10. Voer de volgende opdracht uit om te controleren of de implementatie is geslaagd. Houd er rekening mee dat het enige tijd kan duren voordat alle objecten zijn gemaakt en dat de peulen kunnen worden geïnitialiseerd.
    ```console
    kubectl get deployments
    NAME             READY   UP-TO-DATE   AVAILABLE   AGE
    <gateway-name>   1/1     1            1           18s
    ```
11. Voer de volgende opdracht uit om te controleren of de service is gemaakt. Houd er rekening mee dat uw service-Ip's en-poorten verschillend zijn.
    ```console
    kubectl get services
    NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    <gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
    ```
12. Ga terug naar de Azure Portal en selecteer **overzicht**.
13. Controleer of de **status** een groen vinkje bevat, gevolgd door een aantal knoop punten dat overeenkomt met het aantal replica's dat is opgegeven in het yaml-bestand. Deze status betekent dat de geïmplementeerde zelf-hostende gateway zo goed mogelijk communiceert met de API Management-service en een regel ' heartbeat ' heeft.

    ![Gateway status](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> Voer de <code>kubectl logs deployment/<gateway-name></code> opdracht uit om logboeken van een wille keurig geselecteerd pod weer te geven als er meer dan één.
> Uitvoeren <code>kubectl logs -h</code> voor een volledige set opdracht Opties, zoals het weer geven van Logboeken voor een specifieke Pod of container.

## <a name="production-deployment-considerations"></a>Overwegingen voor productie-implementatie

### <a name="access-token"></a>Toegangs token
Zonder een geldig toegangs token heeft een zelf-hostende gateway geen toegang tot de configuratie gegevens van het eind punt van de bijbehorende API Management service. Het toegangs token kan Maxi maal 30 dagen geldig zijn. Het moet opnieuw worden gegenereerd en het cluster is geconfigureerd met een nieuwe token, hetzij hand matig of via automatisering voordat het verloopt.

Wanneer u het vernieuwen van tokens automatiseert, gebruikt u [deze beheer API-bewerking](/rest/api/apimanagement/2019-12-01/gateway/generatetoken) om een nieuw token te genereren. Zie de [Kubernetes-website](https://kubernetes.io/docs/concepts/configuration/secret)voor meer informatie over het beheren van Kubernetes-geheimen.

### <a name="namespace"></a>Naamruimte
Kubernetes- [naam ruimten](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) helpen bij het delen van één cluster over meerdere teams, projecten of toepassingen. Naam ruimten bieden een bereik voor resources en namen. Ze kunnen worden gekoppeld aan een resource quota en toegangs beheer beleid.

De Azure Portal biedt opdrachten voor het maken van zelf-hostende gateway bronnen in de **standaard** naam ruimte. Deze naam ruimte wordt automatisch gemaakt, bestaat in elk cluster en kan niet worden verwijderd.
Overweeg het [maken en implementeren](https://kubernetesbyexample.com/ns/) van een zelf-hostende gateway in een afzonderlijke naam ruimte in de productie omgeving.

### <a name="number-of-replicas"></a>Aantal replica's
Het minimale aantal replica's dat geschikt is voor productie is twee.

Een zelf-hostende gateway wordt standaard geïmplementeerd met een **RollingUpdate** -implementatie [strategie](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy). Bekijk de standaard waarden en overweeg expliciet de velden [maxUnavailable](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) en [maxSurge](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) in te stellen, met name wanneer u een hoog aantal replica's gebruikt.

### <a name="container-resources"></a>Container bronnen
Standaard worden in het YAML-bestand in de Azure Portal geen container resource aanvragen vermeld.

Het is niet mogelijk om op betrouw bare wijze te voors pellen en de hoeveelheid CPU-en geheugen bronnen per container en het aantal benodigde replica's voor de ondersteuning van een specifieke werk belasting te raden. Veel factoren zijn aan het spelen, zoals:

- Specifieke hardware waarop het cluster wordt uitgevoerd.
- Aanwezigheid en type virtualisatie.
- Aantal en de frequentie van gelijktijdige client verbindingen.
- Aanvraag frequentie.
- Type en aantal geconfigureerde beleids regels.
- Payload-grootte en of payloads worden gebufferd of gestreamd.
- Latentie van back-end-service.

We raden aan om resource aanvragen in te stellen op twee kernen en twee GiB als uitgangs punt. Voer een belasting test uit en schaal omhoog/omlaag of omlaag/in op basis van de resultaten.

### <a name="container-image-tag"></a>Tag container installatie kopie
Het YAML-bestand in de Azure Portal maakt gebruik van de **nieuwste** tag. Deze tag verwijst altijd naar de meest recente versie van de zelf-hostende gateway container installatie kopie.

Overweeg het gebruik van een specifieke versie code in de productie om onbedoelde upgrade naar een nieuwere versie te voor komen.

U kunt [een volledige lijst met beschik bare Tags downloaden](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list).

### <a name="dns-policy"></a>DNS-beleid
DNS-naam omzetting speelt een cruciale rol in de mogelijkheid van een zelf-hostende gateway om verbinding te maken met afhankelijkheden in Azure-en Dispatch API-aanroepen naar back-end-services.

Het YAML-bestand in de Azure Portal past het standaard [ClusterFirst](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) -beleid toe. Dit beleid zorgt ervoor dat aanvragen voor naam omzetting die niet zijn opgelost door de cluster-DNS, worden doorgestuurd naar de upstream-DNS-server die is overgenomen van het knoop punt.

Zie de [Kubernetes-website](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service)voor meer informatie over naam omzetting in Kubernetes. Overweeg het [DNS-beleid](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) of de [DNS-configuratie](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) zo nodig aan te passen voor uw installatie.

### <a name="external-traffic-policy"></a>Beleid voor externe verkeer
Het YAML-bestand dat in het `externalTrafficPolicy` veld Azure Portal op het [service](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/#service-v1-core) object wordt vermeld `Local` . Hiermee behoudt u het IP-adres van de beller (toegankelijk in de context van de [aanvraag](api-management-policy-expressions.md#ContextVariables)) en schakelt u de taak verdeling tussen knoop punten uit, waardoor er geen netwerk hops ontstaan. Houd er rekening mee dat deze instelling een asymmetrische distributie van verkeer kan veroorzaken in implementaties met een ongelijk aantal gateway-aantallen per knoop punt.

### <a name="custom-domain-names-and-ssl-certificates"></a>Aangepaste domein namen en SSL-certificaten

Als u aangepaste domein namen voor de API Management-eind punten gebruikt, met name als u een aangepaste domein naam gebruikt voor het beheer eindpunt, moet u mogelijk de waarde van `config.service.endpoint` in het **\<gateway-name\> . yaml** -bestand bijwerken om de standaard domeinnaam te vervangen door de aangepaste domein naam. Zorg ervoor dat het beheer eindpunt toegankelijk is vanaf de pod van de zelf-hostende gateway in het Kubernetes-cluster.

Als het SSL-certificaat dat wordt gebruikt door het beheer eindpunt niet is ondertekend door een bekend CA-certificaat, moet u ervoor zorgen dat het CA-certificaat wordt vertrouwd door de pod van de zelf-hostende gateway.

### <a name="configuration-backup"></a>Configuratie back-up
Zie [zelf-hostende gateway-overzicht](self-hosted-gateway-overview.md#connectivity-to-azure)voor meer informatie over het gedrag van zelf-hostende gateway in de aanwezigheid van een tijdelijke Azure-verbindings onderbreking.

Configureer een lokaal opslag volume voor de zelf-hostende gateway container, zodat er een back-up van de meest recente gedownloade configuratie kan worden opgeslagen. Als de verbinding is verbroken, kan het opslag volume de back-up gebruiken wanneer de computer opnieuw wordt opgestart. Het pad naar het volume koppelt moet zijn <code>/apim/config</code> . Bekijk een voor beeld op [github](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml).
Zie de [Kubernetes-website](https://kubernetes.io/docs/concepts/storage/volumes/)voor meer informatie over opslag in Kubernetes.

### <a name="local-logs-and-metrics"></a>Lokale logboeken en metrische gegevens
De zelf-hostende Gateway verzendt telemetrie naar [Azure monitor](api-management-howto-use-azure-monitor.md) en [Azure-toepassing inzichten](api-management-howto-app-insights.md) op basis van de configuratie-instellingen in de bijbehorende API Management service.
Wanneer de [verbinding met Azure](self-hosted-gateway-overview.md#connectivity-to-azure) tijdelijk is verbroken, wordt de stroom van telemetrie naar Azure onderbroken en worden de gegevens voor de duur van de onderbreking verloren gegaan.
Overweeg om [lokale bewaking](how-to-configure-local-metrics-logs.md) in te stellen om het API-verkeer te observeren en te voor komen dat telemetrie verloren gaat tijdens Azure-connectiviteit.

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf-hostende gateway-overzicht](self-hosted-gateway-overview.md)voor meer informatie over de zelf-hostende gateway.
