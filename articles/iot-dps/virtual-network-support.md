---
title: Ondersteuning voor Azure IoT Device Provisioning Service (DPS) voor virtuele netwerken
description: Het gebruik van een verbindings patroon voor virtuele netwerken met Azure IoT Device Provisioning Service (DPS)
services: iot-hub
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: wesmc
ms.openlocfilehash: f1409a931195d236b2729e629e4603c606137593
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959778"
---
# <a name="azure-iot-hub-device-provisioning-service-dps-support-for-virtual-networks"></a>Azure IoT Hub Device Provisioning Service (DPS)-ondersteuning voor virtuele netwerken

In dit artikel wordt het connectiviteits patroon van het virtuele netwerk (VNET) geïntroduceerd voor IoT-apparaten die gebruikmaken van IoT-hubs met DPS. Dit patroon biedt een persoonlijke connectiviteit tussen de apparaten, DPS en de IoT-hub binnen een Azure-VNET dat eigendom is van de klant. 

In de meeste scenario's waarbij DPS is geconfigureerd met een VNET, wordt uw IoT Hub ook geconfigureerd in hetzelfde VNET. Zie [IOT hub ondersteuning voor het virtuele netwerk](../iot-hub/virtual-network-support.md)voor meer informatie over VNET-ondersteuning en configuratie voor IOT-hubs.



## <a name="introduction"></a>Inleiding

DPS-hostnamen worden standaard toegewezen aan een openbaar eind punt met een openbaar routeerbaar IP-adres via internet. Dit open bare eind punt is zichtbaar voor alle klanten. Toegang tot het open bare eind punt kan worden getracht door IoT-apparaten via Wide Area Networks en on-premises netwerken.

Om verschillende redenen kunnen klanten de connectiviteit met Azure-resources, zoals DPS, beperken. Dit zijn de volgende redenen:

* Voorkom de verbindings blootstelling via het open bare Internet. De bloot stelling kan worden beperkt door extra beveiligings lagen te introduceren via isolatie op netwerk niveau voor uw IoT-hub en DPS-resources

* Het inschakelen van een persoonlijke connectiviteits ervaring van uw on-premises netwerk assets zorgt ervoor dat uw gegevens en verkeer rechtstreeks naar het Azure-backbone netwerk worden verzonden.

* Exfiltration-aanvallen van gevoelige on-premises netwerken voor komen. 

* Hieronder zijn de Azure-brede connectiviteits patronen ingesteld met behulp van [privé-eind punten](../private-link/private-endpoint-overview.md).

Veelvoorkomende benaderingen voor het beperken van de connectiviteit zijn [DPS IP-filter regels](./iot-dps-ip-filtering.md) en virtuele netwerken (VNET) met [privé-eind punten](../private-link/private-endpoint-overview.md). Het doel van dit artikel is de VNET-benadering voor DPS te beschrijven met behulp van privé-eind punten. 

