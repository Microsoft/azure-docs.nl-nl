---
title: Toegang tot openbare register configureren
description: Configureer IP-regels voor toegang tot een Azure container Registry vanuit geselecteerde open bare IP-adressen of adresbereiken.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 4e4ee817e2534bcca09cba89daafd379ff3f03f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89488759"
---
# <a name="configure-public-ip-network-rules"></a>Open bare IP-netwerk regels configureren

Een Azure-container register accepteert standaard verbindingen via Internet van hosts op elk netwerk. In dit artikel wordt uitgelegd hoe u het container register zo configureert dat alleen specifieke open bare IP-adressen of adresbereiken toegankelijk zijn. Vergelijk bare stappen die gebruikmaken van de Azure CLI en Azure Portal worden meegeleverd.

IP-netwerk regels worden geconfigureerd op het open bare register eindpunt. IP-netwerk regels zijn niet van toepassing op privé-eind punten die zijn geconfigureerd met een [persoonlijke koppeling](container-registry-private-link.md)

Het configureren van IP-toegangs regels is beschikbaar in de service tier van het **Premium** -container register. Zie [Azure container Registry-lagen](container-registry-skus.md)voor meer informatie over de service lagen en limieten voor het REGI ster.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="access-from-selected-public-network---cli"></a>Toegang vanuit geselecteerd openbaar netwerk-CLI

### <a name="change-default-network-access-to-registry"></a>De standaard netwerk toegang wijzigen in het REGI ster

Als u de toegang tot een geselecteerd openbaar netwerk wilt beperken, wijzigt u eerst de standaard actie om de toegang te weigeren. Vervang de naam van het REGI ster in de volgende [AZ ACR update][az-acr-update] -opdracht:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Netwerk regel toevoegen aan REGI ster

Gebruik de opdracht [AZ ACR Network-Rule add][az-acr-network-rule-add] om een netwerk regel toe te voegen aan uw REGI ster waarmee toegang vanaf een openbaar IP-adres of-bereik is toegestaan. Vervang bijvoorbeeld de naam van het container register en het open bare IP-adres van een virtuele machine in een virtueel netwerk.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> Na het toevoegen van een regel duurt het enkele minuten voordat de regel van kracht wordt.

## <a name="access-from-selected-public-network---portal"></a>Toegang vanuit geselecteerd openbaar netwerk-Portal

1. Navigeer in de portal naar het container register.
1. Selecteer onder **instellingen** de optie **netwerken**.
1. Schakel op het tabblad **open bare toegang** het selectie vakje open bare toegang tot **geselecteerde netwerken** toe.
1. Voer onder **firewall** een openbaar IP-adres in, zoals het open bare IP-adres van een virtuele machine in een virtueel netwerk. Of voer een adres bereik in de CIDR-notatie in die het IP-adres van de virtuele machine bevat.
1. Selecteer **Opslaan**.

![Firewall regel voor container register configureren][acr-access-selected-networks]

> [!NOTE]
> Na het toevoegen van een regel duurt het enkele minuten voordat de regel van kracht wordt.

> [!TIP]
> Schakel eventueel toegang tot het REGI ster in vanaf een lokale client computer of een IP-adres bereik. U hebt het open bare IPv4-adres van de computer nodig om deze toegang toe te staan. U kunt dit adres vinden door te zoeken in ' wat is mijn IP-adres ' in een Internet browser. Het huidige client IPv4-adres wordt ook automatisch weer gegeven wanneer u Firewall-instellingen configureert op de pagina **netwerk** in de portal.

## <a name="disable-public-network-access"></a>Open bare netwerk toegang uitschakelen

U kunt het open bare eind punt ook uitschakelen in het REGI ster. Als u het open bare eind punt uitschakelt, worden alle firewall configuraties overschreven. U kunt bijvoorbeeld open bare toegang tot een REGI ster uitschakelen dat is beveiligd in een virtueel netwerk met behulp van een [persoonlijke koppeling](container-registry-private-link.md).

> [!NOTE]
> Als het REGI ster is ingesteld in een virtueel netwerk met een [service-eind punt](container-registry-vnet.md)en de toegang tot het open bare eind punt van het REGI ster wordt uitgeschakeld, wordt de toegang tot het REGI ster in het virtuele netwerk uitgeschakeld.

### <a name="disable-public-access---cli"></a>Open bare toegang uitschakelen-CLI

Als u open bare toegang wilt uitschakelen met behulp van de Azure CLI, voert u [AZ ACR update][az-acr-update] uit en stelt `--public-network-enabled` u in op `false` . Het `public-network-enabled` argument vereist Azure CLI 2.6.0 of hoger. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled false
```

### <a name="disable-public-access---portal"></a>Open bare toegang uitschakelen-Portal

1. Ga in de portal naar het container register en selecteer **instellingen > netwerk**.
1. Selecteer op het tabblad **open bare toegang** in **open bare netwerk toegang toestaan** de optie **uitgeschakeld**. Selecteer vervolgens **Opslaan**.

![Open bare toegang uitschakelen][acr-access-disabled]


## <a name="restore-public-network-access"></a>Open bare netwerk toegang herstellen

Als u het open bare eind punt weer wilt inschakelen, werkt u de netwerk instellingen bij om open bare toegang toe te staan. Als het open bare eind punt wordt ingeschakeld, worden alle firewall configuraties overschreven. 

### <a name="restore-public-access---cli"></a>Open bare toegang herstellen-CLI

Voer [AZ ACR update][az-acr-update] en ingesteld `--public-network-enabled` op `true` . 

> [!NOTE]
> Het `public-network-enabled` argument vereist Azure CLI 2.6.0 of hoger. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled true
```

### <a name="restore-public-access---portal"></a>Open bare toegang herstellen-Portal

1. Ga in de portal naar het container register en selecteer **instellingen > netwerk**.
1. Selecteer op het tabblad **open bare toegang** in **open bare netwerk toegang toestaan** **alle netwerken**. Selecteer vervolgens **Opslaan**.

![Open bare toegang vanuit alle netwerken][acr-access-all-networks]

## <a name="troubleshoot"></a>Problemen oplossen

Als een regel voor een openbaar netwerk is ingesteld of open bare toegang tot het REGI ster is geweigerd, mislukt het aanmelden bij het REGI ster vanuit een niet-toegestaan openbaar netwerk. Client toegang vanaf achter een HTTPS-proxy mislukt ook als er geen toegangs regel voor de proxy is ingesteld. Er wordt een fout bericht weer gegeven dat vergelijkbaar is met `Error response from daemon: login attempt failed with status: 403 Forbidden` of `Looks like you don't have access to registry` .

Deze fouten kunnen ook optreden als u een HTTPS-proxy gebruikt die wordt toegestaan door een netwerk toegangs regel, maar de proxy niet juist is geconfigureerd in de client omgeving. Controleer of de docker-client en de docker-daemon zijn geconfigureerd voor proxy gedrag. Zie [http/https-proxy](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) in de docker-documentatie voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

* Als u de toegang tot een REGI ster wilt beperken met behulp van een persoonlijk eind punt in een virtueel netwerk, raadpleegt u [Azure private link configureren voor een Azure container Registry](container-registry-private-link.md).
* Zie [regels configureren voor toegang tot een Azure container Registry achter een firewall](container-registry-firewall-access-rules.md)als u toegangs regels voor het REGI ster wilt instellen van achter een firewall van een client.

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
