---
title: Gebruik de Azure Portal voor het oplossen van problemen met Azure Stack Edge Pro-volg orde | Microsoft Docs
description: Hierin wordt beschreven hoe u problemen met het rangschikken van Azure Stack Edge Pro oplost.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: e074043cb685f60027d3c09ae7ad8dc17dded0df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443400"
---
# <a name="troubleshoot-your-azure-stack-edge-pro-ordering-issues"></a>Problemen met uw Azure Stack Edge Pro-volg orde oplossen

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u problemen met het rangschikken van Azure Stack Edge Pro oplost.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Problemen met bestellingen oplossen

## <a name="unsupported-subscription-or-region"></a>Niet-ondersteund abonnement of regio

**Fout beschrijving:** Als u in Azure Portal de volgende fout melding krijgt:

*De geselecteerde abonnementen of regio's worden niet ondersteund. Kies een ander abonnement of een andere regio.*

![Niet-ondersteund abonnement of regio](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**Voorgestelde oplossing:**  Zorg ervoor dat u een ondersteund abonnement hebt gebruikt, zoals [micro soft Enterprise overeenkomst (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp)of [Microsoft Azure sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Betalen per gebruik-abonnementen worden niet ondersteund. Zie [Azure stack Edge-resource vereisten](azure-stack-edge-deploy-prep.md#prerequisites)voor meer informatie.

Er is de mogelijkheid dat micro soft per geval een upgrade voor een abonnements type kan toestaan. Neem contact op met [micro soft ondersteuning](https://azure.microsoft.com/support/options/) om uw behoeften te begrijpen en deze limieten op de juiste manier aan te passen.

## <a name="selected-subscription-type-not-supported"></a>Het geselecteerde abonnements type wordt niet ondersteund

**Fout:** U hebt een EA-, CSP-of gesponsord abonnement en u krijgt de volgende fout:

*Het geselecteerde abonnements type wordt niet ondersteund. Zorg ervoor dat u een ondersteund abonnement gebruikt. [Meer informatie](azure-stack-edge-deploy-prep.md#prerequisites). Als u een ondersteund abonnements type gebruikt, moet u ervoor zorgen dat de `Microsoft.DataBoxEdge` provider is geregistreerd. Zie [resource provider registreren](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)voor meer informatie over het registreren*.

**Voorgestelde oplossing:** Volg deze stappen om uw Azure Stack Edge-resource provider te registreren:

1. Ga in azure Portal naar **Home**-  >  **abonnementen**.

2. Selecteer het abonnement dat u gebruikt om uw apparaat te best Ellen.

3. Selecteer **resource providers** en zoek naar **micro soft. DataBoxEdge**.

    ![Resourceprovider registreren](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

Als u geen eigenaar of Inzender toegang hebt om de resource provider te registreren, ziet u de volgende fout: *de abonnements &lt; naam van het abonnement &gt; beschikt niet over de juiste machtigingen voor het registreren van de resource provider (s): micro soft. DataBoxEdge.*

Zie [resource providers registreren](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)voor meer informatie.

## <a name="microsoftdataboxedge-not-registered-for-subscription"></a>Micro soft. DataBoxEdge niet geregistreerd voor abonnement

**Fout:** In Azure Portal selecteert u een abonnement dat u wilt gebruiken voor Azure Stack Edge Pro of Data Box Gateway en krijgt u de volgende fout:

*Resource provider (s): micro soft. DataBoxEdge zijn niet geregistreerd voor abonnements &lt; naam &gt; en u bent niet gemachtigd om een resource provider te registreren voor abonnements &lt; naam &gt; abonnement*.

**Voorgestelde oplossing:** De toegang tot uw abonnement verhogen of iemand met een eigenaar of bijdrager vinden om de resource provider te registreren.

## <a name="resource-disallowed-by-policy"></a>De resource is niet toegestaan door het beleid

**Fout:** In Azure Portal probeert u een resource provider te registreren en de volgende fout op te halen:

*De resource &lt; resource naam &gt; is niet toegestaan door het beleid. (Code: RequestDisallowedByPolicy). Initiatief: doorgaans ongewenste resource typen weigeren. Beleid: resource typen zijn niet toegestaan.*

**Voorgestelde oplossing:** Deze fout treedt op als gevolg van een bestaand Azure-beleid dat het maken van resources blokkeert. Azure-beleid wordt ingesteld door de systeem beheerder van een organisatie om te zorgen voor compatibiliteit bij het gebruik of het maken van Azure-resources. Neem contact op met de systeem beheerder om uw Azure-beleid te bewerken als een dergelijk beleid wordt geblokkeerd Azure Stack rand bronnen worden gemaakt.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het oplossen van problemen [met uw Azure stack Edge Pro](azure-stack-edge-troubleshoot.md).