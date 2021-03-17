---
title: VSphere-referenties voor Azure VMware-oplossing opnieuw instellen
description: Meer informatie over het opnieuw instellen van vSphere-referenties voor uw Azure VMware-oplossing voor de privécloud en zorg ervoor dat de HCX-connector de meest recente vSphere-referenties heeft.
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: e5a15caa98a46b0ae75b68ee7b568dabdbf1956c
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103603077"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>VSphere-referenties voor Azure VMware-oplossing opnieuw instellen

In dit artikel gaan we de stappen door lopen om de vSphere-referenties voor de privécloud van Azure VMware-oplossing opnieuw in te stellen. Zo kunt u ervoor zorgen dat de HCX-connector de meest recente vSphere-referenties heeft.

## <a name="reset-your-vsphere-credentials"></a>Uw vSphere-referenties opnieuw instellen

 Eerst stellen we uw vSphere-referenties opnieuw in. Uw vCenter-CloudAdmin en NSX-T-beheerders referenties verlopen niet. u kunt echter deze stappen uitvoeren om nieuwe wacht woorden voor deze accounts te genereren.

> [!NOTE]
> Als u uw CloudAdmin-referenties gebruikt voor verbonden services zoals HCX, vCenter orchestrator, vCloud Director of vRealize, werken uw verbindingen niet meer wanneer u uw wacht woord bijwerkt.  Deze services moeten worden gestopt voordat de wacht woord rotatie kan worden gestart.  Als u dit niet doet, kan dit leiden tot tijdelijke vergren delingen op uw vCenter CloudAdmin-en NSX-T-beheerders accounts, omdat deze services voortdurend worden aangeroepen met uw oude referenties.  Zie [toegangs-en identiteits concepten](https://docs.microsoft.com/azure/azure-vmware/concepts-identity)voor meer informatie over het instellen van afzonderlijke accounts voor verbonden services.

1. Open een opdracht regel in uw Azure VMware-oplossingen Portal.

2. Voer de volgende opdracht uit om uw vCenter CloudAdmin-wacht woord bij te werken.  U moet {SubscriptionID}, {ResourceGroup} en {PrivateCloudName} vervangen door de werkelijke waarden van de privécloud waarvan het CloudAdmin-account deel uitmaakt.

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. Voer de volgende opdracht uit om uw NSX-T-beheerders wachtwoord bij te werken. U moet {SubscriptionID}, {ResourceGroup} en {PrivateCloudName} vervangen door de werkelijke waarden van de privécloud waarvan het NSX-T-beheerders account deel uitmaakt.

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vsphere-credentials"></a>Zorg ervoor dat de HCX-connector uw meest recente vSphere-referenties heeft

Nu u uw referenties opnieuw hebt ingesteld, voert u de volgende stappen uit om ervoor te zorgen dat de HCX-connector uw bijgewerkte referenties heeft.

1. Als uw wacht woord is gewijzigd, gaat u naar de on-premises HCX connector-webinterface met behulp van https://{IP van het HCX-connector apparaat}: 443. Zorg ervoor dat u poort 443 gebruikt. Meld u aan met uw nieuwe referenties.

2. Selecteer op het VMware HCX-dash board **site koppeling**.
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="Scherm opname van het VMware HCX-dash board met site koppeling gemarkeerd.":::
 
3. Selecteer de juiste verbinding met AVS (als er meer dan één) en selecteer **verbinding bewerken**.
 
4. Geef de nieuwe vSphere-referenties op en selecteer **bewerken**, waarmee de referenties worden opgeslagen. Het opslaan moet worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt gefeliciteerd met het opnieuw instellen van vSphere-referenties voor de Azure VMware-oplossing, kunt u het volgende weten:

- [NSX-netwerk onderdelen configureren in azure VMware-oplossing](configure-nsx-network-components-azure-portal.md).
- [Levenscyclus beheer van virtuele machines met Azure VMware-oplossingen](lifecycle-management-of-azure-vmware-solution-vms.md).
- [Herstel na nood gevallen van virtuele machines met behulp van de Azure VMware-oplossing](disaster-recovery-for-virtual-machines.md).
