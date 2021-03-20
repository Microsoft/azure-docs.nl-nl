---
title: Een Azure-opslag account dat wordt gebruikt door een lab in Azure DevTest Labs versleutelen
description: Meer informatie over het configureren van versleuteling van een Azure-opslag die wordt gebruikt door een lab in Azure DevTest Labs
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: dcede89fb23c532742e41121688bcb51a5a73833
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92149309"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>Azure-opslag die wordt gebruikt door een lab in Azure DevTest Labs versleutelen
Elk lab dat is gemaakt in Azure DevTest Labs wordt gemaakt met een gekoppeld Azure Storage-account. Het opslag account wordt gebruikt voor de volgende doel einden: 

- [Formule](devtest-lab-manage-formulas.md) documenten opslaan die kunnen worden gebruikt voor het maken van virtuele machines.
- Artefact resultaten opslaan met implementatie-en extensie logboeken die zijn gegenereerd op basis van het Toep assen van artefacten. 
- [Virtuele harde schijven (Vhd's) uploaden om aangepaste installatie kopieën in het lab te maken.](devtest-lab-create-template.md)
- Het opslaan van veelgebruikte [artefacten](add-artifact-vm.md) en [Azure Resource Manager sjablonen](devtest-lab-create-environment-from-arm.md) voor het sneller ophalen van het maken van virtuele machines/omgevingen.

> [!NOTE]
> De bovenstaande informatie is essentieel voor de test omgeving. Het is opgeslagen voor de levens duur van de Lab-(en Lab-resources), tenzij expliciet verwijderd. Hand matig verwijderen van deze resources kan leiden tot fouten bij het maken van Lab-Vm's en/of-formules beschadigd raken voor toekomstig gebruik. 

## <a name="locate-the-storage-account-and-view-its-contents"></a>Het opslag account zoeken en de inhoud ervan weer geven

1. Op de start pagina van het lab selecteert u de **resource groep** op de pagina **overzicht** . U ziet de pagina **resource groep** voor de resource groep die het Lab bevat. 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="Resource groep selecteren op de pagina overzicht":::
1. Selecteer het Azure-opslag account van het lab. De naam Conventie voor het lab-opslag account is: `a<labNameWithoutInvalidCharacters><4-digit number>` . Als de naam van het lab bijvoorbeeld is `contosolab` , kan de naam van het opslag account zijn `acontosolab7576` . 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="Selecteer een opslag account in de resource groep van het lab":::
3. Selecteer op de pagina **opslag account** de optie **Storage Explorer (preview)** in het linkermenu en selecteer vervolgens **BLOB-containers** om relevante inhoud te zoeken die betrekking heeft op het lab. 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="Opslagverkenner (preview)" lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>Het opslag account Lab versleutelen
Met Azure Storage worden uw gegevens automatisch versleuteld wanneer deze in de cloud worden bewaard. Azure Storage versleuteling beschermt uw gegevens en helpt u om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. Zie [Azure Storage versleuteling voor Data-at-rest](../storage/common/storage-service-encryption.md)voor meer informatie.

Gegevens in het lab-opslag account zijn versleuteld met een door **micro soft beheerde sleutel**. U kunt gebruikmaken van door micro soft beheerde sleutels voor het versleutelen van uw gegevens, maar u kunt ook versleuteling beheren met uw eigen sleutels. Als u ervoor kiest om versleuteling te beheren met uw eigen sleutels voor het opslag account van de test omgeving, kunt u een door de **klant beheerde sleutel** opgeven met Azure Key Vault voor het versleutelen/ontsleutelen van gegevens in Blob Storage en in azure files. Zie door de klant [beheerde sleutels gebruiken met Azure Key Vault voor het beheren van Azure Storage versleuteling](../storage/common/customer-managed-keys-overview.md)voor meer informatie over door de klant beheerde sleutels.

Raadpleeg de volgende artikelen voor meer informatie over het configureren van door de klant beheerde sleutels voor Azure Storage versleuteling: 

- [Azure-portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure-CLI](../storage/common/customer-managed-keys-configure-key-vault.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>De levens cyclus van Azure Blob-opslag beheren
Zoals vermeld, is de informatie die is opgeslagen in het opslag account van het lab essentieel voor het uitvoeren van de test omgeving zonder fouten. Tenzij dit expliciet wordt verwijderd, blijven deze gegevens in het opslag account van de test omgeving voor de levens duur van het lab of de levens duur van specifieke virtuele lab-machines behouden, afhankelijk van het type gegevens.

### <a name="uploaded-vhds"></a>Geüploade Vhd's
Deze Vhd's worden gebruikt voor het maken van aangepaste installatie kopieën. Als u deze verwijdert, kunt u geen aangepaste installatie kopieën meer maken van deze Vhd's.

### <a name="artifacts-cache"></a>Cache artefacten
Deze caches worden opnieuw gemaakt, waarna er tijd artefacten worden toegepast. Ze worden vernieuwd met de meest recente inhoud van de respectieve opslag plaatsen waarnaar wordt verwezen. Als u deze informatie verwijdert om kosten voor opslag te besparen, is de uitverlichting tijdelijk.

### <a name="azure-resource-manager-template-cache"></a>Cache van Azure Resource Manager-sjabloon
Deze caches worden opnieuw gemaakt op het moment dat Azure Resource Manager sjabloon opslagplaatsen zijn verbonden en aanwezig in het lab. Ze worden vernieuwd met de meest recente inhoud van de respectieve opslag plaatsen waarnaar wordt verwezen. Als u deze informatie verwijdert om kosten voor opslag te besparen, is de uitverlichting tijdelijk.

### <a name="formulas"></a>Formules
Deze documenten worden gebruikt voor het ondersteunen van de optie om formules te maken op basis van bestaande virtuele machines en om Vm's te maken op basis van formules. Het verwijderen van deze formule documenten kan leiden tot fouten tijdens het uitvoeren van de volgende bewerkingen:

- Een formule maken op basis van een bestaande Lab-VM
- Formules maken of bijwerken 
- Een virtuele machine maken op basis van een formule.

### <a name="artifact-results"></a>Artefact resultaten
Naarmate artefacten worden toegepast, kan de grootte van de respectieve artefact resultaten na verloop van tijd toenemen, afhankelijk van het aantal en het type artefacten dat op Lab-Vm's wordt uitgevoerd. Als eigenaar van een Lab wilt u dus de levens cyclus van dergelijke documenten bepalen. Zie [de levens cyclus van Azure Blob-opslag beheren](../storage/blobs/storage-lifecycle-management-concepts.md)voor meer informatie.

> [!IMPORTANT]
> U wordt aangeraden deze stap uit te voeren om de kosten te verminderen die zijn gekoppeld aan het Azure Storage-account. 

De volgende regel wordt bijvoorbeeld gebruikt voor het instellen van een verval regel van 90 dagen specifiek voor artefact resultaten. Het zorgt ervoor dat oudere artefact resultaten worden gerecycled vanuit het opslag account op een gewone uitgebracht.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "artifacts/results" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 90 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over het configureren van door de klant beheerde sleutels voor Azure Storage versleuteling: 

- [Azure-portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure-CLI](../storage/common/customer-managed-keys-configure-key-vault.md)