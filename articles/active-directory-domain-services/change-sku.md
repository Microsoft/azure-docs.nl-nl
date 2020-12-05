---
title: De SKU voor een Azure AD Domain Services wijzigen | Microsoft Docs
description: Meer informatie over de SKU-laag voor een Azure AD Domain Services beheerd domein als uw bedrijfs vereisten veranderen
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 320bd87aa78d26cee44c48f27365febd1dd426ff
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620287"
---
# <a name="change-the-sku-for-an-existing-azure-active-directory-domain-services-managed-domain"></a>De SKU voor een bestaand Azure Active Directory Domain Services beheerd domein wijzigen

In Azure Active Directory Domain Services (Azure AD DS) zijn de beschik bare prestaties en functies gebaseerd op het type SKU. Deze functie verschillen omvatten de back-upfrequentie of het maximum aantal eenrichtings vertrouwensrelaties voor uitgaande forests.

U selecteert een SKU wanneer u het beheerde domein maakt en u kunt naar boven of beneden scha kelen tussen Sku's wanneer de bedrijfs behoeften veranderen nadat het beheerde domein is geïmplementeerd. Wijzigingen in zakelijke vereisten kunnen de nood zaak van vaker back-ups bevatten of extra forestvertrouwensrelaties maken. Meer informatie over de limieten en prijzen van de verschillende Sku's vindt u in [azure AD DS SKU-concepten][concepts-sku] en [Azure AD DS prijzen][pricing] pagina's.

In dit artikel wordt beschreven hoe u de SKU voor een bestaand Azure AD DS beheerde domein wijzigt met behulp van de Azure Portal.

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende resources en bevoegdheden nodig om dit artikel te volt ooien:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
    * Als dat nodig is, voltooit u de zelf studie voor het [maken en configureren van een beheerd domein][create-azure-ad-ds-instance].

## <a name="sku-change-limitations"></a>Beperkingen voor het wijzigen van de SKU

U kunt Sku's omhoog of omlaag wijzigen nadat het beheerde domein is geïmplementeerd. Als u echter een resource-forest gebruikt en een eenrichtings uitgaande forest vertrouwensrelatie van Azure AD DS naar een on-premises AD DS omgeving hebt gemaakt, zijn er enkele beperkingen voor de bewerking voor het wijzigen van de SKU. De *Premium* -en *Enter prise* -sku's definiëren een limiet voor het aantal vertrouwens relaties dat u kunt maken. U kunt niet overschakelen naar een SKU met een lagere maximum limiet dan u momenteel hebt geconfigureerd.

Bijvoorbeeld:

* Als u twee forest-vertrouwens relaties hebt gemaakt voor de *Premium* -SKU, kunt u niet verlagen naar de *standaard* -SKU. De *standaard* -SKU ondersteunt geen forest-vertrouwens relaties.
* Of, als u zeven vertrouwens relaties hebt gemaakt voor de *Premium* -SKU, kunt u niet verlagen naar de *Enter prise* -SKU. De *Enter prise* -SKU ondersteunt Maxi maal vijf vertrouwens relaties.

Zie [Azure AD DS SKU-functies en-limieten][concepts-sku]voor meer informatie over deze limieten.

## <a name="select-a-new-sku"></a>Een nieuwe SKU selecteren

Voer de volgende stappen uit om de SKU voor een beheerd domein te wijzigen met behulp van de Azure Portal:

1. Zoek en selecteer **Azure AD Domain Services** aan de bovenkant van de Azure Portal. Kies uw beheerde domein in de lijst, zoals *aaddscontoso.com*.
1. Selecteer in het menu aan de linkerkant van de pagina Azure-AD DS **instellingen > SKU**.

    ![Selecteer de menu optie van de SKU voor uw Azure AD DS beheerde domein in het Azure Portal](media/change-sku/overview-change-sku.png)

1. Selecteer in de vervolg keuzelijst de SKU die u voor uw beheerde domein wilt. Als u een resource-forest hebt, kunt u *standaard* -SKU niet selecteren als forest-vertrouwens relaties zijn alleen beschikbaar in de *Enter prise* -SKU of hoger.

    Kies de gewenste SKU in de vervolg keuzelijst en selecteer vervolgens **Opslaan**.

    ![Kies de vereiste SKU in het vervolg keuzemenu van de Azure Portal](media/change-sku/change-sku-selection.png)

Het kan een paar minuten duren voordat het SKU-type is gewijzigd.

## <a name="next-steps"></a>Volgende stappen

Als u een resource-forest hebt en aanvullende vertrouwens relaties wilt maken nadat de SKU is gewijzigd, raadpleegt u [een uitgaande forestvertrouwensrelatie maken met een on-premises domein in Azure AD DS][create-trust].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
