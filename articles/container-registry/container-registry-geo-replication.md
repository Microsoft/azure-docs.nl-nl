---
title: Geo-replicatie toepassen voor een register
description: Ga aan de slag met het maken en beheren van een Azure-containerregister met geo-replicatie, waarmee het register meerdere regio's kan bedienen met regionale replica's voor meerdere masters. Geo-replicatie is een functie van de Premium-servicelaag.
author: stevelas
ms.topic: article
ms.date: 07/21/2020
ms.author: stevelas
ms.openlocfilehash: 3e5b064ec37b855186f633677e2b1a3f615a6736
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783859"
---
# <a name="geo-replication-in-azure-container-registry"></a>Geo-replicatie in Azure Container Registry

Bedrijven die lokale aanwezigheid willen, of een back-up zonder opnieuw opstarten, kunnen services uitvoeren vanuit meerdere Azure-regio's. Het plaatsen van een containerregister in elke regio waarin installatiekopieën worden uitgevoerd, is een best practice die bewerkingen dicht bij het netwerk mogelijk maakt, wat zorgt voor snelle, betrouwbare overdrachten van installatiekopielagen. Met geo-replicatie kan een Azure-containerregister als één register functioneren en meerdere regio's bedienen met regionale registers voor meerdere masters. 

Een register met geo-replicatie biedt de volgende voordelen:

* Namen van één register, afbeelding en tag kunnen worden gebruikt in meerdere regio's
* Prestaties en betrouwbaarheid van regionale implementaties verbeteren met registertoegang dicht bij het netwerk
* Kosten voor gegevensoverdracht verlagen door de lagen van de afbeelding op te halen uit een lokaal, gerepliceerd register in dezelfde of in de buurt als uw containerhost
* Eén beheerpunt voor een register in meerdere regio's
* Register tolerantie als er een regionale storing optreedt

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
* Beheer één configuratie van implementaties van installatie afbeeldingen omdat alle regio's dezelfde afbeeldings-URL gebruiken: `contoso.azurecr.io/public/products/web:1.2`
* Push naar één register, terwijl ACR de geo-replicatie beheert. ACR repliceert alleen unieke lagen, waardoor de gegevensoverdracht tussen regio's wordt verkleind. 
* Configureer [regionale webhooks om](container-registry-webhook.md) u op de hoogte te stellen van gebeurtenissen in specifieke replica's.
* Een register met hoge beschikbare gegevens bieden dat bestand is tegen regionale uitval.

Azure Container Registry ook [beschikbaarheidszones voor het](zone-redundancy.md) maken van een robuust Azure-containerregister met hoge beschikbaarheid binnen een Azure-regio. De combinatie van beschikbaarheidszones voor redundantie binnen een regio en geo-replicatie tussen meerdere regio's verbetert zowel de betrouwbaarheid als prestaties van een register.

## <a name="configure-geo-replication"></a>Geo-replicatie configureren

Het configureren van geo-replicatie is net zo gemakkelijk als regio's aanklikken op een kaart. U kunt geo-replicatie ook beheren met hulpprogramma's zoals [de opdrachten az acr replication](/cli/azure/acr/replication) in de Azure CLI of een register implementeren dat is ingeschakeld voor geo-replicatie met een Azure Resource Manager [sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication).

