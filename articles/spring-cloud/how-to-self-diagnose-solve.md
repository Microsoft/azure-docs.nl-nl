---
title: Zelf diagnose en probleem oplossing in azure lente Cloud
description: Meer informatie over het zelf vaststellen en oplossen van problemen in azure lente-Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.custom: devx-track-java
ms.openlocfilehash: c262ba87698c04da69728f7a370040c0679ec44e
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878093"
---
# <a name="how-to-self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>Zelf diagnose en probleem oplossing in azure lente Cloud

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

Azure veer Cloud diagnostiek is een interactieve ervaring bij het oplossen van problemen met uw app zonder configuratie. Azure lente-Cloud diagnose identificeert problemen en helpt u bij het oplossen van problemen die u helpen bij de probleem oplossing.

## <a name="prerequisites"></a>Vereisten
Voor deze zelfstudie hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
* Een geïmplementeerd Azure Spring Cloud-service-exemplaar. Volg onze [quickstart voor het implementeren van een app via Azure CLI](spring-cloud-quickstart.md) om aan de slag te gaan.
* Er is al minstens één toepassing gemaakt in uw service-exemplaar.

## <a name="navigate-to-the-diagnostics-page"></a>Ga naar de pagina met diagnostische gegevens
1. Meld u aan bij Azure Portal.
2. Ga naar de **Overzichtspagina** van Azure Spring Cloud.
3. Selecteer **problemen vaststellen en oplossen** in het navigatie deel venster aan de linkerkant.

 ![Diagnose en oplossen dialoog venster](media/spring-cloud-diagnose/diagnose-solve-dialog.png)

 ## <a name="search-logged-issues"></a>Geregistreerde problemen zoeken
Als u een probleem wilt opsporen, kunt u zoeken door een tref woord te typen of op oplossings groep te klikken om alles in die categorie te verkennen.

 ![Problemen met zoeken](media/spring-cloud-diagnose/search-detectors.png)

Door de selectie van de status controle van de **Configuratie server**, de **Configuratie server status** of de **Update geschiedenis van de configuratie server** worden verschillende resultaten weer gegeven.

![Opties voor problemen](media/spring-cloud-diagnose/detectors-options.png)

Zoek uw doel detectie en klik erop om uit te voeren. Er wordt een samen vatting van de diagnostische gegevens weer gegeven nadat u de detector hebt uitgevoerd. U kunt het **volledige rapport weer geven** selecteren om de diagnostische gegevens te controleren of op de knop **tegel menu weer geven** klikken om terug te gaan naar de lijst met detectors.

 ![Samenvattings diagnostiek](media/spring-cloud-diagnose/summary-diagnostics.png)

Op de pagina Diagnostische gegevens kunt u de diagnostische tijds periode wijzigen met de controller in de rechter bovenhoek. Als u meer metrische gegevens of logboeken wilt weer geven, schakelt u elke diagnose in. Er kan een vertraging van 15 minuten zijn voor metrische gegevens en Logboeken.

 ![Details van diagnostische gegevens](media/spring-cloud-diagnose/diagnostics-details.png)

Sommige resultaten bevatten gerelateerde documentatie.

 ![Gerelateerde Details](media/spring-cloud-diagnose/related-details.png)

## <a name="next-steps"></a>Volgende stappen
* [Spring Cloud-resources bewaken met behulp van waarschuwingen en actiegroepen](spring-cloud-tutorial-alerts-action-groups.md)
* [Beveiligingscontroles voor Azure Spring Cloud Service](spring-cloud-concept-security-controls.md)