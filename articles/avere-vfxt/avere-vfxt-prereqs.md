---
title: AVERE vFXT-vereisten-Azure
description: Meer informatie over taken die u moet uitvoeren voordat u een cluster maakt in avere vFXT voor Azure, met inbegrip van abonnementen, quota's en opslag service-eind punten.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: d87f57873a4bb84b20df3da3880017d9ef2484a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008409"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Voorbereiden op het maken van de Avere vFXT

In dit artikel worden de vereiste taken voor het maken van een avere vFXT-cluster uitgelegd.

## <a name="create-a-new-subscription"></a>Een nieuw abonnement maken

Begin met het maken van een nieuw Azure-abonnement. Gebruik een afzonderlijk abonnement voor elk avere vFXT-project om het bijhouden en opschonen van de kosten te vereenvoudigen, en om ervoor te zorgen dat andere projecten niet worden beïnvloed door quota of resource beperking bij het inrichten van de cluster werk stroom.

Een nieuw Azure-abonnement maken in de Azure Portal:

1. Navigeer naar de [Blade abonnementen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
1. Klik bovenaan op de knop **+ toevoegen**
1. Meld u aan als u hierom wordt gevraagd
1. Selecteer een aanbieding en door loop de stappen voor het maken van een nieuw abonnement

## <a name="configure-subscription-owner-permissions"></a>Machtigingen voor abonnements eigenaren configureren

Een gebruiker met eigenaars machtigingen voor het abonnement moet het vFXT-cluster maken. Voor het maken van een cluster moet een eigenaar de software voorwaarden accepteren en wijzigingen aan netwerk-en opslag bronnen autoriseren.

Er zijn enkele tijdelijke oplossingen waarmee een niet-eigenaar een avere vFXT voor Azure-cluster kan maken. Deze scenario's omvatten het beperken van resources en het toewijzen van extra Azure-rollen aan de maker. In al deze gevallen moet een eigenaar van het abonnement ook [de avere vFXT-software voorwaarden](#accept-software-terms) van tevoren accepteren.

| Scenario | Beperkingen | Er zijn toegangs rollen vereist om het avere vFXT-cluster te maken |
|----------|--------|-------|
| De beheerder van de resource groep maakt de vFXT | Het virtuele netwerk, de cluster controller en de cluster knooppunten moeten worden gemaakt in de resource groep. | [Gebruikers toegang beheerder](../role-based-access-control/built-in-roles.md#user-access-administrator) en [Inzender](../role-based-access-control/built-in-roles.md#contributor) rollen, zowel binnen het bereik van de doel resource groep. |
| Een bestaand, extern virtueel netwerk gebruiken | De cluster controller en cluster knooppunten worden gemaakt in de resource groep van de vFXT, maar er wordt een bestaand virtueel netwerk gebruikt in een andere resource groep. | (1) de beheerders-en [Inzender](../role-based-access-control/built-in-roles.md#contributor) rollen van de [gebruikers toegang](../role-based-access-control/built-in-roles.md#user-access-administrator) tot de resource groep vFXT. en (2) de Inzender van de [virtuele machine](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), de [gebruikers toegangs beheerder](../role-based-access-control/built-in-roles.md#user-access-administrator)en de [avere-Inzender](../role-based-access-control/built-in-roles.md#avere-contributor) rollen binnen het bereik van de resource groep van het virtuele netwerk. |
| Aangepaste rol voor cluster makers | Geen beperkingen voor de plaatsing van resources. Deze methode geeft niet-eigen aren belang rijke bevoegdheden. | De eigenaar van het abonnement maakt een aangepaste Azure-rol zoals beschreven in [dit artikel](avere-vfxt-non-owner.md). |

## <a name="quota-for-the-vfxt-cluster"></a>Quota voor het vFXT-cluster

Controleer of u voldoende quota hebt voor de volgende Azure-onderdelen. Indien nodig kunt u [een quotum verhoging aanvragen](../azure-portal/supportability/resource-manager-core-quotas-request.md).

> [!NOTE]
> De virtuele machines en SSD-onderdelen die hier worden vermeld, gelden voor het vFXT-cluster zelf. Houd er rekening mee dat u ook quota nodig hebt voor de Vm's en Ssd's die u voor uw Compute-Farm gaat gebruiken.
>
> Zorg ervoor dat het quotum is ingeschakeld voor de regio waar u de werk stroom wilt uitvoeren.

|Azure-onderdeel|Quota|
|----------|-----------|
|Virtuele machines|3 of meer E32s_v3 (één per cluster knooppunt) |
|Premium SSD-opslag|200 GB ruimte in het besturingssysteem plus 1 tot 4 TB ruimte in de cache per knooppunt |
|Opslagaccount (optioneel) |v2|
|Back-endopslag van gegevens (optioneel) |Een nieuwe LRS-BLOB-container |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>Software voorwaarden accepteren

> [!TIP]
> Sla deze stap over als de eigenaar van een abonnement het avere vFXT-cluster gaat maken.

Tijdens het maken van het cluster moet u de service voorwaarden accepteren voor de avere vFXT-software. Als u geen eigenaar van het abonnement bent, moet de eigenaar van het abonnement de voor waarden van tevoren accepteren.

Deze stap hoeft slechts één keer per abonnement te worden uitgevoerd.

De software voorwaarden vooraf accepteren:

1. Open een Cloud shell in de Azure Portal of door te bladeren naar <https://shell.azure.com> . Meld u aan met uw abonnements-ID.

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. Geef de volgende opdracht om de service voorwaarden te accepteren en programmatische toegang in te scha kelen voor de avere vFXT voor Azure-software-installatie kopie:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Een opslag service-eind punt in uw virtuele netwerk maken (indien nodig)

Een [service-eind punt](../virtual-network/virtual-network-service-endpoints-overview.md) houdt Azure Blob-verkeer lokaal in plaats van dit buiten het virtuele netwerk te routeren. Het wordt aanbevolen voor alle avere vFXT voor Azure-clusters die gebruikmaken van Azure Blob voor back-end-gegevens opslag.

Als u tijdens het maken van het cluster een nieuw virtueel netwerk maakt, wordt automatisch een eind punt gemaakt. Als u een bestaand virtueel netwerk opgeeft, moet het een service-eind punt van micro soft Storage hebben als u een nieuwe Blob Storage-container wilt maken tijdens het maken van het cluster.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* Sla deze stap over als u een nieuw virtueel netwerk maakt als onderdeel van het maken van het cluster.
>* Een eind punt is optioneel als u geen Blob-opslag maakt tijdens het maken van het cluster. In dat geval kunt u het service-eind punt later maken als u ervoor kiest om Azure Blob te gebruiken.

Maak het eind punt van de opslag service vanuit het Azure Portal.

1. Open de lijst met virtuele netwerken in de portal.
1. Selecteer het virtuele netwerk voor uw cluster.
1. Klik in het menu links op **service-eind punten** .
1. Klik bovenaan op **toevoegen** .
1. Kies de service ``Microsoft.Storage`` .
1. Selecteer het subnet van het cluster.
1. Klik onderaan op **toevoegen**.

   ![Azure Portal scherm opname met annotaties voor de stappen voor het maken van het service-eind punt](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u deze vereisten hebt voltooid, kunt u het cluster maken. Lees [het vFXT-cluster implementeren](avere-vfxt-deploy.md) voor instructies.