---
title: Azure IoT Hub voor virtuele netwerken
description: Verbindingspatroon voor virtuele netwerken gebruiken met IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jlian
ms.openlocfilehash: df38f9b3482847ea0415af5cb47540e244b0510b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739886"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>IoT Hub ondersteuning voor virtuele netwerken met Private Link en beheerde identiteit

Standaard worden IoT Hub hostnamen van de IoT Hub via internet aan een openbaar eindpunt met een openbaar routeerbaar IP-adres. Verschillende klanten delen dit IoT Hub openbaar eindpunt en IoT-apparaten in via wide-area netwerken en on-premises netwerken hebben allemaal toegang tot dit eindpunt.

![IoT Hub openbaar eindpunt maken](./media/virtual-network-support/public-endpoint.png)

IoT Hub functies zoals [](./iot-hub-devguide-messages-d2c.md)berichtroutering, bestandsupload [en](./iot-hub-devguide-file-upload.md) [bulksgewijs importeren/exporteren](./iot-hub-bulk-identity-mgmt.md) van apparaten vereisen ook connectiviteit van IoT Hub naar een Azure-resource die eigendom is van de klant via het openbare eindpunt. Deze verbindingspaden maken gezamenlijk het verkeer van IoT Hub naar klantresources.

Mogelijk wilt u de connectiviteit met uw Azure-resources (inclusief IoT Hub) beperken via een VNet waarvan u eigenaar bent en dat u gebruikt. Dit zijn de volgende redenen:

* Introductie van netwerkisolatie voor uw IoT-hub door te voorkomen dat connectiviteit wordt blootgesteld aan het openbare internet.

* Het inschakelen van een privé-connectiviteitservaring vanuit uw on-premises netwerkactiva, zodat uw gegevens en verkeer rechtstreeks naar het backbone-netwerk van Azure worden verzonden.

* Voorkomen van exfiltratieaanvallen van gevoelige on-premises netwerken. 

* Volg de vastgestelde Azure-connectiviteitspatronen met [behulp van privé-eindpunten.](../private-link/private-endpoint-overview.md)

In dit artikel wordt beschreven hoe u deze doelstellingen bereikt met behulp van [Azure Private Link](../private-link/private-link-overview.md) voor ingressconnectiviteit met IoT Hub en het gebruik van een vertrouwde Microsoft-services-uitzondering voor de connectiviteit van IoT Hub naar andere Azure-resources.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Connectiviteit voor in- en IoT Hub met behulp van Azure Private Link

