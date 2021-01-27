---
title: Privé-eindpunten gebruiken met Azure Batch-accounts
description: Meer informatie over het verbinden van privé met een Azure Batch-account met behulp van privé-eind punten.
ms.topic: how-to
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: d2e9d36e9e964f2e9f9a5a986fbf55d19b3069d8
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920000"
---
# <a name="use-private-endpoints-with-azure-batch-accounts"></a>Privé-eindpunten gebruiken met Azure Batch-accounts

[Azure batch accounts](accounts.md) hebben standaard een openbaar eind punt en zijn toegankelijk voor iedereen. De batch-service biedt de mogelijkheid om persoonlijke batch-accounts te maken en de toegang tot het open bare netwerk uit te scha kelen.

Met behulp van een [persoonlijke Azure-koppeling](../private-link/private-link-overview.md)kunt u verbinding maken met een Azure batch-account via een [persoonlijk eind punt](../private-link/private-endpoint-overview.md). Het persoonlijke eind punt is een reeks privé-IP-adressen in een subnet binnen het virtuele netwerk. Vervolgens kunt u de toegang tot een Azure Batch account beperken via privé-IP-adressen.

Met persoonlijke koppeling kunnen gebruikers toegang krijgen tot een Azure Batch-account vanuit het virtuele netwerk of via een peered virtueel netwerk. Resources die zijn toegewezen aan een privé koppeling, zijn ook on-premises toegankelijk via privé-peering via VPN of [Azure ExpressRoute](../expressroute/expressroute-introduction.md). U kunt verbinding maken met een Azure Batch account dat is geconfigureerd met een persoonlijke koppeling met behulp van de [automatische of hand matige goedkeurings methode](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow).

> [!IMPORTANT]
> Ondersteuning voor particuliere connectiviteit in Azure Batch is momenteel beschikbaar voor alle regio's, met uitzonde ring van Duitsland-centraal, Duitsland-noordoost, China-oost, China-oost 2, China-noord en China-noord 2.

In dit artikel worden de stappen beschreven voor het maken van een persoonlijk batch-account en het openen met behulp van een persoonlijk eind punt.

## <a name="azure-portal"></a>Azure Portal

Gebruik de volgende stappen om een persoonlijk batch-account te maken met behulp van de Azure Portal:

1. Kies in het deel venster **een resource maken** de optie **batch-service** en selecteer vervolgens **maken**.
2. Voer de naam van het abonnement, de resource groep, de regio en het batch-account in op het tabblad **basis beginselen** en selecteer vervolgens **volgende: Geavanceerd**.
3. Stel op het tabblad **Geavanceerd** **open bare netwerk toegang** in op **uitgeschakeld**.
4. Selecteer in **instellingen** **persoonlijke eindpunt verbindingen** en selecteer vervolgens **+ persoonlijk eind punt**.
   :::image type="content" source="media/private-connectivity/private-endpoint-connections.png" alt-text="Verbindingen met privé-eind punten":::
5. Typ of Selecteer in het deel venster **basis beginselen** het abonnement, de resource groep, de naam van de persoonlijke eindpunt resource en de regio gegevens en selecteer vervolgens **volgende: resource**.
6. Stel in het deel venster **resource** het **resource type** in op **Microsoft.BatCH/batchAccounts**. Selecteer het persoonlijke batch-account dat u wilt openen en selecteer vervolgens **volgende: Configuratie**.
   :::image type="content" source="media/private-connectivity/create-private-endpoint.png" alt-text="Een persoonlijk eind punt maken-resource venster":::
7. Voer in het deel venster **configuratie** de volgende gegevens in of Selecteer deze:
   - **Virtueel netwerk**: Selecteer uw virtuele netwerk.
   - **Subnet**: Selecteer uw subnet.
   - **Integreren met een privé-DNS-zone**: Selecteer **Ja**. Als u privé wilt verbinden met uw persoonlijke eind punt, moet u een DNS-record hebben. We raden u aan uw persoonlijke eind punt te integreren met een privé-DNS-zone. U kunt ook uw eigen DNS-servers gebruiken of DNS-records maken met behulp van de host-bestanden op uw virtuele machines.
   - **Privé-DNS zone**: Selecteer privatelink. \<region\> . batch.azure.com. De privé-DNS-zone wordt automatisch bepaald. U kunt deze niet wijzigen met behulp van de Azure Portal.
