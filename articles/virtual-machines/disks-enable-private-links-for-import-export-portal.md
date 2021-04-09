---
title: 'Azure Portal: de toegang voor importeren/exporteren naar beheerde schijven beperken met privékoppelingen'
description: Schakel Private Links in voor uw beheerde schijven in Azure Portal. U kunt in uw virtuele netwerk schijven veilig exporteren en importeren.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b80100216003e91fde54b5e555bafb755c942810
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98682916"
---
# <a name="use-the-azure-portal-to-restrict-importexport-access-for-managed-disks-with-private-links"></a>Azure Portal gebruiken om de toegang voor importeren/exporteren voor beheerde schijven te beperken met Private Link

Met Private Links-ondersteuning voor beheerde schijven kunt u het exporteren en importeren van beheerde schijven beperken zodat dit alleen plaatsvindt in uw virtuele Azure-netwerk. U kunt een tijdsgebonden Shared Access Signature-URI (SAS) genereren voor niet-gekoppelde beheerde schijven en momentopnamen voor het exporteren van de gegevens naar een andere regio voor regionale expansie, herstel na noodgevallen en voor het lezen van de gegevens voor forensische analyse. U kunt ook de SAS-URI gebruiken om de VHD rechtstreeks naar een lege schijf van uw on-premises te uploaden. Netwerkverkeer tussen clients in het virtuele netwerk en beheerde schijven gaat alleen over het virtuele netwerk en een persoonlijke koppeling in het fundamentele Microsoft-netwerk, waardoor de blootstelling naar het openbare internet wordt voorkomen.

U kunt een bron voor schijftoegang maken en deze koppelen aan uw virtuele netwerk in hetzelfde abonnement door een privé-eindpunt te maken. U moet een schijf of een momentopname koppelen aan een schijftoegang voor het exporteren en importeren van de gegevens via privékoppelingen. U moet ook de eigenschap NetworkAccessPolicy van de schijf of de momentopname instellen op `AllowPrivate`. 

U kunt de eigenschap NetworkAccessPolicy instellen op `DenyAll` om te voorkomen dat iemand de SAS-URI voor een schijf of momentopname genereert. De standaardwaarde voor de eigenschap NetworkAccessPolicy is `AllowAll`.

## <a name="limitations"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="create-a-disk-access-resource"></a>Een schijftoegang-resource maken

1. Meld u aan bij de Azure-portal en navigeer naar **Schijftoegang** met [deze koppeling](https://aka.ms/disksprivatelinks).

    > [!IMPORTANT]
    > U moet de [gegeven koppeling](https://aka.ms/disksprivatelinks) gebruiken om naar de blade Schijftoegang te navigeren. Het is momenteel niet zichtbaar in de openbare portal zonder gebruik te maken van de koppeling.

1. Selecteer **en Toevoegen** om een nieuwe resource voor schijftoegang te maken.
1. Selecteer op de blade ‘maken’ uw abonnement, een resourcegroep, voer een naam in en selecteer een regio.
1. Selecteer **Controleren + maken**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="Schermopname van de blade voor het maken van de schijftoegang. Vul de gewenste naam in, selecteer een regio, selecteer een resourcegroep en ga door":::

Wanneer uw resource is gemaakt, gaat u hier rechtstreeks naartoe.

:::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="Schermopname van de knop Ga naar resource in de portal":::

## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken

Nu u een resource hebt om toegang te krijgen tot de schijf, kunt u deze gebruiken voor het afhandelen van de toegang tot het exporteren/importeren van uw schijf. Dit gebeurt via privé-eindpunten. U moet daarom een privé-eindpunt maken en configureren voor schijftoegang.

1. Selecteer **verbindingen met een privé-eindpunt** vanuit uw resource voor schijftoegang.
1. Selecteer **+ Privé-eindpunt**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="Schermopname van de blade overzicht voor uw resource voor schijftoegang. Privé-eindpuntverbindingen zijn gemarkeerd.":::

1. Een resourcegroep selecteren
1. Vul de naam in en selecteer de regio waarin uw resource voor schijftoegang is gemaakt.
1. Selecteer **Volgende: Resource >**

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="Schermopname van de werkstroom voor het maken van een privé-eindpunt, de eerste blade. Als u de juiste regio niet selecteert, kunt u later problemen ondervinden in.":::

1. Schakel op de blade **Resource** **Verbinding maken met een Azure-resource in mijn directory** in.
1. Voor **Resourcetype** selecteert u **Microsoft.Compute/diskAccesses**
1. Voor **Resource** selecteert u de schijftoegang die u eerder hebt gemaakt
1. Behoud de **Doel-subresource** als **schijven**
1. Selecteer **Volgende : Configuratie >** .

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="Schermopname van de werkstroom voor het maken van een privé-eindpunt, de tweede blade. Met alle gemarkeerde waarden (resourcetype, resource, subresource van doel)":::

1. Selecteer het virtuele netwerk waartoe u de schijfexport wilt beperken. Andere virtuele netwerken kunnen uw schijf niet exporteren.

    > [!NOTE]
    > Als u een netwerkbeveiligingsgroep (NGS) hebt ingeschakeld voor het geselecteerde subnet, wordt deze alleen uitgeschakeld voor privé-eindpunten op dit subnet. Voor andere resources op dit subnet wordt NSG nog steeds afgedwongen.

1. Selecteer het gewenste subnet
1. Selecteer **Controleren + maken**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="Schermopname van de werkstroom voor het maken van een privé-eindpunt, de derde blade. Virtueel netwerk en subnet benadrukt.":::

## <a name="enable-private-endpoint-on-your-disk"></a>Privé-eindpunt op de schijf inschakelen

1. Ga naar de schijf die u wilt configureren
1. Selecteer **Netwerken**
1. Selecteer **Privé-eindpunt (via schijftoegang)** en selecteer de schijftoegang die u eerder hebt gemaakt.
1. Selecteer **Opslaan**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="Schermopname van de blade netwerken van beheerde schijf. De selectie van het privé-eindpunt en de geselecteerde schijftoegang markeren. Als u dit opslaat, configureert u de schijf voor deze toegang.":::

U hebt nu de configuratie van persoonlijke koppelingen voltooid die u kunt gebruiken bij het importeren/exporteren van uw beheerde schijf.

## <a name="next-steps"></a>Volgende stappen

- [Veelgestelde vragen over privékoppelingen](./faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Beheerde momentopnamen exporteren/kopiëren als VHD naar een opslagaccount in een andere regio met PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account)