Een privé-eindpunt is een privé-IP-adres dat is toegewezen binnen een VNet van de klant via welke een Azure-resource bereikbaar is. Via Azure Private Link kunt u een privé-eindpunt instellen voor uw IoT-hub zodat services in uw VNet IoT Hub kunnen bereiken zonder dat er verkeer naar het openbare eindpunt van IoT Hub moet worden verzonden. Op dezelfde manier kunnen uw on-premises apparaten vpn-peering [(Virtual Private Network)](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [ExpressRoute](https://azure.microsoft.com/services/expressroute/) gebruiken om verbinding te maken met uw VNet en uw IoT Hub (via het privé-eindpunt). Als gevolg hiervan kunt u de connectiviteit met de openbare eindpunten van uw IoT-hub beperken of volledig blokkeren met behulp van [IoT Hub IP-filter of](./iot-hub-ip-filtering.md) de schakelknop voor openbare [netwerktoegang.](iot-hub-public-network-access.md) Met deze aanpak blijft de verbinding met uw hub met behulp van het privé-eindpunt voor apparaten. De belangrijkste focus van deze installatie is voor apparaten in een on-premises netwerk. Deze installatie wordt niet aanbevolen voor apparaten die zijn geïmplementeerd in een wide-area network.

![IoT Hub virtueel netwerk](./media/virtual-network-support/virtual-network-ingress.png)

Voordat u doorgaat, moet u ervoor zorgen dat aan de volgende vereisten wordt voldaan:

* U hebt een [Azure-VNet gemaakt](../virtual-network/quick-create-portal.md) met een subnet waarin het privé-eindpunt wordt gemaakt.

* Voor apparaten die in on-premises netwerken werken, stelt u [VPN (Virtual Private Network)](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [persoonlijke ExpressRoute-peering](https://azure.microsoft.com/services/expressroute/) in uw Azure VNet in.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Een privé-eindpunt instellen voor IoT Hub ingress

Privé-eindpunt werkt voor IoT Hub-API's (zoals apparaat-naar-cloud-berichten) en service-API's (zoals het maken en bijwerken van apparaten).

1. Selecteer Azure Portal **netwerken,** **privé-eindpuntverbindingen** en klik op **het + Privé-eindpunt.**

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="Schermopname die laat zien waar u een privé-eindpunt voor IoT Hub":::

1. Geef het abonnement, de resourcegroep, de naam en de regio op waarin u het nieuwe privé-eindpunt wilt maken. In het ideale moment moet het privé-eindpunt worden gemaakt in dezelfde regio als uw hub.

1. Klik op **Volgende: Resource** en geef het abonnement voor uw IoT Hub-resource op en selecteer **Microsoft.Devices/IotHubs** als resourcetype, uw IoT Hub-naam als **resource** en **iotHub** als doelsubresource.

1. Klik **op Volgende: Configuratie** en geef uw virtuele netwerk en subnet op om het privé-eindpunt in te maken. Selecteer de optie voor integratie met de privé-DNS-zone van Azure, indien gewenst.

1. Klik **op Volgende: Tags** en geef eventueel tags op voor uw resource.

1. Klik **op Beoordelen en maken om** uw private link-resource te maken.

### <a name="built-in-event-hub-compatible-endpoint"></a>Ingebouwd event hub-compatibel eindpunt 

Het [ingebouwde eindpunt dat compatibel](iot-hub-devguide-messages-read-builtin.md) is met Event Hub is ook toegankelijk via een privé-eindpunt. Wanneer private link is geconfigureerd, ziet u een extra privé-eindpuntverbinding voor het ingebouwde eindpunt. Het is de met `servicebus.windows.net` in de FQDN.

:::image type="content" source="media/virtual-network-support/private-built-in-endpoint.png" alt-text="Afbeelding van twee privé-eindpunten die elk IoT Hub privékoppeling":::

IoT Hub [IP-filter van](iot-hub-ip-filtering.md) de IoT Hub kunt desgewenst openbare toegang tot het ingebouwde eindpunt bepalen. 

Als u openbare netwerktoegang tot uw IoT-hub volledig wilt [blokkeren,](iot-hub-public-network-access.md) moet u openbare netwerktoegang uitschakelen of ip-filter gebruiken om alle IP-adressen te blokkeren en de optie selecteren om regels toe te passen op het ingebouwde eindpunt.

### <a name="pricing-for-private-link"></a>Prijzen voor Private Link

Zie [prijzen van Azure Private Link](https://azure.microsoft.com/pricing/details/private-link) voor meer informatie over prijzen.

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Connectiviteit van IoT Hub naar andere Azure-resources

IoT Hub kunt verbinding maken met uw Azure Blob Storage, [](./iot-hub-devguide-messages-d2c.md)Event Hub, Service Bus-resources voor berichtroutering, bestand uploaden [en](./iot-hub-devguide-file-upload.md)bulksgewijs apparaat [importeren/exporteren](./iot-hub-bulk-identity-mgmt.md) via het openbare eindpunt van de resources. Het binden van uw resource aan een VNet blokkeert standaard de verbinding met de resource. Als gevolg hiervan voorkomt deze configuratie dat IoT Hub gegevens naar uw resources kan verzenden. U kunt dit probleem oplossen door connectiviteit vanuit uw IoT Hub-resource met uw opslagaccount, Event Hub of Service Bus-resources in te stellen via de vertrouwde **Microsoft-serviceoptie.**

### <a name="turn-on-managed-identity-for-iot-hub"></a>Beheerde identiteit in IoT Hub

Als u wilt dat andere services uw IoT-hub kunnen vinden als een vertrouwde Microsoft-service, moet deze een door het systeem toegewezen beheerde identiteit hebben.

1. **Navigeer naar Identiteit** in IoT Hub portal

1. Selecteer **onder Status** de optie **Aan** en klik vervolgens op **Opslaan.**

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="Schermopname die laat zien hoe u een beheerde identiteit in kunt IoT Hub":::

Azure CLI gebruiken om beheerde identiteiten in te zetten:

```azurecli-interactive
az iot hub update --name <iot-hub-resource-name> --set identity.type="SystemAssigned"
```

### <a name="assign-managed-identity-to-your-iot-hub-at-creation-time-using-arm-template"></a>Beheerde identiteit toewijzen aan uw IoT Hub maken met behulp van een ARM-sjabloon

Als u een beheerde identiteit wilt toewijzen aan uw IoT-hub tijdens het inrichten van resources, gebruikt u de onderstaande ARM-sjabloon. Deze ARM-sjabloon heeft twee vereiste resources en beide moeten worden geïmplementeerd voordat u andere resources maakt, zoals `Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups` . 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "createIotHub",
      "dependsOn": [
        "[resourceId('Microsoft.Devices/IotHubs', '<provide-a-valid-resource-name>')]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

Nadat u de waarden voor uw resource , en hebt vervangen, kunt u Azure CLI gebruiken om de resource in een bestaande `name` `location` `SKU.name` `SKU.tier` resourcegroep te implementeren met behulp van:

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Nadat de resource is gemaakt, kunt u de beheerde service-identiteit ophalen die is toegewezen aan uw hub met behulp van Azure CLI:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

### <a name="pricing-for-managed-identity"></a>Prijzen voor beheerde identiteit

Vertrouwde uitzonderingsfunctie voor services van Microsoft is gratis. Kosten voor de inrichtende opslagaccounts, Event Hubs of Service Bus-resources worden afzonderlijk toegepast.

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Egress-connectiviteit met eindpunten van opslagaccounts voor routering

IoT Hub kunt berichten door sturen naar een opslagaccount van de klant. Om de routeringsfunctionaliteit toegang te geven tot een opslagaccount terwijl er firewallbeperkingen zijn, moet uw hub een beheerde identiteit gebruiken voor toegang tot het opslagaccount. Uw hub heeft eerst een [beheerde identiteit nodig.](#turn-on-managed-identity-for-iot-hub) Zodra een beheerde identiteit is ingericht, volgt u de onderstaande stappen om Azure RBAC toestemming te geven voor de resource-identiteit van uw hub voor toegang tot uw opslagaccount.

1. Navigeer Azure Portal het tabblad Toegangsbeheer **(IAM)** van uw opslagaccount en klik op **Toevoegen** in de sectie **Een roltoewijzing** toevoegen.

2. Selecteer  Inzender voor opslagblobgegevens [ (niet](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)Inzender of Bijdrager voor opslagaccount) als **rol,** **Azure AD-gebruiker,** -groep of -service-principal als **Toegang** toewijzen aan en selecteer de resourcenaam van uw IoT Hub in de vervolgkeuzelijst. Klik op de knop **Opslaan**.

3. Navigeer naar **het tabblad Firewalls en virtuele netwerken** in uw opslagaccount en schakel de optie Toegang vanuit geselecteerde netwerken **toestaan** in. Vink onder **de** lijst Uitzonderingen het selectievakje Vertrouwde gebruikers **Microsoft-services toegang tot dit opslagaccount toestaan aan.** Klik op de knop **Opslaan**.

4. Navigeer IoT Hub de resourcepagina van uw bedrijf naar **het tabblad Berichtroutering.**

5. **Navigeer naar de sectie Aangepaste eindpunten** en klik op **Toevoegen.** Selecteer **Opslag** als het eindpunttype.

6. Geef op de pagina die wordt weergegeven een naam op voor uw eindpunt, selecteer de container die u wilt gebruiken in uw blobopslag, geef codering en bestandsnaamindeling op. Selecteer **Op basis van identiteit** als **verificatietype** voor uw opslag-eindpunt. Klik op de knop **Maken**.

Uw aangepaste opslag-eindpunt is nu ingesteld voor het gebruik van de door het systeem toegewezen identiteit van uw hub en het heeft toestemming om toegang te krijgen tot uw opslagresource, ondanks de firewallbeperkingen. U kunt dit eindpunt nu gebruiken om een regel voor doorsturen in te stellen.

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Connectiviteit van verkeer naar Event Hubs-eindpunten voor routering

IoT Hub kunnen worden geconfigureerd om berichten te sturen naar een event hubs-naamruimte die eigendom is van de klant. Om de routeringsfunctionaliteit toegang te geven tot een Event Hubs-resource terwijl er firewallbeperkingen zijn, moet uw IoT Hub een beheerde identiteit gebruiken om toegang te krijgen tot de Event Hubs-resource. Uw hub heeft eerst een beheerde identiteit nodig. Nadat een beheerde identiteit is gemaakt, volgt u de onderstaande stappen om Azure RBAC toestemming te geven voor de resource-identiteit van uw hub voor toegang tot uw Event Hubs.

1. Navigeer Azure Portal het tabblad Toegangsbeheer **(IAM)** van event hubs en klik op **Toevoegen** onder de sectie **Een roltoewijzing** toevoegen.

2. Selecteer **Event Hubs Gegevensverzender** als **rol,** Azure AD-gebruiker, -groep of **-service-principal** als **Toegang** toewijzen aan en selecteer de resourcenaam van uw IoT Hub in de vervolgkeuzelijst. Klik op de knop **Opslaan**.

3. Navigeer naar **het tabblad Firewalls en virtuele** netwerken in uw Event Hubs en schakel de optie Toegang vanuit geselecteerde netwerken **toestaan** in. Onder de **lijst Uitzonderingen** het selectievakje Vertrouwde gebruikers toegang Microsoft-services **Event Hubs toestaan.** Klik op de knop **Opslaan**.

4. Navigeer IoT Hub de resourcepagina van uw bedrijf naar **het tabblad Berichtroutering.**

5. **Navigeer naar de sectie Aangepaste eindpunten** en klik op **Toevoegen.** Selecteer **Event Hubs** als het eindpunttype.

6. Geef op de pagina die wordt weergegeven een naam op voor uw eindpunt en selecteer uw Event Hubs-naamruimte en -instantie. Selecteer **Identiteit als verificatietype** en klik op de **knop** Maken. 

Uw aangepaste Event Hubs-eindpunt is nu ingesteld om de door het systeem toegewezen identiteit van uw hub te gebruiken en heeft ondanks de firewallbeperkingen toegang tot uw Event Hubs-resource. U kunt dit eindpunt nu gebruiken om een regel voor doorsturen in te stellen.

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Egress-connectiviteit met Service Bus-eindpunten voor routering

IoT Hub kunnen worden geconfigureerd om berichten te sturen naar een Service Bus-naamruimte die eigendom is van de klant. Om de routeringsfunctionaliteit toegang te geven tot een Service Bus-resource terwijl er firewallbeperkingen zijn, moet uw IoT Hub een beheerde identiteit gebruiken voor toegang tot de Service Bus-resource. Uw hub heeft eerst een beheerde identiteit nodig. Zodra een beheerde identiteit is ingericht, volgt u de onderstaande stappen om Azure RBAC toestemming te geven voor de resource-identiteit van uw hub voor toegang tot uw Service Bus.

1. Navigeer Azure Portal het tabblad Toegangsbeheer **(IAM)** van uw Service Bus en klik op **Toevoegen** in de sectie **Een roltoewijzing** toevoegen.

2. Selecteer **Service Bus Data Sender** als **rol,** **Azure AD-gebruiker,** groep of service-principal als **Toegang** toewijzen aan en selecteer de resourcenaam van uw IoT Hub in de vervolgkeuzelijst. Klik op de knop **Opslaan**.

3. Navigeer naar **het tabblad Firewalls en virtuele netwerken** in uw servicebus en schakel de optie Toegang vanuit geselecteerde netwerken **toestaan** in. Vink onder **de** lijst Uitzonderingen het selectievakje Vertrouwde gebruikers **Microsoft-services toegang tot deze servicebus.** Klik op de knop **Opslaan**.

4. Navigeer IoT Hub de resourcepagina van uw bedrijf naar **het tabblad Berichtroutering.**

5. **Navigeer naar de sectie Aangepaste eindpunten** en klik op **Toevoegen.** Selecteer **Service Bus-wachtrij** **of Service Bus onderwerp** (indien van toepassing) als het eindpunttype.

6. Geef op de pagina die wordt weergegeven een naam op voor uw eindpunt en selecteer de naamruimte en wachtrij of het onderwerp van uw Service Bus (indien van toepassing). Selecteer **Identiteit als** verificatietype en klik op de **knop** Maken. 

Uw aangepaste Service Bus-eindpunt is nu ingesteld voor het gebruik van de door het systeem toegewezen identiteit van uw hub en het heeft toestemming om toegang te krijgen tot uw Service Bus-resource, ondanks de firewallbeperkingen. U kunt dit eindpunt nu gebruiken om een regel voor doorsturen in te stellen.

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Connectiviteit van Egress met opslagaccounts voor het uploaden van bestanden

IoT Hub functie voor het uploaden van bestanden kunnen apparaten bestanden uploaden naar een opslagaccount van de klant. Als u wilt dat het uploaden van bestanden werkt, moeten zowel apparaten als IoT Hub verbinding hebben met het opslagaccount. Als er firewallbeperkingen gelden voor het opslagaccount, moeten uw apparaten gebruikmaken van een [](../private-link/tutorial-private-endpoint-storage-portal.md)van de ondersteunde opslagaccounts (inclusief privé-eindpunten, [service-eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md)of directe [firewallconfiguratie)](../storage/common/storage-network-security.md)om verbinding te maken. En als er firewallbeperkingen gelden voor het opslagaccount, moet IoT Hub worden geconfigureerd voor toegang tot de opslagresource via de vertrouwde Microsoft-services uitzondering. Hiervoor moet uw IoT Hub een beheerde identiteit hebben. Zodra een beheerde identiteit is ingericht, volgt u de onderstaande stappen om Azure RBAC toegang te geven tot de resource-identiteit van uw hub voor toegang tot uw opslagaccount.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

1. Navigeer Azure Portal het tabblad Toegangsbeheer **(IAM)** van uw opslagaccount en klik op Toevoegen **onder** de sectie **Een roltoewijzing** toevoegen.

2. Selecteer **Inzender** voor opslagblobgegevens [ (niet](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)Inzender of Bijdrager voor opslagaccount) als rol **,** Azure AD-gebruiker, -groep of **-service-principal** als **Toegang** toewijzen aan en selecteer de resourcenaam van uw IoT Hub in de vervolgkeuzelijst. Klik op de knop **Opslaan**.

3. Navigeer naar **het tabblad Firewalls en virtuele netwerken** in uw opslagaccount en schakel de optie Toegang vanuit geselecteerde netwerken **toestaan** in. Vink onder **de** lijst Uitzonderingen het selectievakje Vertrouwde gebruikers **Microsoft-services toegang tot dit opslagaccount toe.** Klik op de knop **Opslaan**.

4. Navigeer IoT Hub naar het tabblad Bestand uploaden op de resourcepagina **van uw** IoT Hub.

5. Selecteer op de pagina die wordt weergegeven de container die u wilt gebruiken in uw blobopslag, configureer de instellingen voor bestandsmeldingen, **SAS TTL,** **Standaard-TTL** en **Maximum** aantal leveringen naar wens. Selecteer **Op basis van identiteit** als **verificatietype** voor uw opslag-eindpunt. Klik op de knop **Maken**. Als er tijdens deze stap een foutmelding wordt weergegeven, stelt u uw opslagaccount tijdelijk in om toegang vanuit **Alle netwerken** toe te staan. Probeer het vervolgens opnieuw. U kunt de firewall in het opslagaccount configureren zodra de configuratie voor het uploaden van bestanden is voltooid.

Uw opslag-eindpunt voor het uploaden van bestanden is nu ingesteld om de door het systeem toegewezen identiteit van uw hub te gebruiken. Het eindpunt heeft ondanks de firewallbeperkingen toegang tot uw opslagresource.

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Egress-connectiviteit met opslagaccounts voor bulksgewijs importeren/exporteren van apparaten

IoT Hub ondersteunt de functionaliteit voor het [bulksgewijs importeren/exporteren](./iot-hub-bulk-identity-mgmt.md) van gegevens van/naar een door de klant geleverde opslagblob. Als u bulksgewijs importeren/exporteren wilt toestaan, moeten zowel apparaten als IoT Hub verbinding hebben met het opslagaccount.

Deze functionaliteit vereist connectiviteit van IoT Hub naar het opslagaccount. Als u toegang wilt krijgen tot een Service Bus-resource terwijl er firewallbeperkingen gelden, moet uw IoT Hub een beheerde identiteit hebben. Zodra een beheerde identiteit is ingericht, volgt u de onderstaande stappen om Azure RBAC toestemming te geven voor de resource-identiteit van uw hub voor toegang tot uw Service Bus.

1. Navigeer Azure Portal het tabblad Toegangsbeheer **(IAM)** van uw opslagaccount en klik op **Toevoegen** in de sectie **Een roltoewijzing** toevoegen.

2. Selecteer **Inzender** voor opslagblobgegevens [ (niet](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)Inzender of Bijdrager voor opslagaccount) als rol **,** Azure AD-gebruiker, -groep of **-service-principal** als **Toegang** toewijzen aan en selecteer de resourcenaam van uw IoT Hub in de vervolgkeuzelijst. Klik op de knop **Opslaan**.

3. Navigeer naar **het tabblad Firewalls en virtuele netwerken** in uw opslagaccount en schakel de optie Toegang vanuit geselecteerde netwerken **toestaan** in. Vink onder **de** lijst Uitzonderingen het selectievakje Vertrouwde gebruikers **Microsoft-services toegang tot dit opslagaccount toestaan aan.** Klik op de knop **Opslaan**.

U kunt nu de Azure IoT [](/rest/api/iothub/service/jobs/getimportexportjobs) REST API's gebruiken voor het maken van importexporttaken voor informatie over het gebruik van de functionaliteit voor bulkimport/export. U moet de in de aanvraag body en gebruiken en als respectievelijk de invoer- en `storageAuthenticationType="identityBased"` `inputBlobContainerUri="https://..."` `outputBlobContainerUri="https://..."` uitvoer-URL's van uw opslagaccount.

Azure IoT Hub SDK's ondersteunen deze functionaliteit ook in registerbeheer van de serviceclient. In het volgende codefragment ziet u hoe u een import- of export job initieert met behulp van de C# SDK.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```

Als u deze versie van de Azure IoT-SDK's wilt gebruiken met ondersteuning voor virtuele netwerken voor C#, Java en Node.js:

1. Maak een omgevingsvariabele `EnableStorageIdentity` met de naam en stel de waarde ervan in op `1` .

2. Download de SDK: [Java](https://aka.ms/vnetjavasdk)  |  [C#](https://aka.ms/vnetcsharpsdk)  |  [Node.js](https://aka.ms/vnetnodesdk)
 
Download voor Python onze beperkte versie van GitHub.

1. Navigeer naar [de GitHub-releasepagina](https://aka.ms/vnetpythonsdk).

2. Download het volgende bestand, dat u onderaan de releasepagina vindt onder de header met de **naam assets**.
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. Open een terminal en navigeer naar de map met het gedownloade bestand.

4. Voer de volgende opdracht uit om de Python Service SDK met ondersteuning voor virtuele netwerken te installeren:
    > pip install ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>Volgende stappen

Gebruik de onderstaande koppelingen voor meer informatie over IoT Hub functies:

* [Berichtroutering](./iot-hub-devguide-messages-d2c.md)
* [Bestand uploaden](./iot-hub-devguide-file-upload.md)
* [Bulksgewijs apparaat importeren/exporteren](./iot-hub-bulk-identity-mgmt.md)