Geo-replicatie is een functie van [Premium-registers](container-registry-skus.md). Als uw register nog niet Premium is, kunt u overstappen van Basic en Standard naar Premium in de [Azure-portal](https://portal.azure.com):

![Schakelen tussen servicelagen in de Azure Portal](media/container-registry-skus/update-registry-sku.png)

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

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Overwegingen voor het gebruik van een geo-gerepliceerd register

* Elke regio in een geo-gerepliceerd register is onafhankelijk na het instellen. Azure Container Registry SLA's zijn van toepassing op elke geografisch gerepliceerde regio.
* Wanneer u afbeeldingen pusht of pullt uit een geo-gerepliceerd register, verzendt Azure Traffic Manager op de achtergrond de aanvraag naar het register in de regio die zich het dichtst bij u in de buurt bevindt wat betreft netwerklatentie.
* Nadat u een update van een afbeelding of tag naar de dichtstbijzijnde regio hebt pusht, duurt het enige tijd Azure Container Registry om de manifesten en lagen te repliceren naar de resterende regio's waar u voor hebt gekozen. Grotere afbeeldingen duren langer om te repliceren dan kleinere. Afbeeldingen en tags worden gesynchroniseerd in de replicatieregio's met een model voor uiteindelijke consistentie.
* Voor het beheren van werkstromen die afhankelijk zijn van push-updates naar een geo-gerepliceerd register, raden we u aan [webhooks](container-registry-webhook.md) te configureren om te reageren op de pushgebeurtenissen. U kunt regionale webhooks binnen een geo-gerepliceerd register instellen om pushgebeurtenissen bij te houden terwijl deze worden voltooid in de geografisch gerepliceerde regio's.
* Voor het leveren van blobs die inhoudslagen vertegenwoordigen, Azure Container Registry gegevens-eindpunten gebruikt. U kunt toegewezen [gegevens eindpunten inschakelen](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) voor uw register in elk van de geografisch gerepliceerde regio's van uw register. Met deze eindpunten kunt u firewalltoegangsregels met een nauw bereik configureren. Voor het oplossen van problemen kunt u routering naar een replicatie desgewenst uitschakelen [terwijl](#temporarily-disable-routing-to-replication) de gerepliceerde gegevens behouden blijven.
* Als u een [privékoppeling](container-registry-private-link.md) voor uw register configureert met behulp van privé-eindpunten in een virtueel netwerk, worden toegewezen gegevens eindpunten in elk van de geografisch gerepliceerde regio's standaard ingeschakeld. 

## <a name="delete-a-replica"></a>Een replica verwijderen

Nadat u een replica voor uw register hebt geconfigureerd, kunt u deze op elk gewenst moment verwijderen als deze niet meer nodig is. Verwijder een replica met behulp Azure Portal hulpprogramma's of andere hulpprogramma's, zoals de [opdracht az acr replication delete](/cli/azure/acr/replication#az_acr_replication_delete) in de Azure CLI.

Een replica in de Azure Portal:

1. Navigeer naar Azure Container Registry en selecteer **Replicaties.**
1. Selecteer de naam van een replica en selecteer **Verwijderen.** Bevestig dat u de replica wilt verwijderen.

De Azure CLI gebruiken om een replica van *myregistry* te verwijderen in de regio VS - oost:

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>Prijzen van geo-replicatie

Geo-replicatie is een functie van de [Premium-servicelaag](container-registry-skus.md) van Azure Container Registry. Wanneer u een register naar de gewenste regio's repliceert, worden er kosten voor het Premium-register voor elke regio gemaakt.

In het voorgaande voorbeeld ging Contoso van twee registers naar één en voegde het bedrijf replica's toe aan VS - oost, Canada - centraal en Europa - west. Contoso zou vier keer per maand Premium betalen, zonder extra configuratie of beheer. Elke regio haalt nu installatiekopieën lokaal op, wat zorgt voor betere prestaties en een hogere betrouwbaarheid zonder kosten voor uitgaand netwerkverkeer voor VS - west naar Canada en VS - oost.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Problemen met pushbewerkingen met geo-gerepliceerde registers oplossen
 
Een Docker-client die een afbeelding naar een geo-gerepliceerd register pusht, pusht mogelijk niet alle lagen van de afbeelding en het manifest ervan naar één gerepliceerde regio. Dit kan gebeuren omdat Azure Traffic Manager registeraanvragen naar het netwerk dichtstbijzijnde gerepliceerde register routeert. Als het register  twee replicatieregio's in de buurt heeft, kunnen afbeeldingslagen en het manifest worden gedistribueerd naar de twee sites en mislukt de pushbewerking wanneer het manifest wordt gevalideerd. Dit probleem treedt op vanwege de manier waarop de DNS-naam van het register wordt opgelost op sommige Linux-hosts. Dit probleem doet zich niet voor in Windows, dat een DNS-cache aan de clientzijde biedt.
 
Als dit probleem zich voordoet, is één oplossing het toepassen van een DNS-cache aan de clientzijde, zoals `dnsmasq` op de Linux-host. Dit zorgt ervoor dat de naam van het register consistent wordt opgelost. Als u een virtuele Linux-machine in Azure gebruikt om naar een register te pushen, bekijkt u opties in Opties voor DNS-naamresolutie voor [virtuele Linux-machines in Azure.](../virtual-machines/linux/azure-dns.md)

Als u de DNS-resolutie naar de dichtstbijzijnde replica wilt optimaliseren bij het pushen van afbeeldingen, configureert u een geo-gerepliceerd register in dezelfde Azure-regio's als de bron van de pushbewerkingen of de dichtstbijzijnde regio wanneer u buiten Azure werkt.

### <a name="temporarily-disable-routing-to-replication"></a>Routering naar replicatie tijdelijk uitschakelen

Als u problemen met bewerkingen met een geo-gerepliceerd register wilt oplossen, kunt u de routering Traffic Manager naar een of meer replicaties tijdelijk uitschakelen. Vanaf Azure CLI versie 2.8 kunt u een optie (preview) configureren wanneer u een gerepliceerde regio `--region-endpoint-enabled` maakt of bij bijgewerkt. Wanneer u de optie van een replicatie in stelt op , Traffic Manager niet langer `--region-endpoint-enabled` `false` docker-push- of pull-aanvragen naar die regio door. Routering naar alle replicaties is standaard ingeschakeld en gegevenssynchronisatie voor alle replicaties vindt plaats, ongeacht of routering is ingeschakeld of uitgeschakeld.

Als u routering naar een bestaande replicatie wilt uitschakelen, moet u [eerst az acr replication list uitvoeren om][az-acr-replication-list] de replicaties in het register weer te geeft. Voer vervolgens [az acr replication update uit][az-acr-replication-update] en stel in voor een specifieke `--region-endpoint-enabled false` replicatie. Als u bijvoorbeeld de instelling voor de replicatie *westus* in *myregistry wilt configureren:*

```azurecli
# Show names of existing replications
az acr replication list --registry --output table

# Disable routing to replication
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled false
```

Routering herstellen naar een replicatie:

```azurecli
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled true
```

## <a name="next-steps"></a>Volgende stappen

Bekijk de driedelige zelfstudiereeks [Geo-replicatie in Azure Container Registry](container-registry-tutorial-prepare-registry.md). Ontdek hoe u een geo-gerepliceerd register maakt, een container bouwt en het vervolgens met één `docker push`-opdracht implementeert naar meerdere regionale Web App for Containers-instanties.

> [!div class="nextstepaction"]
> [Geo-replicatie in Azure Container Registry](container-registry-tutorial-prepare-registry.md)

[az-acr-replication-list]: /cli/azure/acr/replication#az_acr_replication_list
[az-acr-replication-update]: /cli/azure/acr/replication#az_acr_replication_update
