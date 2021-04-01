---
title: Tenant-ID, object-ID en koppelings gegevens van de partner vinden in azure Marketplace
description: Het vinden van de Tenant-ID, object-ID en koppelings gegevens van de partner van een abonnements-ID in de Azure Marketplace.
ms.service: marketplace
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/09/2020
ms.openlocfilehash: 2b1ba0779649c4955987c7dae9802cefaba89b79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97109335"
---
# <a name="find-tenant-id-object-id-and-partner-association-details"></a>Tenant-ID, object-ID en koppelings gegevens van de partner zoeken

In dit artikel wordt beschreven hoe u de Tenant-ID, de object-ID en de koppelings gegevens van de partner vindt, samen met de bijbehorende abonnement-Id's.

Als u scherm opnamen van deze items wilt ophalen in Azure Cloud Shell voor hulp bij fout opsporing, gaat u naar de [koppeling Tenant, object en partner-id voor fout opsporing](#find-ids-for-debugging).

>[!Note]
> Alleen de eigenaar van een abonnement heeft de bevoegdheden om deze stappen uit te voeren.

## <a name="find-tenant-id"></a>Tenant-ID zoeken

1. Ga naar de [Azure Portal](https://ms.portal.azure.com/).
2. Selecteer **Azure Active Directory**.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-ad.png" alt-text="Het Azure Active Directory pictogram in Azure Portal.":::

3. Selecteer **Overzicht**. Uw Tenant-ID moet worden weer gegeven onder **basis informatie**.

    :::image type="content" source="media/tenant-and-object-id/select-groups-1.png" alt-text="Selecteer groepen in Azure Portal.":::

## <a name="find-subscriptions-and-roles"></a>Abonnementen en rollen zoeken

1. Ga naar de Azure Portal en selecteer **Azure Active Directory** zoals u in stap 1 en 2 hierboven hebt genoteerd.
2. Selecteer **Abonnementen**.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-subscriptions-1.png" alt-text="Het pictogram abonnementen in Azure Portal.":::

3. Abonnementen en rollen weer geven.

    :::image type="content" source="media/tenant-and-object-id/subscriptions-screen-1.png" alt-text="Het scherm abonnementen in Azure Portal.":::

## <a name="find-partner-id"></a>Partner-ID zoeken

1. Ga naar de pagina Abonnementen zoals beschreven in de vorige sectie.
2. Selecteer een abonnement.
3. Selecteer onder **facturering** **partner gegevens**.

    :::image type="content" source="media/tenant-and-object-id/menu-partner-information.png" alt-text="Partner gegevens in het menu links.":::

## <a name="find-user-object-id"></a>Gebruiker zoeken (object-ID)

1. Meld u aan bij de [Office 365-beheer Portal](https://portal.office.com/adminportal/home) met een account in de gewenste Tenant met de juiste beheerders rechten.
2. Vouw in het menu aan de linkerkant het gedeelte **beheer centrums** onderaan uit en selecteer vervolgens de optie Azure Active Directory om de beheer console te openen in een nieuw browser venster.
3. Selecteer **Gebruikers**.
4. Blader naar of zoek naar de gewenste gebruiker en selecteer vervolgens de account naam om de profiel gegevens van het gebruikers account weer te geven.
5. De object-ID bevindt zich in de sectie identiteit aan de rechter kant.

    :::image type="content" source="media/tenant-and-object-id/azure-ad-admin-center.png" alt-text="Azure Active Directory-beheer centrum.":::

6. Zoek **roltoewijzingen** door **toegangs beheer (IAM)** te selecteren in het menu links en vervolgens **roltoewijzingen**.

    :::image type="content" source="https://docs.microsoft.com/azure/role-based-access-control/media/role-assignments-portal/role-assignments.png" alt-text="Roltoewijzingen voor Azure-resources.":::

## <a name="find-ids-for-debugging"></a>Id's zoeken voor fout opsporing

In deze sectie wordt beschreven hoe u een Tenant-, object-en partner-ID-koppeling kunt vinden voor fout opsporing.

1. Ga naar de [Azure Portal](https://ms.portal.azure.com/).
2. Open Azure Cloud Shell door het Power shell-pictogram rechtsboven te selecteren.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-cloud-shell-1.png" alt-text="Power shell-pictogram in de rechter bovenhoek van het scherm.":::

3. Selecteer **Power shell**.

    :::image type="content" source="media/tenant-and-object-id/icon-powershell.png" alt-text="Selecteer de Power shell-koppeling.":::

4. Selecteer het vak **abonnement** om te kiezen aan welke doos de partner is gekoppeld en **Maak vervolgens opslag**. Dit is een eenmalige actie. Als u al een opslag hebt ingesteld, gaat u verder met de volgende stap.

    :::image type="content" source="media/tenant-and-object-id/create-storage-window.png" alt-text="Selecteer de knop opslag maken.":::

5. Als u opslag (of al hebt) maakt, wordt het Azure Cloud Shell-venster geopend.

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-1.png" alt-text="Het Azure Cloud Shell-venster.":::

6. Voor partner koppelings Details installeert u de extensie met de volgende opdracht:<br>`az extension add --name managementpartner`.<br>Azure Cloud Shell ziet u dat de extensie al is geïnstalleerd:

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-2.png" alt-text="Het Azure Cloud Shell venster met de extensie is al geïnstalleerd.":::

7. Raadpleeg de partner gegevens met behulp van deze opdracht:<br>`az managementpartner show --partner-id xxxxxx`<br>Bijvoorbeeld: `az managementpartner show --partner-id 4760962`.<br>Een scherm opname van de opdracht resultaten uitlijnen die er ongeveer als volgt uitziet:

    :::image type="content" source="media/tenant-and-object-id/partner-id-sample-screenshot.png" alt-text="Voorbeeld scherm met de resultaten van de vorige opdracht om de parter-ID weer te geven.":::

>[!NOTE]
>Als meerdere abonnementen een scherm opname vereisen, gebruikt u deze opdracht om te scha kelen tussen abonnementen:<br>`az account set --subscription "My Demos"`

## <a name="next-steps"></a>Volgende stappen

- [Ondersteunde landen en regio's](sell-from-countries.md)