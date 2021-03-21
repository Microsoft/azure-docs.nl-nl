---
title: Container groep bijwerken
description: Meer informatie over het bijwerken van actieve containers in uw Azure Container Instances-container groepen.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: fb31eeda83532c408a303e879439006bcd7d4e45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200645"
---
# <a name="update-containers-in-azure-container-instances"></a>Containers in Azure Container Instances bijwerken

Tijdens de normale werking van uw container instanties is het wellicht nood zakelijk om de actieve containers in een [container groep](./container-instances-container-groups.md)bij te werken. U kunt bijvoorbeeld een eigenschap bijwerken zoals een installatie kopie versie, een DNS-naam of een omgevings variabele, of een eigenschap vernieuwen in een container waarvan de toepassing is gecrasht.

Werk de containers in een actieve container groep bij door een bestaande groep opnieuw te implementeren met ten minste één gewijzigde eigenschap. Wanneer u een container groep bijwerkt, worden alle actieve containers in de groep op locatie opnieuw gestart, meestal op dezelfde onderliggende container host.

> [!NOTE]
> Beëindigde of verwijderde container groepen kunnen niet worden bijgewerkt. Als een container groep is beëindigd (heeft een geslaagde of mislukte status) of is verwijderd, moet de groep worden geïmplementeerd als nieuw. Zie andere [beperkingen](#limitations).

## <a name="update-a-container-group"></a>Een container groep bijwerken

Een bestaande container groep bijwerken:

* Voer de opdracht Create (of gebruik de Azure Portal) uit en geef de naam van een bestaande groep op 
* Wijzig of Voeg ten minste één eigenschap van de groep toe die de update ondersteunt wanneer u deze opnieuw implementeert. Bepaalde eigenschappen [bieden geen ondersteuning voor updates](#properties-that-require-container-delete).
* Stel andere eigenschappen in met de waarden die u eerder hebt opgegeven. Als u geen waarde voor een eigenschap instelt, keert deze terug naar de standaard waarde.

> [!TIP]
> Een [yaml-bestand](./container-instances-container-groups.md#deployment) helpt bij het onderhouden van de implementatie configuratie van een container groep en biedt een start punt voor het implementeren van een bijgewerkte groep. Als u een andere methode hebt gebruikt om de groep te maken, kunt u de configuratie exporteren naar YAML met [AZ container export][az-container-export], 

### <a name="example"></a>Voorbeeld

In het volgende voor beeld van Azure CLI wordt een container groep bijgewerkt met een nieuwe DNS-naam label. Omdat de eigenschap van het DNS-naam label van de groep een kan worden bijgewerkt, wordt de container groep opnieuw geïmplementeerd en opnieuw gestart.

Eerste implementatie met DNS-naam label *mijn toepassing-staging*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Werk de container groep bij met een nieuwe DNS-naam label, *mijn toepassing* en stel de resterende eigenschappen in met de eerder gebruikte waarden:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Voor delen bijwerken

Het belangrijkste voor deel van het bijwerken van een bestaande container groep is een snellere implementatie. Wanneer u een bestaande container groep opnieuw implementeert, worden de bijbehorende container afbeeldings lagen opgehaald uit de caches van de vorige implementatie. In plaats van alle afbeeldings lagen uit het REGI ster te halen, zoals wordt uitgevoerd met nieuwe implementaties, worden alleen gewijzigde lagen (indien aanwezig) opgehaald.

Toepassingen die zijn gebaseerd op grotere container installatie kopieën, zoals Windows Server Core, kunnen aanzienlijke verbetering van de implementatie snelheid zien wanneer u een update in plaats van verwijderen en nieuwe implementeren.

## <a name="limitations"></a>Beperkingen

* Niet alle eigenschappen van een container groep ondersteunen updates. Als u bepaalde eigenschappen van een container groep wilt wijzigen, moet u eerst de groep verwijderen en vervolgens opnieuw implementeren. Zie [eigenschappen waarvoor container verwijderen is vereist](#properties-that-require-container-delete).
* Alle containers in een container groep worden opnieuw gestart wanneer u de container groep bijwerkt. Het is niet mogelijk om een update uit te voeren of een specifieke container in een groep met meerdere containers opnieuw op te starten.
* Het IP-adres van een container groep wordt doorgaans bewaard tussen updates, maar is niet gegarandeerd hetzelfde. Zolang de container groep is geïmplementeerd op dezelfde onderliggende host, behoudt de container groep zijn IP-adres. Hoewel zeldzame gevallen, zijn er enkele Azure-interne gebeurtenissen die de implementatie naar een andere host kunnen veroorzaken. Als u dit probleem wilt verhelpen, raden we u aan om een DNS-naam label te gebruiken voor uw container exemplaren.
* Beëindigde of verwijderde container groepen kunnen niet worden bijgewerkt. Zodra een container groep is gestopt (de status is *beëindigd* ) of verwijderd, wordt de groep geïmplementeerd als nieuw.

## <a name="properties-that-require-container-delete"></a>Eigenschappen waarvoor container verwijderen is vereist

Niet alle eigenschappen van de container groep kunnen worden bijgewerkt. Als u bijvoorbeeld het beleid voor het opnieuw opstarten van een container wilt wijzigen, moet u eerst de container groep verwijderen en vervolgens opnieuw maken.

Voor wijzigingen in deze eigenschappen moet de container groep worden verwijderd voordat deze opnieuw kan worden geïmplementeerd:

* Type besturings systeem
* CPU-, geheugen-of GPU-resources
* Beleid voor opnieuw starten
* Netwerkprofiel

Wanneer u een container groep verwijdert en opnieuw maakt, wordt deze niet opnieuw geïmplementeerd, maar is er nieuwe gemaakt. Alle afbeeldings lagen worden vernieuwd uit het REGI ster, niet uit de caches van een vorige implementatie. Het IP-adres van de container kan ook worden gewijzigd omdat deze is geïmplementeerd op een andere onderliggende host.

## <a name="next-steps"></a>Volgende stappen

Dit artikel bevat meerdere keren de **container groep**. Elke container in Azure Container Instances wordt geïmplementeerd in een container groep en container groepen kunnen meer dan één container bevatten.

[Containergroepen in Azure Container Instances](./container-instances-container-groups.md)

[Een groep met meerdere containers implementeren](container-instances-multi-container-group.md)

[Containers in Azure Container Instances handmatig stoppen of starten](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az-container-export
