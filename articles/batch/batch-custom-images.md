---
title: Een beheerde installatie kopie gebruiken om een aangepaste installatie kopie groep te maken
description: Maak een aangepaste batch-installatie kopie groep van een beheerde installatie kopie om reken knooppunten in te richten met de software en gegevens voor uw toepassing.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 0a357a1d8a22341297f3bee73fb0867fb03f374f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94916573"
---
# <a name="use-a-managed-image-to-create-a-custom-image-pool"></a>Een beheerde installatie kopie gebruiken om een aangepaste installatie kopie groep te maken

Als u een aangepaste installatie kopie groep wilt maken voor de virtuele machines (Vm's) van uw batch-pool, kunt u een beheerde installatie kopie gebruiken om een afbeelding van de [Galerie met gedeelde afbeeldingen](batch-sig-images.md)te maken. Het gebruik van alleen een beheerde installatie kopie wordt ook ondersteund, maar alleen voor API-versies tot en met 2019-08-01.

> [!IMPORTANT]
> In de meeste gevallen moet u aangepaste installatie kopieën maken met behulp van de galerie met gedeelde afbeeldingen. Met de galerie gedeelde afbeeldingen kunt u Pools sneller inrichten, grotere aantallen Vm's schalen en de betrouw baarheid verbeteren bij het inrichten van Vm's. Zie [de galerie met gedeelde afbeeldingen gebruiken om een aangepaste groep te maken](batch-sig-images.md)voor meer informatie.

In dit onderwerp wordt uitgelegd hoe u een aangepaste installatie kopie groep maakt met behulp van alleen een beheerde installatie kopie.

## <a name="prerequisites"></a>Vereisten

- **Een beheerde installatie kopie bron**. Als u een groep virtuele machines wilt maken met behulp van een aangepaste installatie kopie, moet u een beheerde installatie kopie bron hebben of maken in hetzelfde Azure-abonnement en dezelfde regio als het batch-account. De installatie kopie moet worden gemaakt op basis van moment opnamen van de besturingssysteem schijf van de VM en optioneel op de gekoppelde gegevens schijven.
  - Gebruik een unieke aangepaste installatie kopie voor elke groep die u maakt.
  - Als u een groep wilt maken met de installatie kopie met behulp van de batch-Api's, geeft u de **resource-id** op van de afbeelding. Dit is van het formulier `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage` .
  - De beheerde installatie kopie bron moet bestaan voor de levens duur van de groep zodat deze kan worden geschaald en kan worden verwijderd nadat de groep is verwijderd.

- **Azure Active Directory-verificatie (Azure AD)**. De batch-client-API moet gebruikmaken van Azure AD-verificatie. Azure Batch-ondersteuning voor Azure AD wordt beschreven in [Oplossingen van Batch-service verifiëren met Active Directory](batch-aad-auth.md).

## <a name="prepare-a-managed-image"></a>Een beheerde installatie kopie voorbereiden

In azure kunt u een beheerde installatie kopie voorbereiden vanaf:

- Moment opnamen van het besturings systeem en de gegevens schijven van een virtuele machine van Azure
- Een gegeneraliseerde Azure-VM met beheerde schijven
- Een gegeneraliseerde on-premises VHD die is geüpload naar de Cloud

Als u batch-Pools betrouwbaar wilt schalen met een beheerde installatie kopie, raden we u aan om de beheerde installatie kopie te maken met *alleen* de eerste methode: met behulp van moment opnamen van de schijven van de virtuele machine. De volgende stappen laten zien hoe u een virtuele machine voorbereidt, een moment opname maakt en een beheerde installatie kopie vanuit de moment opname kunt maken.

### <a name="prepare-a-vm"></a>Een virtuele machine voorbereiden

Als u een nieuwe VM voor de installatie kopie maakt, gebruikt u een Azure Marketplace-installatie kopie van de eerste partij die door batch wordt ondersteund als basis installatie kopie voor uw beheerde installatie kopie. Alleen installatie kopieën van de eerste partij kunnen worden gebruikt als basis installatie kopie. Voor een volledige lijst met voor beelden van Azure Marketplace-installatie kopieën die door Azure Batch worden ondersteund, raadpleegt u de [List node agent sku's](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) -bewerking.

> [!NOTE]
> U kunt geen afbeelding van derden gebruiken die aanvullende licentie-en aankoop voorwaarden als uw basis installatie kopie heeft. Zie de richt lijnen voor [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) -of [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) -vm's voor meer informatie over deze installatie kopieën voor Marketplace.

- Zorg ervoor dat de virtuele machine is gemaakt met een beheerde schijf. Dit is de standaard instelling voor opslag wanneer u een VM maakt.
- Installeer geen Azure-extensies, zoals de aangepaste script extensie, op de VM. Als de installatie kopie een vooraf geïnstalleerde extensie bevat, kan Azure problemen ondervinden bij het implementeren van de batch-pool.
- Wanneer u gekoppelde gegevens schijven gebruikt, moet u de schijven koppelen en Format teren vanuit een VM om ze te gebruiken.
- Zorg ervoor dat de basis installatie kopie van het besturings systeem dat u opgeeft, gebruikmaakt van het standaard tijdelijke station. De batch-knooppunt agent verwacht momenteel het standaard tijdelijke station.
- Zorg ervoor dat de besturingssysteem schijf niet is versleuteld.
- Zodra de virtuele machine wordt uitgevoerd, maakt u verbinding met de VM via RDP (voor Windows) of SSH (voor Linux). Installeer de benodigde software of kopieer de gewenste gegevens.  

### <a name="create-a-vm-snapshot"></a>Een VM-moment opname maken

Een moment opname is een volledige, alleen-lezen kopie van een VHD. Als u een moment opname van het besturings systeem of de gegevens schijven van een virtuele machine wilt maken, kunt u de Azure Portal of opdracht regel Programma's gebruiken. Zie de richt lijnen voor [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) -of [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) -vm's voor een overzicht van de stappen en opties voor het maken van een moment opname.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Een installatie kopie maken van een of meer moment opnamen

Als u een beheerde installatie kopie wilt maken op basis van een moment opname, gebruikt u de opdracht regel Programma's van Azure, zoals de opdracht [AZ image Create](/cli/azure/image) . U kunt een installatie kopie maken door een moment opname van de besturingssysteem schijf en eventueel een of meer moment opnamen van de gegevens schijf op te geven.

## <a name="create-a-pool-from-a-managed-image"></a>Een groep maken op basis van een beheerde installatie kopie

Wanneer u de resource-ID van de beheerde installatie kopie hebt gevonden, maakt u een aangepaste installatie kopie groep van die installatie kopie. De volgende stappen laten zien hoe u een aangepaste installatie kopie groep maakt met behulp van batch-service of batch-beheer.

> [!NOTE]
> Zorg ervoor dat de identiteit die u voor Azure AD-verificatie gebruikt, machtigingen heeft voor de afbeeldings bron. Zie [batch-service oplossingen verifiëren met Active Directory](batch-aad-auth.md).
>
> De resource voor de beheerde installatie kopie moet bestaan voor de levens duur van de groep. Als de onderliggende resource wordt verwijderd, kan de groep niet worden geschaald.

### <a name="batch-service-net-sdk"></a>.NET SDK voor batch-service

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image definition name}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
```

### <a name="batch-management-rest-api"></a>REST API voor Batch-beheer

REST API-URI

```http
 PUT https://management.azure.com/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Batch/batchAccounts/{account name}/pools/{pool name}?api-version=2020-03-01
```

Aanvraagtekst

```json
 {
   "properties": {
     "vmSize": "{VM size}",
     "deploymentConfiguration": {
       "virtualMachineConfiguration": {
         "imageReference": {
           "id": "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image name}"
         },
         "nodeAgentSkuId": "{Node Agent SKU ID}"
       }
     }
   }
 }
```

## <a name="considerations-for-large-pools"></a>Overwegingen voor grote Pools

Als u van plan bent een pool met honderden Vm's of meer te maken met behulp van een aangepaste installatie kopie, is het belang rijk dat u de voor gaande richt lijnen volgt voor het gebruik van een installatie kopie die is gemaakt van een VM-moment opname.

Houd ook rekening met de volgende overwegingen:

- **Grootte limieten** : batch beperkt de pool grootte tot 2500 toegewezen reken knooppunten of de knoop punten met een lage prioriteit van 1000 wanneer u een aangepaste installatie kopie gebruikt.

  Als u dezelfde installatie kopie (of meerdere installatie kopieën op basis van dezelfde onderliggende moment opname) gebruikt om meerdere groepen te maken, kunnen de totale reken knooppunten in de groepen niet groter zijn dan de voor gaande limieten. Het is niet raadzaam een afbeelding of de onderliggende moment opname te gebruiken voor meer dan één groep.

  Limieten kunnen worden verminderd als u de groep configureert met [binnenkomende NAT-Pools](pool-endpoint-configuration.md).

- **Grootte van de time-out wijzigen** : als uw pool een vast aantal knoop punten bevat (niet automatisch schalen), verhoogt u de eigenschap resizeTimeout van de groep naar een waarde van 20-30 minuten. Als uw pool niet binnen de time-outperiode de doel grootte bereikt, moet u een andere grootte voor het [formaat van de bewerking](/rest/api/batchservice/pool/resize)uitvoeren.

  Als u een pool plant met meer dan 300 reken knooppunten, moet u mogelijk meerdere malen het formaat van de groep wijzigen om de doel grootte te bereiken.
  
Met de [Galerie gedeelde afbeeldingen](batch-sig-images.md)kunt u met uw aangepaste installatie kopieën grote groepen maken, samen met meer gedeelde afbeeldings replica's. Als u gedeelde installatie kopieën gebruikt, is de tijd die nodig is om de groep te bereiken, tot 25% sneller en is de latentie van de virtuele machine niet langer dan 30%.

## <a name="considerations-for-using-packer"></a>Overwegingen voor het gebruik van Packer

Het maken van een beheerde installatie kopie bron rechtstreeks met de Packer kan alleen worden uitgevoerd met de modus voor de gebruikers abonnement-batch accounts. Voor accounts van de batch-service modus moet u eerst een VHD maken en vervolgens de VHD importeren in een beheerde installatie kopie bron. Afhankelijk van de pool toewijzings modus (gebruikers abonnement of batch-service), kunnen de stappen voor het maken van een beheerde afbeeldings resource verschillen.

Zorg ervoor dat de resource die wordt gebruikt om de beheerde installatie kopie te maken, bestaat voor de levens duur van elke groep die verwijst naar de aangepaste installatie kopie. Als u dit niet doet, kan dit leiden tot geheugen toewijzings fouten en/of het formaat van fouten.

Als de afbeelding of de onderliggende resource wordt verwijderd, wordt er mogelijk een fout bericht weer gegeven die vergelijkbaar is met: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed` . Als u deze fout ontvangt, controleert u of de onderliggende resource niet is verwijderd.

Zie [een Linux-installatie kopie bouwen met een Packer](../virtual-machines/linux/build-image-with-packer.md) of [een Windows-installatie kopie bouwen met Packer](../virtual-machines/windows/build-image-with-packer.md)voor meer informatie over het gebruik van Packer voor het maken van een virtuele machine.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van de [Galerie met gedeelde afbeeldingen](batch-sig-images.md) voor het maken van een aangepaste groep.
- Zie [batch service workflow en resources](batch-service-workflow-features.md)voor een uitgebreid overzicht van batch.
