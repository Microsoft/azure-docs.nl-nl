---
title: Problemen oplossen voor Datadog-Azure-partner oplossingen
description: Dit artikel bevat informatie over het oplossen van problemen met Datadog op Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 0e3c82f711de4cd9710c9aafe798a986e3403ed4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563705"
---
# <a name="troubleshooting-datadog-on-azure"></a>Problemen met Datadog oplossen in Azure

Dit document bevat informatie over het oplossen van problemen met de oplossingen die gebruikmaken van Datadog.

## <a name="purchase-errors"></a>Inkoop fouten

* De aankoop mislukt omdat een geldige credit card niet is verbonden met het Azure-abonnement of omdat er geen betalings methode aan het abonnement is gekoppeld.

  Gebruik een ander Azure-abonnement. Of Voeg de credit card of betalings methode voor het abonnement toe of werk deze bij. Zie [de credit-en betalings methode bijwerken](../../cost-management-billing/manage/change-credit-card.md)voor meer informatie.

* Het EA-abonnement staat geen Marketplace-aankopen toe.

  Gebruik een ander abonnement. U kunt ook controleren of uw EA-abonnement is ingeschakeld voor Marketplace-aankopen. Zie [Marketplace-aankopen inschakelen](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)voor meer informatie. Als deze opties het probleem niet oplossen, neemt u contact op met de [Datadog-ondersteuning](https://www.datadoghq.com/support).

## <a name="unable-to-create-datadog-resource"></a>Kan de Datadog-resource niet maken

Als u de Azure Datadog-integratie wilt instellen, moet u **eigenaar** zijn van het Azure-abonnement. Zorg ervoor dat u de juiste toegang hebt voordat u de installatie start.

## <a name="single-sign-on-errors"></a>Fouten bij eenmalige aanmelding

**Kan instellingen voor eenmalige aanmelding niet opslaan** . deze fout treedt op wanneer er een andere bedrijfs-app is die de Datadog SAML-id gebruikt. Als u wilt weten welke app deze gebruikt, selecteert u **bewerken** in de sectie basis configuratie van SAML.

U kunt dit probleem oplossen door de andere app uit te scha kelen of door de andere app te gebruiken als de Enter prise-app om SAML SSO in te stellen met Datadog. Als u besluit om de andere app te gebruiken, moet u ervoor zorgen dat de app de [vereiste instellingen](create.md#configure-single-sign-on)heeft.

De **app wordt niet weer gegeven in de pagina instellingen voor eenmalige aanmelding** , zoekt naar de toepassings-id. Als er geen resultaat wordt weer gegeven, controleert u de SAML-instellingen van de app. In het raster worden alleen apps met de juiste SAML-instellingen weer gegeven. 

De id-URL moet zijn `https://us3.datadoghq.com/account/saml/metadata.xml` .

De antwoord-URL moet zijn `https://us3.datadoghq.com/account/saml/assertion` .

De volgende afbeelding toont de juiste waarden.
  
:::image type="content" source="media/troubleshoot/troubleshooting.png" alt-text="Controleer de SAML-instellingen voor de Datadog-toepassing in AAD." border="true":::

**Gast gebruikers die zijn uitgenodigd voor de Tenant hebben geen toegang tot eenmalige aanmelding** , sommige gebruikers hebben twee e-mail adressen in azure Portal. Normaal gesp roken is één e-mail bericht de user principal name (UPN) en is het andere e-mail adres een alternatief e-mail adres.

Wanneer u een gast gebruiker uitnodigt, gebruikt u de UPN van de thuis Tenant. Door gebruik te maken van de UPN, moet u het e-mail adres synchroon houden tijdens het proces voor eenmalige aanmelding. U kunt de UPN vinden door te zoeken naar het e-mail adres in de rechter bovenhoek van de Azure Portal van de gebruiker.
  
## <a name="logs-not-being-emitted"></a>Logboeken niet verzonden

Alleen resources die worden vermeld in de Azure Monitor resource logboek categorieën verzenden logboeken naar Datadog. Als u wilt controleren of de resource logboeken verzendt naar Datadog, gaat u naar de diagnostische instelling voor Azure voor de specifieke resource. Controleer of er een Datadog diagnostische instelling is.

:::image type="content" source="media/troubleshoot/diagnostic-setting.png" alt-text="Datadog diagnostische instelling voor de Azure-resource" border="true":::

## <a name="metrics-not-being-emitted"></a>De metrische gegevens worden niet verzonden

De Datadog-resource krijgt een rol van **bewakings lezer** toegewezen in het juiste Azure-abonnement. Met deze rol kan de Datadog-resource metrische gegevens verzamelen en deze metrische gegevens naar Datadog verzenden.

Als u wilt controleren of de resource de juiste roltoewijzing heeft, opent u de Azure Portal en selecteert u het abonnement. Selecteer **Access Control (IAM)** in het linkerdeel venster. Zoek naar de naam van de Datadog-resource. Controleer of de Datadog-resource de roltoewijzing van de **bewakings** functie heeft, zoals hieronder wordt weer gegeven.

:::image type="content" source="media/troubleshoot/datadog-role-assignment.png" alt-text="Datadog-roltoewijzing in het Azure-abonnement" border="true":::

## <a name="datadog-agent-installation-fails"></a>De installatie van de Datadog-agent mislukt

De integratie van Azure Datadog biedt u de mogelijkheid om Datadog-agent op een virtuele machine of app service te installeren. Voor het configureren van de Datadog-agent wordt de API-sleutel gebruikt die is geselecteerd als **standaard sleutel** in het scherm API-sleutels. Als er geen standaard sleutel is geselecteerd, mislukt de installatie van de Datadog-agent.

Als de Datadog-agent is geconfigureerd met een onjuiste sleutel, gaat u naar het scherm API-sleutels en wijzigt u de **standaard sleutel**. U moet de Datadog-agent verwijderen en opnieuw installeren om de virtuele machine te configureren met de nieuwe API-sleutels.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het beheren van uw exemplaar](manage.md) van Datadog.
