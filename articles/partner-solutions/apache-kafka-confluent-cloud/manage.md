---
title: Een confluente Cloud beheren-Azure-partner oplossingen
description: In dit artikel wordt het beheer van een confluente Cloud op de Azure Portal beschreven. Eenmalige aanmelding instellen, een confluente organisatie verwijderen en ondersteuning krijgen.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/08/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f8a54096ecda4729f7070120a02be3055f933cea
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989142"
---
# <a name="manage-the-confluent-cloud-resource"></a>Confluent Cloud-resource beheren

In dit artikel wordt beschreven hoe u uw exemplaar van Apache Kafka beheert voor confluente Cloud in Azure. U ziet hoe u eenmalige aanmelding (SSO) instelt, een confluente organisatie verwijdert en een ondersteunings aanvraag maakt.

## <a name="single-sign-on"></a>Eenmalige aanmelding

Als u SSO wilt implementeren voor uw organisatie, kan de Tenant beheerder de galerie toepassing importeren. Deze stap is optioneel. Voor informatie over het importeren van een toepassing raadpleegt [u Quick Start: een toepassing toevoegen aan uw Azure Active Directory-Tenant (Azure AD)](../../active-directory/manage-apps/add-application-portal.md). Wanneer de Tenant beheerder de toepassing importeert, hoeven gebruikers geen expliciete toestemming te geven om toegang toe te staan voor de confluente Cloud Portal.

Voer de volgende stappen uit om SSO in te scha kelen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Navigeer naar het **overzicht** voor uw exemplaar van de confluente Cloud resource.
1. Selecteer de koppeling om te **beheren op confluente Cloud**.

   :::image type="content" source="media/sso-link.png" alt-text="Eenmalige aanmelding voor de portal.":::

1. Als de Tenant beheerder de galerie toepassing voor SSO-toestemming niet heeft geïmporteerd, moet u machtigingen en toestemming verlenen. Deze stap is alleen nodig wanneer u de koppeling voor het eerst opent om te **beheren op confluente Cloud**.
1. Kies een Azure AD-account voor eenmalige aanmelding bij de confluente Cloud Portal.
1. Nadat u toestemming hebt gegeven, wordt u omgeleid naar de confluente Cloud Portal.

## <a name="set-up-cluster"></a>Cluster instellen

Zie [een cluster maken in confluente Cloud-confluente documentatie](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)voor meer informatie over het instellen van uw cluster.

## <a name="delete-confluent-organization"></a>Confluente organisatie verwijderen

Wanneer u uw confluente Cloud resource niet meer nodig hebt, verwijdert u de resource in Azure en confluente Cloud.

De resources in azure verwijderen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **alle resources** en **filter op de naam** van de confluente Cloud resource of het **resource type** _Confluent-organisatie_. In de Azure Portal zoekt u de naam van de resource.
1. Selecteer **verwijderen** in het **overzicht** van de resource.

    :::image type="content" source="media/delete-resources-icon.png" alt-text="Pictogram resource verwijderen.":::

1. Om het verwijderen te bevestigen, typt u de naam van de resource en selecteert u **verwijderen**.

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="Vragen om het verwijderen van resources te bevestigen.":::

Als u de resource wilt verwijderen in confluente Cloud, raadpleegt u de documentatie voor [confluente Cloud omgevingen-Confluent-documentatie](https://docs.confluent.io/current/cloud/using/environments.html) en [confluente Cloud beginselen-confluente documentatie](https://docs.confluent.io/current/cloud/using/cloud-basics.html).

Het cluster en alle gegevens in het cluster worden definitief verwijderd. Als uw contract een component voor het bewaren van gegevens bevat, zorgt u ervoor dat uw gegevens worden bewaard gedurende de periode die is opgegeven in de [voor waarden van service-confluente documentatie](https://www.confluent.io/confluent-cloud-tos).

U wordt gefactureerd voor het gebruik van profactories tot aan het verwijderen van het cluster. Wanneer het cluster definitief is verwijderd, stuurt Confluent u een bevestiging per e-mail.

## <a name="get-support"></a>Ondersteuning krijgen

Als u een ondersteunings aanvraag wilt indienen, neemt u contact op met [Confluent-ondersteuning](https://support.confluent.io) of verzendt u een aanvraag via de portal, zoals hieronder wordt weer gegeven.

> [!NOTE]
> Stel voor de eerste keer dat gebruikers uw wacht woord opnieuw in om u aan te melden bij de portal voor Confluent-ondersteuning. Als u geen account hebt met confluente Cloud, stuurt u een e-mail naar `cloud-support@confluent.io` voor verdere ondersteuning.

In de portal kunt u een aanvraag indienen via Azure Help en ondersteuning, of rechtstreeks vanuit uw exemplaar van Apache Kafka voor confluente Cloud in Azure.

Een aanvraag indienen via Azure Help en ondersteuning:

1. Selecteer **Help en ondersteuning**.
1. Selecteer **een ondersteunings aanvraag maken**.
1. Selecteer in het formulier **Technical** voor **probleem type**. Selecteer uw abonnement. Selecteer in de lijst met Services de optie **Confluent op Azure**.

    :::image type="content" source="media/support-request-help.png" alt-text="Maak een ondersteunings aanvraag via de Help.":::

Voer de volgende stappen uit om een aanvraag in te dienen bij uw resource:

1. Selecteer uw confluente organisatie in de Azure Portal.
1. Selecteer **nieuwe ondersteunings aanvraag** in het menu aan de linkerkant van het scherm.
1. Als u een ondersteunings aanvraag wilt maken, selecteert u de koppeling naar de **confluente Portal**.

    :::image type="content" source="media/support-request.png" alt-text="Een ondersteunings aanvraag maken op basis van een exemplaar.":::

## <a name="next-steps"></a>Volgende stappen

Zie [problemen oplossen Apache Kafka voor confluente cloud oplossingen](troubleshoot.md)voor hulp bij het oplossen van problemen.
