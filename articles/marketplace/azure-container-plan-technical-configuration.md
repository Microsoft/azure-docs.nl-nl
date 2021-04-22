---
title: Stel de technische configuratie voor een Azure Container-aanbieding in Microsoft AppSource.
description: Stel de technische configuratie voor een Azure Container-aanbieding in Microsoft AppSource.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 04/21/2021
ms.openlocfilehash: dfee13f31509fedc9558befeaaeebadedf2104de
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107892652"
---
# <a name="set-plan-technical-configuration-for-an-azure-container-offer"></a>Technische configuratie voor plannen instellen voor een Azure Container-aanbieding

Container-afbeeldingen moeten worden gehost in een [Azure Container Registry](https://azure.microsoft.com/services/container-registry/). Gebruik deze pagina om referentie-informatie op te geven voor uw opslagplaats voor containerafbeeldingen in de Azure Container Registry.

Nadat u de aanbieding hebt indienen, wordt uw containerkopie gekopieerd naar Azure Marketplace in een specifiek openbaar containerregister. Alle aanvragen van Azure-gebruikers om uw module te gebruiken, worden uitgevoerd vanuit Azure Marketplace openbare containerregister, niet uw persoonlijke containerregister.

U kunt zich richten op meerdere platforms en verschillende versies van de containerafbeelding van de module leveren met behulp van tags. Zie Prepare Azure Container technical assets (Technische assets voor Azure Container voorbereiden) voor meer informatie over [tags en versie-versies.](azure-container-technical-assets.md)

## <a name="image-repository-details"></a>Details van opslagplaats voor afbeeldingen

Geef de **Azure-abonnements-id** op waar resourcegebruik wordt gerapporteerd en services worden gefactureerd voor de Azure Container Registry die uw containerafbeelding bevat. U vindt deze id op de [pagina Abonnementen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in de Azure Portal.

Geef de [**naam van de Azure-resourcegroep**](../azure-resource-manager/management/manage-resource-groups-portal.md) op die de Azure Container Registry met uw containerafbeelding. De resourcegroep moet toegankelijk zijn in de abonnements-id (hierboven). U vindt de naam op de [pagina Resourcegroepen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) in de Azure Portal.

Geef de [**naam van het Azure-containerregister**](../container-registry/container-registry-intro.md) op die uw containerafbeelding heeft. Het containerregister moet aanwezig zijn in de Azure-resourcegroep die u eerder hebt opgegeven. Geef alleen de registernaam op, niet de volledige naam van de aanmeldingsserver. Laat **azurecr.io** naam weg. U vindt de registernaam op de [pagina Containerregisters](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) in de Azure Portal.

Geef de [**gebruikersnaam van de beheerder op voor de Azure Container Registry**](../container-registry/container-registry-authentication.md#admin-account) gekoppeld aan de Azure Container Registry die uw containerafbeelding heeft. De gebruikersnaam en het wachtwoord (volgende stap) zijn vereist om ervoor te zorgen dat uw bedrijf toegang heeft tot het register. Als u de gebruikersnaam en  het wachtwoord van de beheerder wilt op halen, stelt u de eigenschap met beheerdersrechten in op Waar met **behulp** van de Azure Command-Line Interface (CLI). U kunt eventueel Gebruiker **met beheerdersrechten instellen** **op Inschakelen** in Azure Portal.

:::image type="content" source="media/azure-container/azure-create-12-update-container-registry-edit.png" alt-text="Illustreert het dialoogvenster Containerregister bijwerken.":::

Geef het **beheerderswachtwoord voor de Azure Container Registry** de gebruikersnaam van de beheerder op die is Azure Container Registry de container-afbeelding. De gebruikersnaam en het wachtwoord zijn vereist om ervoor te zorgen dat uw bedrijf toegang heeft tot het register. U kunt het wachtwoord ophalen uit de Azure Portal door naar Container Registry  >  **toegangssleutels** of met Azure CLI te gaan met behulp van [de opdracht show.](/cli/azure/acr/credential#az-acr-credential-show)

:::image type="content" source="media/azure-container/azure-create-13-access-keys.png" alt-text="Illustreert het scherm met de toegangssleutel in Azure Portal.":::

Geef de **naam van de opslagplaats op in de Azure Container Registry** die uw afbeelding heeft. U geeft de naam van de opslagplaats op wanneer u de afbeelding naar het register pusht. U kunt de naam van de [](https://azure.microsoft.com/services/container-registry/)opslagplaats vinden door naar de pagina Container Registry  >  **opslagplaatsen te gaan.** Zie Opslagplaatsen voor [containerregisters](../container-registry/container-registry-repositories.md)weergeven in de Azure Portal. Nadat de naam is ingesteld, kan deze niet meer worden gewijzigd. Gebruik een unieke naam voor elke aanbieding in uw account.

## <a name="image-versions"></a>Installatiekopieversies

Klanten moeten automatisch updates kunnen ontvangen van de Azure Marketplace wanneer u een update publiceert. Als ze niet willen bijwerken, moeten ze een specifieke versie van uw afbeelding kunnen blijven gebruiken. U kunt dit doen door nieuwe afbeeldingstags toe te voegen telkens wanneer u een update voor de afbeelding maakt.

Selecteer **Versie van de afbeelding toevoegen** om een afbeeldingstag **op** te nemen die naar de nieuwste versie van uw afbeelding op alle ondersteunde platforms wijst. Het moet ook een versietag bevatten (bijvoorbeeld beginnend met xx.xx.xx, waarbij xx een getal is). Klanten moeten [manifesttags gebruiken om](https://github.com/estesp/manifest-tool) zich op meerdere platforms te richten. Alle tags waarnaar wordt verwezen door een manifesttag moeten ook worden toegevoegd, zodat we ze kunnen uploaden. Alle manifesttags (met uitzondering van de meest recente tag) moeten beginnen met X.Y- of X.Y.Z, waarbij X, Y en Z gehele getallen zijn. Als een meest recente tag bijvoorbeeld naar , en wijst, moeten deze `1.0.1-linux-x64` zes tags worden toegevoegd aan dit `1.0.1-linux-arm32` `1.0.1-windows-arm32` veld. Zie Prepare your Azure Container technical assets (Uw technische assets voor Azure Container voorbereiden) voor meer informatie over [tags en versie-versies.](azure-container-technical-assets.md)

> [!TIP]
> Voeg een testtag toe aan uw afbeelding, zodat u de afbeelding tijdens het testen kunt identificeren.

<!-- possible future restore

## Samples

These examples show how the plan listing fields appear in different views.

These are the fields in Azure Marketplace when viewing plan details:

:::image type="content" source="media/azure-container/azure-create-10-plan-details-mtplc.png" alt-text="Illustrates the fields you see when viewing plan details in Azure Marketplace.":::

These are plan details on the Azure portal:

:::image type="content" source="media/azure-container/azure-create-11-plan-details-portal.png" alt-text="Illustrates plan details on the Azure portal.":::

Select **Save draft**, then **← Plan overview**  in the left-nav menu to return to the plan overview page.
-->
## <a name="next-steps"></a>Volgende stappen

- Als **u wilt gaan verkopen met Microsoft** (optioneel), selecteert u deze in het menu aan de linkerkant. Zie Partnerbetrokkenheid [bij co-sell voor meer informatie.](marketplace-co-sell.md)
- Als **u opnieuw wilt verkopen via CSP's** (Cloud Solution Partners, ook optioneel), selecteert u deze in het menu aan de linkerkant. Zie Opnieuw verkopen via [CSP-partners voor meer informatie.](cloud-solution-providers.md)
- Als u geen van beide instelt of als u klaar bent, is het tijd om uw aanbieding te controleren [en te publiceren.](review-publish-offer.md)
