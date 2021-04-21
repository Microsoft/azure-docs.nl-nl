---
title: Toegang tot openbare register configureren
description: Configureer IP-regels om toegang tot een Azure-containerregister in te stellen vanuit geselecteerde openbare IP-adressen of adresbereiken.
ms.topic: article
ms.date: 03/08/2021
ms.openlocfilehash: 00912f0e66c84feff40e6439d59ccdfa82a4ab6a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785832"
---
# <a name="configure-public-ip-network-rules"></a>Regels voor openbare IP-netwerken configureren

Een Azure-containerregister accepteert standaard verbindingen via internet van hosts in elk netwerk. In dit artikel wordt beschreven hoe u uw containerregister zo configureert dat alleen bepaalde openbare IP-adressen of adresbereiken toegang krijgen. Gelijkwaardige stappen met behulp van de Azure CLI en Azure Portal worden gegeven.

IP-netwerkregels worden geconfigureerd op het eindpunt van het openbare register. IP-netwerkregels zijn niet van toepassing op privé-eindpunten die zijn geconfigureerd met [Private Link](container-registry-private-link.md)

Het configureren van IP-toegangsregels is beschikbaar in de **servicelaag Premium** Container Registry. Zie Azure Container Registry servicelagen voor meer [informatie over registerservicelagen en -limieten.](container-registry-skus.md)

Elk register ondersteunt maximaal 100 regels voor netwerktoegang.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="access-from-selected-public-network---cli"></a>Toegang vanaf geselecteerd openbaar netwerk - CLI

### <a name="change-default-network-access-to-registry"></a>Standaardnetwerktoegang tot register wijzigen

