---
title: Geo-replicatie toepassen voor een register
description: Ga aan de slag met het maken en beheren van een geo-gerepliceerd Azure container Registry, waarmee het REGI ster meerdere regio's met regionale replica's voor meerdere masters kan verwerken. Geo-replicatie is een functie van de service-laag Premium.
author: stevelas
ms.topic: article
ms.date: 07/21/2020
ms.author: stevelas
ms.openlocfilehash: e5f0fe76b599874afe8d64c293f3d914da5dd243
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705163"
---
# <a name="geo-replication-in-azure-container-registry"></a>Geo-replicatie in Azure Container Registry

Bedrijven die lokale aanwezigheid willen, of een back-up zonder opnieuw opstarten, kunnen services uitvoeren vanuit meerdere Azure-regio's. Het plaatsen van een containerregister in elke regio waarin installatiekopieën worden uitgevoerd, is een best practice die bewerkingen dicht bij het netwerk mogelijk maakt, wat zorgt voor snelle, betrouwbare overdrachten van installatiekopielagen. Met geo-replicatie kan een Azure-containerregister als één register functioneren en meerdere regio's bedienen met regionale registers voor meerdere masters. 

Een register met geo-replicatie biedt de volgende voordelen:

* U kunt één REGI ster-, afbeeldings-en tagnaam gebruiken in meerdere regio's
* Verbeter de prestaties en betrouw baarheid van regionale implementaties met netwerk-sluit register toegang
* Verlaag de kosten voor gegevens overdracht door afbeeldings lagen van een lokaal, gerepliceerd REGI ster in dezelfde of nabijgelegen regio als de container host op te halen.
* Eén beheerpunt voor een register in meerdere regio's

> [!NOTE]
> Als u kopieën van containerinstallaties in meer dan één Azure-containerregister wilt behouden, kunt u met Azure Container Registry ook [installatiekopieën importeren](container-registry-import-images.md). U kunt bijvoorbeeld binnen een DevOps-werkstroom een installatiekopie uit een ontwikkelingsregister naar een productieregister importeren zonder Docker-opdrachten te gebruiken.
>

## <a name="example-use-case"></a>Voorbeeld van een toepassing
Contoso voert een website voor openbare aanwezigheid uit voor de VS, Canada en Europa. Contoso voert AKS-clusters ([Azure Kubernetes Service](../aks/index.yml)) uit in VS - west, VS - oost, Canada - centraal en Europa - west om deze markten met lokale inhoud van dicht bij het netwerk te bedienen. De webtoepassing, geïmplementeerd als een Docker-installatiekopie, gebruikt dezelfde code en installatiekopie in alle regio's. Inhoud, die lokaal is voor een bepaalde regio, wordt opgehaald uit een database, die uniek is ingericht in elke regio. Elke regionale implementatie heeft een unieke configuratie voor resources zoals de lokale database.

Het ontwikkelteam bevindt zich in Seattle, WA en gebruikt het datacentrum VS - west.