8. Selecteer **controleren + maken** en wacht tot de configuratie is gevalideerd door Azure.
9. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**.

Nadat het persoonlijke eind punt is ingericht, hebt u toegang tot het batch-account van Vm's in hetzelfde virtuele netwerk met behulp van het persoonlijke eind punt.

> [!IMPORTANT]
> Het uitvoeren van bewerkingen buiten het virtuele netwerk waar het persoonlijke eind punt wordt ingericht, resulteert in het bericht ' AuthorizationFailure ' in azure Portal.

Het IP-adres van de Azure Portal weer geven:

1. Selecteer **Alle resources**.
2. Zoek naar het persoonlijke eind punt dat u eerder hebt gemaakt.
3. Selecteer het tabblad **overzicht** om de DNS-instellingen en IP-adressen te bekijken.

:::image type="content" source="media/private-connectivity/access-private.png" alt-text="DNS-instellingen en IP-adressen van persoonlijk eind punt":::

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

Wanneer u [een batch-account maakt met behulp van Azure Resource Manager sjabloon](quick-create-template.md), wijzigt u de sjabloon om **publicNetworkAccess** in te stellen op **uitgeschakeld** , zoals hieronder wordt weer gegeven.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "batchAccountName": {
      "type": "string",
      },
    "location": {
      "type": "string",
    }
  },
  "resources": [
    {
      "name": "[parameters('batchAccountName')]",
      "type": "Microsoft.Batch/batchAccounts",
      "apiVersion": "2020-03-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": []
      "properties": {
        "poolAllocationMode": "BatchService"
        "publicNetworkAccess": "Disabled"
      }
    }
  ]
}
```

## <a name="configure-dns-zones"></a>DNS-zones configureren

Gebruik een [privé-DNS-zone](../dns/private-dns-privatednszone.md) in het subnet waar u het persoonlijke eind punt hebt gemaakt. Configureer de eind punten zodat elk privé-IP-adres wordt toegewezen aan een DNS-vermelding.

Wanneer u het persoonlijke eind punt maakt, kunt u het integreren met een [privé-DNS-zone](../dns/private-dns-privatednszone.md) in Azure. Als u in plaats daarvan een [aangepast domein](../dns/dns-custom-domain.md)wilt gebruiken, moet u dit configureren voor het toevoegen van DNS-records voor alle privé-IP-adressen die zijn gereserveerd voor het persoonlijke eind punt.

## <a name="pricing"></a>Prijzen

Zie [prijzen voor persoonlijke koppelingen voor Azure](https://azure.microsoft.com/pricing/details/private-link/)voor meer informatie over de kosten die betrekking hebben op persoonlijke eind punten.

## <a name="current-limitations-and-best-practices"></a>Huidige beperkingen en aanbevolen procedures

Houd bij het maken van uw persoonlijke batch-account rekening met het volgende:

- Persoonlijke eindpunt resources moeten worden gemaakt in hetzelfde abonnement als het batch-account.
- Als u de persoonlijke verbinding wilt verwijderen, moet u de persoonlijke eindpunt resource verwijderen.
- Zodra een batch-account is gemaakt met open bare netwerk toegang, kunt u dit niet wijzigen in alleen persoonlijke toegang.
- DNS-records in de privé-DNS-zone worden niet automatisch verwijderd wanneer u een persoonlijk eind punt verwijdert of wanneer u een regio uit het batch-account verwijdert. U moet de DNS-records hand matig verwijderen voordat u een nieuw persoonlijk eind punt toevoegt dat is gekoppeld aan deze privé-DNS-zone. Als u de DNS-records niet opschoont, kunnen er onverwachte problemen met gegevens vlak optreden, zoals gegevens uitval tot regio's die zijn toegevoegd na het verwijderen van het privé-eind punt of het verwijderen van de regio.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van batch-Pools in virtuele netwerken](batch-virtual-network.md).
- Meer informatie over het [maken van batch-Pools zonder open bare IP-adressen](batch-pool-no-public-ip-address.md)
- Meer informatie over het [maken van batch-Pools met opgegeven open bare IP-adressen](create-pool-public-ip.md).
- Meer informatie over [persoonlijke Azure-koppelingen](../private-link/private-link-overview.md).