Als u de toegang tot een geselecteerd openbaar netwerk wilt beperken, wijzigt u eerst de standaardactie om toegang te weigeren. Vervang de naam van het register in de volgende [az acr update-opdracht:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Netwerkregel toevoegen aan register

Gebruik de [opdracht az acr network-rule add][az-acr-network-rule-add] om een netwerkregel toe te voegen aan uw register die toegang toestaat vanaf een openbaar IP-adres of bereik. Vervang bijvoorbeeld de naam van het containerregister en het openbare IP-adres van een virtuele machine in een virtueel netwerk.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> Nadat u een regel hebt toegevoegd, duurt het enkele minuten voordat de regel van kracht wordt.

## <a name="access-from-selected-public-network---portal"></a>Toegang vanaf geselecteerd openbaar netwerk - portal

1. Navigeer in de portal naar uw containerregister.
1. Selecteer **onder Instellingen** de optie **Netwerken.**
1. Selecteer op **het tabblad Openbare** toegang de optie om openbare toegang vanuit geselecteerde netwerken toe te **staan.**
1. Voer **onder Firewall** een openbaar IP-adres in, zoals het openbare IP-adres van een virtuele machine in een virtueel netwerk. U kunt ook een adresbereik invoeren in CIDR-notatie dat het IP-adres van de VM bevat.
1. Selecteer **Opslaan**.

![Firewallregel configureren voor containerregister][acr-access-selected-networks]

> [!NOTE]
> Nadat u een regel hebt toegevoegd, duurt het enkele minuten voordat de regel van kracht is.

> [!TIP]
> Schakel optioneel registertoegang in vanaf een lokale clientcomputer of ip-adresbereik. Als u deze toegang wilt toestaan, hebt u het openbare IPv4-adres van de computer nodig. U kunt dit adres vinden door in een internetbrowser te zoeken naar 'What is my IP address'. Het huidige IPv4-adres van de client wordt ook automatisch weergegeven wanneer u firewallinstellingen configureert op de **pagina** Netwerken in de portal.

## <a name="disable-public-network-access"></a>Openbare netwerktoegang uitschakelen

Schakel desgewenst het openbare eindpunt in het register uit. Als u het openbare eindpunt uitschakelingt, worden alle firewallconfiguraties overschrijven. U kunt bijvoorbeeld openbare toegang tot een register dat is beveiligd in een virtueel netwerk uitschakelen met behulp [van Private Link](container-registry-private-link.md).

> [!NOTE]
> Als het register is ingesteld in [](container-registry-vnet.md)een virtueel netwerk met een service-eindpunt, wordt met het uitschakelen van toegang tot het openbare eindpunt van het register ook de toegang tot het register in het virtuele netwerk uitgeschakeld.

### <a name="disable-public-access---cli"></a>Openbare toegang uitschakelen - CLI

Als u openbare toegang wilt uitschakelen met behulp van de Azure CLI, moet [u az acr update uitvoeren][az-acr-update] en instellen op `--public-network-enabled` `false` . Voor `public-network-enabled` het argument is Azure CLI 2.6.0 of hoger vereist. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled false
```

### <a name="disable-public-access---portal"></a>Openbare toegang uitschakelen - portal

1. Navigeer in de portal naar uw containerregister en selecteer **Instellingen > Netwerken.**
1. Selecteer op **het tabblad Openbare** toegang in Openbare **netwerktoegang toestaan** de optie **Uitgeschakeld.** Selecteer vervolgens **Opslaan**.

![Openbare toegang uitschakelen][acr-access-disabled]


## <a name="restore-public-network-access"></a>Openbare netwerktoegang herstellen

Als u het openbare eindpunt opnieuw wilt inschakelen, moet u de netwerkinstellingen bijwerken om openbare toegang toe te staan. Als u het openbare eindpunt inschakelen, worden alle firewallconfiguraties overschrijven. 

### <a name="restore-public-access---cli"></a>Openbare toegang herstellen - CLI

Voer [az acr update uit][az-acr-update] en stel in op `--public-network-enabled` `true` . 

> [!NOTE]
> Voor `public-network-enabled` het argument is Azure CLI 2.6.0 of hoger vereist. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled true
```

### <a name="restore-public-access---portal"></a>Openbare toegang herstellen - portal

1. Navigeer in de portal naar het containerregister en selecteer **Instellingen > Netwerken.**
1. Selecteer op **het tabblad Openbare** toegang in Openbare **netwerktoegang toestaan** de optie Alle **netwerken.** Selecteer vervolgens **Opslaan**.

![Openbare toegang vanuit alle netwerken][acr-access-all-networks]

## <a name="troubleshoot"></a>Problemen oplossen

Als een regel voor een openbaar netwerk is ingesteld of openbare toegang tot het register wordt geweigerd, mislukken pogingen om zich vanuit een niet-toegestaan openbaar netwerk aan te melden bij het register. Clienttoegang van achter een HTTPS-proxy mislukt ook als er geen toegangsregel voor de proxy is ingesteld. Er wordt een foutbericht weergegeven dat vergelijkbaar is met `Error response from daemon: login attempt failed with status: 403 Forbidden` of `Looks like you don't have access to registry` .

Deze fouten kunnen ook optreden als u een HTTPS-proxy gebruikt die is toegestaan door een netwerktoegangsregel, maar de proxy niet juist is geconfigureerd in de clientomgeving. Controleer of zowel uw Docker-client als de Docker-daemon zijn geconfigureerd voor proxygedrag. Zie [HTTP/HTTPS-proxy](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) in de Docker-documentatie voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

* Zie Configure Azure Private Link [for an Azure container registry](container-registry-private-link.md)(Toegang tot een register beperken met behulp van een privé-eindpunt in een virtueel netwerk).
* Zie Regels configureren voor toegang tot een Azure-containerregister achter een firewall als u registertoegangsregels wilt [instellen achter een clientfirewall.](container-registry-firewall-access-rules.md)

[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az_acr_network_rule_add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az_acr_network_rule_remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az_acr_network_rule_list
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-update]: /cli/azure/acr#az_acr_update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