![Pushen naar meerdere registers](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Pushen naar meerdere registers*

Voordat Contoso de functies voor geo-replicatie ging gebruiken, had het bedrijf een in de VS gebaseerd register in VS - west, met een extra register in Europa - west. Het ontwikkelteam pushte installatiekopieën naar twee verschillende registers om deze verschillende regio's te bedienen.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Ophalen vanuit meerdere registers](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Ophalen vanuit meerdere registers*

Typische uitdagingen van meerdere registers zijn onder meer:

* De clusters VS - oost, VS - west en Canada - centraal halen allemaal gegevens op uit het register VS - west, waarbij extra kosten voor uitgaand verkeer worden gegenereerd omdat al deze externe containerhosts installatiekopieën ophalen vanuit datacenters in VS - west.
* Het ontwikkelteam moet installatiekopieën pushen naar registers in VS - west en Europa - west.
* Het ontwikkelteam moet elke regionale implementatie configureren en onderhouden met installatiekopienamen die verwijzen naar het lokale register.
* Toegang tot het register moet voor elke regio worden geconfigureerd.

## <a name="benefits-of-geo-replication"></a>Voordelen van geo-replicatie

![Ophalen vanuit een geo-gerepliceerd register](media/container-registry-geo-replication/after-geo-replicate-pull.png)

De functie voor geo-replicatie van Azure Container Registry biedt de volgende voordelen:

* Eén register beheren voor alle regio's: `contoso.azurecr.io`
* Eén configuratie van installatie kopie-implementaties beheren, omdat alle regio's dezelfde afbeeldings-URL gebruiken: `contoso.azurecr.io/public/products/web:1.2`
* Push naar één REGI ster, terwijl ACR de geo-replicatie beheert. ACR repliceert alleen unieke lagen en vermindert de gegevens overdracht tussen regio's. 
* Configureer regionale [webhooken](container-registry-webhook.md) om u op de hoogte te stellen van gebeurtenissen in specifieke replica's.

Azure Container Registry biedt ook ondersteuning voor [beschikbaarheids zones](zone-redundancy.md) om in een Azure-regio een robuuste en Maxi maal beschik bare Azure container Registry te maken. De combi natie van beschikbaarheids zones voor redundantie binnen een regio en geo-replicatie tussen meerdere regio's, verbetert de betrouw baarheid en prestaties van een REGI ster.

## <a name="configure-geo-replication"></a>Geo-replicatie configureren

Het configureren van geo-replicatie is net zo gemakkelijk als regio's aanklikken op een kaart. U kunt geo-replicatie ook beheren met hulpprogram ma's met inbegrip van de opdracht [AZ ACR Replication](/cli/azure/acr/replication) commands in de Azure CLI of het implementeren van een REGI ster dat is ingeschakeld voor geo-replicatie met een [Azure Resource Manager sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication).

Geo-replicatie is een functie van [Premium-registers](container-registry-skus.md). Als uw register nog niet Premium is, kunt u overstappen van Basic en Standard naar Premium in de [Azure-portal](https://portal.azure.com):

![Service lagen overschakelen in het Azure Portal](media/container-registry-skus/update-registry-sku.png)

Als u geo-replicatie voor een Premium-register wilt configureren, moet u zich aanmelden bij de Azure-portal via https://portal.azure.com.

Ga naar Azure Container Registry en selecteer **Replicaties**:

![Replicaties in de containerregister-UI van Azure Portal](media/container-registry-geo-replication/registry-services.png)

Er wordt een kaart weergegeven met alle huidige Azure-regio's:

 ![Kaart met regio's in Azure Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Blauwe zeshoeken staan voor huidige replica's
* Groene zeshoeken staan voor mogelijk replicatieregio's
* Grijze zeshoeken staan voor Azure-regio's die nog niet beschikbaar zijn voor replicatie

Als u een replica wilt configureren, selecteert u een groene zeshoek en selecteert u vervolgens **Maken**:

 ![Gebruikersinterface voor het maken van een replicatie in Azure Portal](media/container-registry-geo-replication/create-replication.png)

Als u extra replica's wilt configureren, selecteert u de groene zeshoeken voor andere regio's en klikt u op **Maken**.

ACR begint installatiekopieën te synchroniseren voor de geconfigureerde replica's. Zodra dit is voltooid, geeft de portal *Gereed* weer. De status van de replica in de portal wordt niet automatisch bijgewerkt. Gebruik de vernieuwknop om de bijgewerkte status te bekijken.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Overwegingen voor het gebruik van een geo-gerepliceerd REGI ster

* Elke regio in een geo-gerepliceerd REGI ster is onafhankelijk nadat deze is ingesteld. Azure Container Registry Sla's gelden voor elke regio met geo-replicatie.
* Wanneer u installatie kopieën vanuit een geo-gerepliceerd REGI ster pusht of pullt, verzendt Azure Traffic Manager op de achtergrond de aanvraag naar het REGI ster in de regio die het dichtst bij u in de buurt is van de netwerk latentie.
* Nadat u een installatie kopie of label update naar de dichtstbijzijnde regio hebt gepusht, duurt het enige tijd voor Azure Container Registry om de manifesten en lagen te repliceren naar de overige regio's waarin u zich hebt aangemeld. Grotere afbeeldingen nemen meer tijd in beslag dan kleinere bestanden. Afbeeldingen en tags worden gesynchroniseerd over de replicatie regio's met een mogelijk consistentie model.
* Voor het beheren van werk stromen die afhankelijk zijn van push-updates naar een geo-gerepliceerd REGI ster, wordt u aangeraden [webhooks](container-registry-webhook.md) zodanig te configureren dat deze reageert op push gebeurtenissen. U kunt regionale webhooks instellen binnen een geo-gerepliceerd REGI ster om Push gebeurtenissen bij te houden die worden uitgevoerd in de geografisch gerepliceerde regio's.
* Azure Container Registry gebruikt gegevens eindpunten als u blobs wilt leveren die inhouds lagen vertegenwoordigen. U kunt [specifieke gegevens eindpunten](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) voor uw REGI ster inschakelen in elk van de geo-replicatie regio's van uw REGI ster. Met deze eind punten kan de nauw keurig bereik van Firewall toegangs regels worden geconfigureerd. Voor het oplossen van problemen kunt u eventueel [route ring naar een replicatie uitschakelen](#temporarily-disable-routing-to-replication) terwijl u gerepliceerde gegevens bijhoudt.
* Als u een [persoonlijke koppeling](container-registry-private-link.md) voor uw REGI ster configureert met behulp van privé-eind punten in een virtueel netwerk, worden toegewezen gegevens eindpunten in elk van de geo-gerepliceerde regio's standaard ingeschakeld. 

## <a name="delete-a-replica"></a>Een replica verwijderen

Nadat u een replica voor uw REGI ster hebt geconfigureerd, kunt u deze op elk gewenst moment verwijderen als deze niet meer nodig is. Verwijder een replica met de Azure Portal of andere hulpprogram ma's, zoals de opdracht [AZ ACR Replication delete](/cli/azure/acr/replication#az-acr-replication-delete) in de Azure cli.

Een replica verwijderen in de Azure Portal:

1. Navigeer naar uw Azure Container Registry en selecteer **replicaties**.
1. Selecteer de naam van een replica en selecteer **verwijderen**. Bevestig dat u de replica wilt verwijderen.

Als u de Azure CLI wilt gebruiken om een replica van *myregistry* te verwijderen uit de regio VS-Oost:

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>Prijzen van geo-replicatie

Geo-replicatie is een functie van de [Premium-servicelaag](container-registry-skus.md) van Azure container Registry. Wanneer u een register naar de gewenste regio's repliceert, worden er kosten voor het Premium-register voor elke regio gemaakt.

In het voorgaande voorbeeld ging Contoso van twee registers naar één en voegde het bedrijf replica's toe aan VS - oost, Canada - centraal en Europa - west. Contoso zou vier keer per maand Premium betalen, zonder extra configuratie of beheer. Elke regio haalt nu installatiekopieën lokaal op, wat zorgt voor betere prestaties en een hogere betrouwbaarheid zonder kosten voor uitgaand netwerkverkeer voor VS - west naar Canada en VS - oost.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Problemen met push bewerkingen met geo-gerepliceerde registers oplossen
 
Een docker-client die een installatie kopie naar een geo-gerepliceerd REGI ster pusht, kan niet alle afbeeldings lagen en het bijbehorende manifest naar één gerepliceerde regio pushen. Dit kan gebeuren omdat Azure-Traffic Manager register aanvragen naar het netwerk-dichtstbijzijnde gerepliceerde REGI ster stuurt. Als het REGI ster twee regio's voor replicatie in de *buurt* heeft, kunnen afbeeldings lagen en het manifest naar de twee sites worden gedistribueerd en mislukt de push bewerking wanneer het manifest is gevalideerd. Dit probleem wordt veroorzaakt door de manier waarop de DNS-naam van het REGI ster op sommige Linux-hosts wordt opgelost. Dit probleem doet zich niet voor in Windows, dat een DNS-cache aan de client zijde biedt.
 
Als dit probleem optreedt, kunt u een DNS-cache aan de client zijde Toep assen, zoals `dnsmasq` op de Linux-host. Dit helpt ervoor te zorgen dat de naam van het REGI ster consistent wordt opgelost. Als u een virtuele Linux-machine in azure gebruikt om naar een REGI ster te pushen, raadpleegt u opties in [DNS-naam omzettings opties voor virtuele Linux-machines in azure](../virtual-machines/linux/azure-dns.md).

Als u de DNS-omzetting naar de dichtstbijzijnde replica tijdens het pushen van installatie kopieën wilt optimaliseren, configureert u een geo-gerepliceerd REGI ster in dezelfde Azure-regio's als de bron van de push bewerkingen of de dichtstbijzijnde regio bij het werken buiten Azure.

### <a name="temporarily-disable-routing-to-replication"></a>Route ring naar replicatie tijdelijk uitschakelen

Als u bewerkingen wilt uitvoeren met een geo-gerepliceerd REGI ster, wilt u Traffic Manager route ring mogelijk tijdelijk uitschakelen voor een of meer replicaties. Vanaf Azure CLI versie 2,8 kunt u een `--region-endpoint-enabled` optie (preview) configureren wanneer u een gerepliceerde regio maakt of bijwerkt. Wanneer u de optie van een replicatie instelt `--region-endpoint-enabled` op `false` , stuurt Traffic Manager geen push-of pull-aanvragen meer naar die regio. Standaard wordt route ring naar alle replicaties ingeschakeld en wordt de gegevens synchronisatie voor alle replicaties uitgevoerd, ongeacht of route ring is in-of uitgeschakeld.

Als u route ring naar een bestaande replicatie wilt uitschakelen, voert u eerst [AZ ACR Replication List][az-acr-replication-list] uit om de replicaties in het REGI ster weer te geven. Vervolgens voert u [AZ ACR-replicatie update][az-acr-replication-update] uit en stelt u deze `--region-endpoint-enabled false` in voor een specifieke replicatie. Als u bijvoorbeeld de instelling voor de *westus* -replicatie in *myregistry* wilt configureren:

```azurecli
# Show names of existing replications
az acr replication list --registry --output table

# Disable routing to replication
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled false
```

Route ring naar een replicatie herstellen:

```azurecli
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled true
```

## <a name="next-steps"></a>Volgende stappen

Bekijk de driedelige zelfstudiereeks [Geo-replicatie in Azure Container Registry](container-registry-tutorial-prepare-registry.md). Ontdek hoe u een geo-gerepliceerd register maakt, een container bouwt en het vervolgens met één `docker push`-opdracht implementeert naar meerdere regionale Web App for Containers-instanties.

> [!div class="nextstepaction"]
> [Geo-replicatie in Azure Container Registry](container-registry-tutorial-prepare-registry.md)

[az-acr-replication-list]: /cli/azure/acr/replication#az-acr-replication-list
[az-acr-replication-update]: /cli/azure/acr/replication#az-acr-replication-update
