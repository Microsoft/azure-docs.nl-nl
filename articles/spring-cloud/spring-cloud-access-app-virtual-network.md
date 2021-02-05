---
title: Azure lente-app voor toegang tot de cloud in het virtuele netwerk
description: Toegang tot de app in een Azure lente-Cloud in een virtueel netwerk.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/11/2020
ms.custom: devx-track-java
ms.openlocfilehash: 37c8b4bc186c217ecb27638f5f50297102345de7
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576528"
---
# <a name="access-your-application-in-a-private-network"></a>Toegang tot uw toepassing in een privé netwerk

In dit document wordt uitgelegd hoe u toegang krijgt tot een eind punt voor uw toepassing in een particulier netwerk.  Als u toegang wilt krijgen, moet u een **Azure privé-DNS-zone** maken in uw abonnement om de privé fully QUALIFIED domain name (FQDN) te vertalen naar het IP-adres.

Wanneer u een **eind punt toewijst** voor toepassingen in een Azure lente-Cloud service-exemplaar in uw virtuele netwerk, is het eind punt een particuliere FQDN. Het domein is alleen toegankelijk in het particuliere netwerk. Apps en services gebruiken het toepassings eindpunt. Ze bevatten het **test eindpunt** dat wordt beschreven in [apps en implementaties weer geven](spring-cloud-howto-staging-environment.md#view-apps-and-deployments). **Logboek streaming**, die wordt beschreven in [in realtime stream-Logboeken in de cloud van Azure](spring-cloud-howto-log-streaming.md), werkt ook alleen binnen het particuliere netwerk.

## <a name="create-a-private-dns-zone"></a>Een privé-DNS-zone maken

Met de volgende procedure maakt u een privé-DNS-zone voor een toepassing in het particuliere netwerk.

1. Open Azure Portal. Zoek in het bovenste zoekvak naar **privé-DNS zones** en selecteer **privé-DNS zones** uit het resultaat.

2. Selecteer op de pagina **privé-DNS zones** **+ toevoegen**.

3. Vul het formulier in op de pagina **privé-DNS zone maken** . Voer **<span>Private.azuremicroservices.io</span>** in als de **naam** van de zone.

4. Selecteer **Controleren + maken**.

5. Selecteer **Maken**.

Het maken van de zone kan een paar minuten duren.

## <a name="link-the-virtual-network"></a>Het virtuele netwerk koppelen

Als u de privé-DNS-zone aan het virtuele netwerk wilt koppelen, moet u een koppeling maken met een virtueel netwerk.

1. Selecteer de bron voor de privé-DNS-zone die hierboven is gemaakt: **<span>Private.azuremicroservices.io</span>** 

2. Selecteer in het linkerdeelvenster de optie **Virtuele netwerkkoppelingen**.

3. Selecteer **Toevoegen**.

4. Voer **Azure-lente-Cloud-DNS-link** in voor de naam van de **koppeling**.

5. Voor **virtueel netwerk** selecteert u het virtuele netwerk dat u hebt gemaakt, zoals wordt uitgelegd in [Azure lente-Cloud implementeren in uw virtuele Azure-netwerk (VNet-injectie)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md).

    ![Virtuele netwerkkoppeling toevoegen](media/spring-cloud-access-app-vnet/add-virtual-network-link.png)

6. Klik op **OK**.

## <a name="create-dns-record"></a>DNS-record maken

Als u de privé-DNS-zone wilt gebruiken om DNS te vertalen/omzetten, moet u een type record in de zone maken.

1. Selecteer de virtuele netwerk resource die u hebt gemaakt, zoals wordt uitgelegd in [Azure lente-Cloud implementeren in uw virtuele Azure-netwerk (VNet-injectie)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md).

2. Voer in het zoekvak **verbonden apparaten** *kubernetes-Internal* in.

3. In het gefilterde resultaat zoekt u het **apparaat** dat is verbonden met het service runtime- **subnet** van het service-exemplaar en kopieert u het bijbehorende **IP-adres**. In dit voor beeld is het IP-adres *10.1.0.7*.

    [![DNS-record ](media/spring-cloud-access-app-vnet/create-dns-record.png) maken](media/spring-cloud-access-app-vnet/create-dns-record.png)

Of u kunt het IP-adres ophalen met behulp van de volgende AZ CLI-opdracht:

```azurecli
SPRING_CLOUD_RG= # Resource group name of your Azure Spring Cloud service instance
SPRING_CLOUD= # Name of your Azure Spring Cloud service instance

SERVICE_RUNTIME_RG=`az spring-cloud show -g $SPRING_CLOUD_RG -n $SPRING_CLOUD --query \
"properties.networkProfile.serviceRuntimeNetworkResourceGroup" -o tsv`

IP_ADDRESS=`az network lb frontend-ip list --lb-name kubernetes-internal -g \
$SERVICE_RUNTIME_RG --query "[0].privateIpAddress" -o tsv`
```

4. Selecteer de bron voor de privé-DNS-zone die hierboven is gemaakt: **<span>Private.azuremicroservices.io</span>**.

5. Selecteer **+ Recordset**.

6. In **recordset toevoegen** voert u de volgende gegevens in of selecteert u deze:

    |Instelling     |Waarde                                                                      |
    |------------|---------------------------------------------------------------------------|
    |Naam        |*\** invoeren                                                                 |
    |Type        |Selecteer **een**                                                               |
    |TTL         |*1* invoeren                                                                  |
    |TTL-eenheid    |**Uren** selecteren                                                           |
    |IP-adres  |Geef het IP-adres op dat u in stap 3 hebt gekopieerd. Voer *10.1.0.7* in het voor beeld in.    |

    Selecteer vervolgens **OK**.

    ![Persoonlijke DNS-zone record toevoegen](media/spring-cloud-access-app-vnet/private-dns-zone-add-record.png)

## <a name="assign-private-fqdn-for-your-application"></a>Persoonlijke FQDN voor uw toepassing toewijzen

Na het volgen van de procedure in micro [service-toepassingen bouwen en implementeren](spring-cloud-tutorial-deploy-in-azure-virtual-network.md), kunt u een persoonlijke FQDN voor uw toepassing toewijzen.

1. Selecteer het Azure lente-Cloud service-exemplaar dat is geïmplementeerd in uw virtuele netwerk en open het tabblad **apps** in het menu aan de linkerkant.

2. Selecteer de toepassing om de **overzichts** pagina weer te geven.

3. Selecteer **eind punt toewijzen** om een persoonlijke FQDN toe te wijzen aan uw toepassing. Dit kan enkele minuten duren.

    ![Persoonlijk eind punt toewijzen](media/spring-cloud-access-app-vnet/assign-private-endpoint.png)

4. De toegewezen persoonlijke FQDN-naam ( **URL**) is nu beschikbaar. Het kan alleen worden geopend in het particuliere netwerk, maar niet op internet.

## <a name="access-application-private-fqdn"></a>Persoonlijke FQDN van Access-toepassing

Na de toewijzing krijgt u toegang tot de persoonlijke FQDN van uw toepassing in een particulier netwerk. U kunt bijvoorbeeld een JumpBox-machine maken in hetzelfde virtuele netwerk of een gekoppeld virtueel netwerk, en op die JumpBox-machine is de persoonlijke FQDN toegankelijk.

![Toegang tot persoonlijk eind punt in vnet](media/spring-cloud-access-app-vnet/access-private-endpoint.png)

## <a name="next-steps"></a>Volgende stappen

- [Toepassingen beschikbaar stellen voor Internet-met behulp van Application Gateway en Azure Firewall](spring-cloud-expose-apps-gateway-azure-firewall.md)

## <a name="see-also"></a>Zie ook

- [Problemen met Azure Spring Cloud in VNET oplossen](spring-cloud-troubleshooting-vnet.md)
- [Verantwoordelijkheden van de klant voor het uitvoeren van Azure Spring Cloud in VNET](spring-cloud-vnet-customer-responsibilities.md)