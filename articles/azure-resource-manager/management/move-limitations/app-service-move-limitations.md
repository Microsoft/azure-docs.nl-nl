---
title: Azure App Service resources verplaatsen
description: Gebruik Azure Resource Manager om App Service resources te verplaatsen naar een nieuwe resource groep of een nieuw abonnement.
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 27555a4616befca41c7e970e947afa1cd1ff7248
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90531369"
---
# <a name="move-guidance-for-app-service-resources"></a>Richt lijnen voor App Service resources verplaatsen

In dit artikel worden de stappen beschreven voor het verplaatsen van App Service resources. Er zijn specifieke vereisten voor het verplaatsen van App Service-resources naar een nieuw abonnement.

## <a name="move-across-subscriptions"></a>Scha kelen tussen abonnementen

Bij het verplaatsen van een web-app tussen abonnementen, gelden de volgende richt lijnen:

- De doel resource groep mag geen bestaande App Service resources hebben. App Service resources zijn onder andere:
    - Web Apps
    - App Service-plannen
    - Geüploade of geïmporteerde TLS/SSL-certificaten
    - App Service-omgevingen
- Alle App Service resources in de resource groep moeten samen worden verplaatst.
- App Service omgevingen kunnen niet worden verplaatst naar een nieuwe resource groep of een nieuw abonnement. U kunt echter een web-app en een app service-plan verplaatsen naar een nieuw abonnement zonder de App Service Environment te verplaatsen. Na de verplaatsing wordt de web-app niet meer gehost in de App Service Environment.
- U kunt een certificaat dat is gebonden aan een web verplaatsen zonder de TLS-bindingen te verwijderen, zolang het certificaat met alle andere resources in de resource groep is verplaatst.
- App Service resources kunnen alleen worden verplaatst van de resource groep waarin ze oorspronkelijk zijn gemaakt. Als een App Service resource niet langer in de oorspronkelijke resource groep staat, zet u deze terug naar de oorspronkelijke resource groep. Verplaats vervolgens de resource over abonnementen.

Als u de oorspronkelijke resource groep niet meer weet, kunt u deze vinden via diagnostische gegevens. Selecteer voor uw web-app **diagnose en los problemen** op. Selecteer vervolgens **configuratie en beheer**.

![Diagnostische gegevens selecteren](./media/app-service-move-limitations/select-diagnostics.png)

Selecteer **migratie opties**.

![Migratie opties selecteren](./media/app-service-move-limitations/select-migration.png)

Selecteer de optie voor de aanbevolen stappen voor het verplaatsen van de web-app.

![Aanbevolen stappen selecteren](./media/app-service-move-limitations/recommended-steps.png)

U ziet de aanbevolen acties die u moet uitvoeren voordat u de resources verplaatst. De informatie bevat de oorspronkelijke resource groep voor de web-app.

![Scherm opname bevat de aanbevolen stappen voor het verplaatsen van micro soft dot-webresources.](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Ondersteuning verplaatsen

Zie de ondersteunings status verplaatsen voor het volgende om te bepalen welke App Service resources kunnen worden verplaatst:

- [Micro soft. AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Micro soft. DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Volgende stappen

Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../move-resource-group-and-subscription.md)voor opdrachten voor het verplaatsen van resources.