Apparaten die in on-premises netwerken werken [, kunnen VPN (virtueel particulier netwerk)](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privé-peering gebruiken om verbinding te maken met een VNET in Azure en toegang te krijgen tot DPS-bronnen via persoonlijke eind punten. 

Een persoonlijk eind punt is een privé-IP-adres dat is toegewezen in een VNET van de klant waarmee een Azure-resource toegankelijk is. Als u een persoonlijk eind punt voor uw DPS-resource hebt, kunt u apparaten in uw VNET toestaan om de inrichting van uw DPS-resource aan te vragen zonder verkeer naar het open bare eind punt toe te staan.


## <a name="prerequisites"></a>Vereisten

Voordat u doorgaat, controleert u of aan de volgende vereisten wordt voldaan:

* Uw DPS-resource is al gemaakt en gekoppeld aan uw IoT-hubs. Zie [IOT hub Device Provisioning Service instellen met de Azure Portal](./quick-setup-auto-provision.md) voor hulp bij het instellen van een nieuwe DPS-resource.

* U hebt een Azure VNET ingericht met een subnet waarin het persoonlijke eind punt wordt gemaakt. Zie [een virtueel netwerk maken met behulp van Azure cli](../virtual-network/quick-create-cli.md)voor meer informatie.

* Voor apparaten die in een on-premises netwerk werken, stelt u [VPN (virtueel particulier netwerk)](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privé-peering in op uw Azure VNET.

## <a name="private-endpoint-limitations"></a>Beperkingen van privé-eind punten

Houd rekening met de volgende actuele beperkingen voor DPS wanneer u persoonlijke eind punten gebruikt:

* Privé-eind punten werken niet met DPS wanneer de DPS-resource en de gekoppelde hub zich in verschillende Clouds bevinden. Bijvoorbeeld [Azure Government en wereld wijd Azure](../azure-government/documentation-government-welcome.md).

* [Aangepaste toewijzings beleidsregels met Azure functions](how-to-use-custom-allocation-policies.md) voor DPS werken momenteel niet wanneer de Azure-functie wordt vergrendeld op een VNET-en privé-eind punten. 

* De huidige ondersteuning voor DPS VNET is alleen voor het inkomen van gegevens in DPS. Het uitvallen van gegevens, het verkeer van DPS naar IoT Hub, gebruikt een intern service-to-service-mechanisme in plaats van een toegewezen VNET. Ondersteuning voor volledige op VNET gebaseerde uitstaande vergren deling tussen DPS en IoT Hub is momenteel niet beschikbaar.

* Het laagste latentie toewijzings beleid wordt gebruikt om een apparaat toe te wijzen aan de IoT-hub met de laagste latentie. Dit toewijzings beleid is niet betrouwbaar in een virtuele netwerk omgeving. 

## <a name="set-up-a-private-endpoint"></a>Een persoonlijk eind punt instellen

Voer de volgende stappen uit om een persoonlijk eind punt in te stellen:

1. Open in de [Azure Portal](https://portal.azure.com/)uw DPS-resource en klik op het tabblad **netwerk** . Klik op **persoonlijke eindpunt verbindingen** en **+ persoonlijk eind punt**.

    ![Een nieuw persoonlijk eind punt voor DPS toevoegen](./media/virtual-network-support/networking-tab-add-private-endpoint.png)

2. Voer op de pagina basis beginselen voor het _maken van een persoonlijk eind punt_ de informatie in de onderstaande tabel in.

    ![Basis beginselen voor persoonlijke eind punten maken](./media/virtual-network-support/create-private-endpoint-basics.png)

    | Veld | Waarde |
    | :---- | :-----|
    | **Abonnement** | Kies het gewenste Azure-abonnement om het persoonlijke eind punt te bevatten.  |
    | **Resourcegroep** | Kies of maak een resource groep die het persoonlijke eind punt bevat |
    | **Naam**       | Voer een naam in voor uw persoonlijke eind punt |
    | **Regio**     | De gekozen regio moet hetzelfde zijn als de regio die het VNET bevat, maar hoeft niet hetzelfde te zijn als de DPS-resource. |

    Klik op **volgende: resource** om de resource te configureren waaraan het persoonlijke eind punt moet wijzen.

3. Voer op de pagina _een persoonlijke eindpunt resource maken_ de gegevens in die in de onderstaande tabel worden vermeld.

    ![Een persoonlijke eindpunt resource maken](./media/virtual-network-support/create-private-endpoint-resource.png)

    | Veld | Waarde |
    | :---- | :-----|
    | **Abonnement**        | Kies het Azure-abonnement dat de DPS-resource bevat waaraan uw persoonlijke eind punt moet verwijzen.  |
    | **Resourcetype**       | Kies **micro soft. devices/ProvisioningServices**. |
    | **Resource**            | Selecteer de DPS-resource waaraan het persoonlijke eind punt moet worden toegewezen. |
    | **Stel subresource in** | Selecteer **iotDps**. |

    > [!TIP]
    > Informatie over de instelling **verbinding maken met een Azure-resource per resource-id of alias** vindt u in de sectie [een persoonlijk eind punt aanvragen](#request-a-private-endpoint) in dit artikel.


    Klik op **volgende: Configuratie** om het VNET voor het persoonlijke eind punt te configureren.

4. Kies op de pagina _een persoonlijke eindpunt configuratie maken_ het virtuele netwerk en subnet om het persoonlijke eind punt in te maken.
 
    Klik op **volgende: Tags** en geef eventueel labels voor uw resource op.

    ![Persoonlijk eind punt configureren](./media/virtual-network-support/create-private-endpoint-configuration.png)

6. Klik op **beoordeling + maken** en vervolgens op **maken** om uw persoonlijke eindpunt resource te maken.


## <a name="request-a-private-endpoint"></a>Een persoonlijk eind punt aanvragen

U kunt een persoonlijk eind punt aanvragen bij een DPS-resource per resource-ID. Als u deze aanvraag wilt maken, moet u de resource-eigenaar om u te voorzien van de resource-ID. 

1. De resource-ID bevindt zich op het tabblad Eigenschappen voor de DPS-resource, zoals hieronder wordt weer gegeven.

    ![Het tabblad DPS-eigenschappen](./media/virtual-network-support/dps-properties.png)

    > [!CAUTION]
    > Houd er rekening mee dat de resource-ID de abonnements-ID bevat. 

2. Nadat u de resource-ID hebt, volgt u de bovenstaande stappen in [een persoonlijk eind punt instellen](#set-up-a-private-endpoint) op de pagina _een persoonlijke eindpunt resource maken_ . Klik op **verbinding maken met een Azure-resource op resource-id of alias** en voer de informatie in de volgende tabel in. 

    | Veld | Waarde |
    | :---- | :-----|
    | **Resource-ID of-alias** | Voer de resource-ID voor de DPS-resource in. |
    | **Stel subresource in** | **IotDps** invoeren |
    | **Aanvraag bericht** | Voer een aanvraag bericht in voor de eigenaar van de DPS-resource.<br>Bijvoorbeeld: <br>`Please approve this new private endpoint`<br>`for IoT devices in site 23 to access this DPS instance`  |

    Klik op **volgende: Configuratie** om het VNET voor het persoonlijke eind punt te configureren.

3. Kies op de pagina _een persoonlijke eindpunt configuratie maken_ het virtuele netwerk en subnet om het persoonlijke eind punt in te maken.
 
    Klik op **volgende: Tags** en geef eventueel labels voor uw resource op.

4. Klik op **beoordeling + maken** en vervolgens op **maken** om uw persoonlijke eindpunt aanvraag te maken.

5. De eigenaar van de DPS ziet de aanvraag voor het privé-eind punt in de lijst met verbindingen van het **privé-eind punt** op het tabblad DPS Networking. Op die pagina kan de eigenaar de aanvraag voor een privé-eind punt **goed keuren** of **afwijzen** , zoals hieronder wordt weer gegeven.

    ![DPS-goed keuring](./media/virtual-network-support/approve-dps-private-endpoint.png)


## <a name="pricing-private-endpoints"></a>Privé-eind punten voor prijzen

Zie [prijzen van Azure Private Link](https://azure.microsoft.com/pricing/details/private-link) voor meer informatie over prijzen.



## <a name="next-steps"></a>Volgende stappen

Gebruik de onderstaande koppelingen voor meer informatie over DPS-beveiligings functies:

* [Beveiliging](./concepts-service.md#attestation-mechanism)
* [TLS 1,2-ondersteuning](tls-support.md)