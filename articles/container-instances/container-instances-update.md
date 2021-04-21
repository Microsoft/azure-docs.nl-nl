---
title: Containergroep bijwerken
description: Meer informatie over het bijwerken van containers die worden uitgevoerd in Azure Container Instances containergroepen.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: cbb2e830490d2591645b8156ee830856da0f9049
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786959"
---
# <a name="update-containers-in-azure-container-instances"></a>Containers in Azure Container Instances bijwerken

Tijdens de normale werking van uw container-exemplaren is het mogelijk nodig om de containers die worden uitgevoerd in een [containergroep bij te werken.](./container-instances-container-groups.md) U kunt bijvoorbeeld een eigenschap bijwerken, zoals een versie van een afbeelding, een DNS-naam of een omgevingsvariabele, of een eigenschap vernieuwen in een container waarvan de toepassing is gecrasht.

Werk de containers in een containergroep die wordt uitgevoerd bij door een bestaande groep met ten minste één gewijzigde eigenschap opnieuw teploeeren. Wanneer u een containergroep bij werkt, worden alle containers die in de groep worden uitgevoerd, opnieuw opgestart, meestal op dezelfde onderliggende containerhost.

> [!NOTE]
> Beëindigde of verwijderde containergroepen kunnen niet worden bijgewerkt. Zodra een containergroep is beëindigd (de status Geslaagd of Mislukt heeft) of is verwijderd, moet de groep als nieuw worden geïmplementeerd. Zie andere [beperkingen.](#limitations)

## <a name="update-a-container-group"></a>Een containergroep bijwerken

Een bestaande containergroep bijwerken:

* Voer de opdracht create uit (of gebruik de Azure Portal) en geef de naam van een bestaande groep op 
* Wijzig of voeg ten minste één eigenschap toe van de groep die ondersteuning biedt voor bijwerken wanneer u de groep opnieuw wilt installeren. Bepaalde eigenschappen [bieden geen ondersteuning voor updates.](#properties-that-require-container-delete)
* Stel andere eigenschappen in met de waarden die u eerder hebt opgegeven. Als u geen waarde voor een eigenschap in stelt, wordt de standaardwaarde ervan terug gezet.

> [!TIP]
> Een [YAML-bestand](./container-instances-container-groups.md#deployment) helpt bij het onderhouden van de implementatieconfiguratie van een containergroep en biedt een beginpunt voor het implementeren van een bijgewerkte groep. Als u een andere methode hebt gebruikt om de groep te maken, kunt u de configuratie exporteren naar YAML met [az container export][az-container-export], 

### <a name="example"></a>Voorbeeld

In het volgende Azure CLI-voorbeeld wordt een containergroep bijgewerkt met een nieuw DNS-naamlabel. Omdat de eigenschap van het DNS-naamlabel van de groep kan worden bijgewerkt, wordt de containergroep opnieuw geïmplementeerd en worden de containers opnieuw gestart.

Eerste implementatie met DNS-naamlabel *myapplication-staging:*

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Werk de containergroep bij met een nieuw DNS-naamlabel, *myapplication,* en stel de resterende eigenschappen in met de waarden die u eerder hebt gebruikt:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Voordelen bijwerken

Het belangrijkste voordeel van het bijwerken van een bestaande containergroep is een snellere implementatie. Wanneer u een bestaande containergroep opnieuw wilt implementeren, worden de lagen van de containerafbeeldingen gehaald uit de lagen die in de cache zijn opgeslagen door de vorige implementatie. In plaats van alle installatielaag nieuw uit het register te halen, zoals bij nieuwe implementaties, worden alleen gewijzigde lagen (indien van toepassing) uit het register gehaald.

Toepassingen die zijn gebaseerd op grotere containerafbeeldingen, zoals Windows Server Core, kunnen een aanzienlijke verbetering in de implementatiesnelheid zien wanneer u bijrolt in plaats van nieuwe te verwijderen en te implementeren.

## <a name="limitations"></a>Beperkingen

* Niet alle eigenschappen van een containergroep ondersteunen updates. Als u enkele eigenschappen van een containergroep wilt wijzigen, moet u eerst verwijderen en vervolgens de groep opnieuwployeren. Zie [Eigenschappen waarvoor het verwijderen van de container is vereist.](#properties-that-require-container-delete)
* Alle containers in een containergroep worden opnieuw opgestart wanneer u de containergroep bij te werken. U kunt een specifieke container in een groep met meerdere containers niet bijwerken of in-place opnieuw opstarten.
* Het IP-adres van een containergroep wordt doorgaans bewaard tussen updates, maar blijft niet gegarandeerd hetzelfde. Zolang de containergroep is geïmplementeerd op dezelfde onderliggende host, behoudt de containergroep het IP-adres. Hoewel dit zeldzaam is, zijn er enkele interne Azure-gebeurtenissen die kunnen leiden tot een herdeployment op een andere host. Om dit probleem te verhelpen, raden we u aan een DNS-naamlabel te gebruiken voor uw container-exemplaren.
* Beëindigde of verwijderde containergroepen kunnen niet worden bijgewerkt. Zodra een containergroep is gestopt (heeft de status *Beëindigd)* of verwijderd, wordt de groep geïmplementeerd als nieuw.

## <a name="properties-that-require-container-delete"></a>Eigenschappen waarvoor het verwijderen van containers is vereist

Niet alle eigenschappen van containergroep kunnen worden bijgewerkt. Als u bijvoorbeeld het beleid voor opnieuw opstarten van een container wilt wijzigen, moet u eerst de containergroep verwijderen en deze vervolgens opnieuw maken.

Wijzigingen in deze eigenschappen vereisen verwijdering van containergroep vóór de herployment:

* Type besturingssysteem
* CPU-, geheugen- of GPU-resources
* Beleid voor opnieuw starten
* Netwerkprofiel

Wanneer u een containergroep verwijdert en opnieuw maakt, wordt deze niet opnieuw geïmplementeerd, maar nieuwe gemaakt. Alle installatielaag wordt opnieuw uit het register gehaald, niet uit de lagen die door een eerdere implementatie in de cache zijn opgeslagen. Het IP-adres van de container kan ook worden gewijzigd omdat het is geïmplementeerd op een andere onderliggende host.

## <a name="next-steps"></a>Volgende stappen

De containergroep die meerdere keren in dit artikel **wordt vermeld,** is . Elke container in Azure Container Instances wordt geïmplementeerd in een containergroep en containergroepen kunnen meer dan één container bevatten.

[Containergroepen in Azure Container Instances](./container-instances-container-groups.md)

[Een groep met meerdere containers implementeren](container-instances-multi-container-group.md)

[Containers in Azure Container Instances handmatig stoppen of starten](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az_container_export
