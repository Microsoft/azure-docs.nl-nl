---
title: Datadog maken-Azure-partner oplossingen
description: In dit artikel wordt beschreven hoe u de Azure Portal gebruikt om een instantie van Datadog te maken.
ms.service: partner-services
ms.topic: quickstart
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: 7af8b82c5da6c60527b45b6e8e292b9f067016ec
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748458"
---
# <a name="quickstart-get-started-with-datadog"></a>Snelstartgids: aan de slag met Datadog

In deze Quick Start maakt u een instantie van Datadog. U kunt een nieuwe Datadog-organisatie maken of koppelen aan een bestaande Datadog-organisatie.

## <a name="pre-requisites"></a>Vereisten

Als u de Azure Datadog-integratie wilt instellen, moet u **eigenaar** zijn van het Azure-abonnement. Zorg ervoor dat u de juiste toegang hebt voordat u de installatie start.

Als u de functie single Sign-On (SSO) van de Security Assertion Markup Language (SAML) wilt gebruiken in de Datadog-resource, moet u een bedrijfs toepassing instellen. Als u een bedrijfs toepassing wilt toevoegen, hebt u een van de volgende rollen nodig: globale beheerder, Cloud toepassings beheerder, toepassings beheerder of eigenaar van de Service-Principal.

Gebruik de volgende stappen om de bedrijfs toepassing in te stellen:

1. Ga naar [Azure Portal](https://portal.azure.com). Selecteer **Azure Active Directory**.
1. Selecteer in het linkerdeelvenster **Enterprise-toepassingen**.
1. Selecteer een **nieuwe toepassing**.
1. Zoek in **de galerie** naar *Datadog*. Selecteer het Zoek resultaat en selecteer vervolgens **toevoegen**.

   :::image type="content" source="media/create/datadog-azure-ad-app-gallery.png" alt-text="Datadog-toepassing in de AAD Enter prise Gallery." border="true":::

1. Zodra de app is gemaakt, gaat u naar eigenschappen in het deel venster aan de zijkant. Stel de **gebruikers toewijzing vereist** in op **Nee** en selecteer **Opslaan**.

   :::image type="content" source="media/create/user-assignment-required.png" alt-text="Eigenschappen instellen voor de Datadog-toepassing" border="true":::

1. Ga in het deel venster aan de zijkant naar **eenmalige aanmelding** . Selecteer vervolgens **SAML**.

   :::image type="content" source="media/create/saml-sso.png" alt-text="SAML-verificatie." border="true":::

1. Selecteer **Ja** wanneer u wordt gevraagd **instellingen voor eenmalige aanmelding op te slaan**.

   :::image type="content" source="media/create/save-sso.png" alt-text="Eenmalige aanmelding opslaan voor de Datadog-app" border="true":::

1. De installatie van eenmalige aanmelding is nu voltooid.

## <a name="find-offer"></a>Aanbieding zoeken

Gebruik de Azure Portal om Datadog te vinden.

1. Ga naar [Azure Portal](https://portal.azure.com/) en meld u aan.

1. Als u **Marketplace** in een recente sessie hebt bezocht, selecteert u het pictogram tussen de beschikbare opties. Als dat niet het geval is, zoekt u naar _Marketplace_.

    :::image type="content" source="media/create/marketplace.png" alt-text="Pictogram van Marketplace.":::

1. Zoek op de Marketplace naar **Datadog**.

1. Selecteer in het scherm overzicht van plannen de optie **instellen en abonneren**.

   :::image type="content" source="media/create/datadog-app.png" alt-text="Datadog-toepassing in azure Marketplace.":::

## <a name="create-a-datadog-resource-in-azure"></a>Een Datadog-resource maken in azure

In de portal wordt een formulier weer gegeven voor het maken van de Datadog-resource.

:::image type="content" source="media/create/datadog-create-resource.png" alt-text="Datadog-resource maken" border="true":::

Geef de volgende waarden op.

|Eigenschap | Beschrijving
|:-----------|:-------- |
| Abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor het maken van de Datadog-resource. U moet eigenaar toegang hebben. |
| Resourcegroep | Geef aan of u een nieuwe resourcegroep wilt maken of een bestaande groep wilt gebruiken. Een [resourcegroep](../../azure-resource-manager/management/overview.md#resource-groups) is een container met gerelateerde resources voor een Azure-oplossing. |
| Resourcenaam | Geef een naam op voor de Datadog-resource. Deze naam is de naam van de nieuwe Datadog-organisatie bij het maken van een nieuwe Datadog-organisatie. |
| Locatie | Selecteer VS - west 2. Op dit moment is West VS 2 de enige ondersteunde regio. |
| Datadog-organisatie | Als u een nieuwe Datadog-organisatie wilt maken, selecteert u **Nieuw**. Als u een koppeling wilt maken naar een bestaande Datadog-organisatie, selecteert u **bestaande**. |
| Prijs plan | Wanneer u een nieuwe organisatie maakt, selecteert u in de lijst met beschik bare Datadog plannen. |
| Facturerings termijn | Keren. |

Zie de volgende sectie als u een koppeling met een bestaande Datadog-organisatie maakt. Anders selecteert u **volgende: metrische gegevens en logboeken** en gaat u verder met de volgende sectie.

## <a name="link-to-existing-datadog-organization"></a>Koppeling naar de bestaande Datadog-organisatie

U kunt uw nieuwe Datadog-resource in azure koppelen aan een bestaande Datadog-organisatie.

Selecteer **bestaande** voor gegevens organisatie en selecteer vervolgens **koppeling naar Datadog org**.

:::image type="content" source="media/create/link-to-existing.png" alt-text="Koppeling naar de bestaande Datadog-organisatie." border="true":::

Met de koppeling opent u een Datadog-verificatie venster. Meld u aan bij Datadog.

Standaard koppelt Azure uw huidige Datadog-organisatie aan uw Datadog-resource. Als u een koppeling wilt maken naar een andere organisatie, selecteert u de juiste organisatie in het verificatie venster, zoals hieronder wordt weer gegeven.

:::image type="content" source="media/create/select-datadog-organization.png" alt-text="Selecteer de juiste Datadog-organisatie die u wilt koppelen" border="true":::

## <a name="configure-metrics-and-logs"></a>Metrische gegevens en logboeken configureren

Gebruik Azure-resource Tags om te configureren welke metrische gegevens en logboeken naar Datadog worden verzonden. U kunt metrische gegevens en logboeken voor specifieke resources opnemen of uitsluiten.

Label regels voor het verzenden van **metrische gegevens** zijn:

- Standaard worden metrische gegevens verzameld voor alle resources, met uitzonde ring van virtuele machines, virtuele-machine schaal sets en app service-plannen.
- Virtuele machines, virtuele-machine schaal sets en app service-plannen met *include* -Tags verzenden metrische gegevens naar Datadog.
- Virtuele machines, virtuele-machine schaal sets en app service-plannen met *exclude* -Tags verzenden geen metrische gegevens naar Datadog.
- Als er een conflict is tussen insluitings-en uitsluitings regels, heeft uitsluiting prioriteit

Label regels voor het verzenden van **Logboeken** zijn:

- Standaard worden logboeken verzameld voor alle resources.
- Azure-resources met tags voor *include* -bestanden verzenden logboeken naar Datadog.
- Azure-resources met  *exclude* -Tags verzenden geen logboeken naar Datadog.
- Als er een conflict is tussen insluitings-en uitsluitings regels, heeft uitsluiting prioriteit.

In de onderstaande scherm afbeelding ziet u bijvoorbeeld een tag-regel waarbij alleen de virtuele machines, virtuele-machine schaal sets en app service-plannen met de label *Datadog = True* naar Datadog worden verzonden.

:::image type="content" source="media/create/config-metrics-logs.png" alt-text="Logboeken en metrische gegevens configureren." border="true":::

Er zijn twee typen logboeken die van Azure naar Datadog kunnen worden verzonden.

1. **Logboeken op abonnements niveau** : bieden inzicht in de bewerkingen op uw resources op het [besturings vlak](../../azure-resource-manager/management/control-plane-and-data-plane.md). Er zijn ook updates voor service Health-gebeurtenissen opgenomen. Gebruik het activiteiten logboek om te bepalen wat, wie en wanneer voor schrijf bewerkingen (PUT, POST, DELETE). Er is één activiteiten logboek voor elk Azure-abonnement.

1. **Azure-resource logboeken** : bieden inzicht in bewerkingen die zijn uitgevoerd op een Azure-resource op het vlak van de [gegevens](../../azure-resource-manager/management/control-plane-and-data-plane.md). U kunt bijvoorbeeld een geheim ophalen van een Key Vault een gegevensvlak bewerking is. Of het maken van een aanvraag voor een Data Base is ook een gegevensvlak bewerking. De inhoud van bron Logboeken is afhankelijk van de Azure-service en het resource type.

Als u logboeken op abonnements niveau wilt verzenden naar Datadog, selecteert u **Logboeken voor abonnements activiteiten verzenden**. Als deze optie niet is ingeschakeld, worden geen logboeken op abonnements niveau verzonden naar Datadog.

Als u Azure-resource logboeken wilt verzenden naar Datadog, selecteert u **Azure-resource logboeken verzenden voor alle gedefinieerde resources**. De typen Azure-resource logboeken worden vermeld in [Azure monitor resource logboek categorieën](../../azure-monitor/essentials/resource-logs-categories.md).  Gebruik Azure-resource Tags om de set Azure-resources die logboeken naar Datadog verzenden te filteren.  

De logboeken die naar Datadog worden verzonden, worden door Azure in rekening gebracht. Zie de [prijzen van platform logboeken](https://azure.microsoft.com/pricing/details/monitor/) die worden verzonden naar Azure Marketplace-partners voor meer informatie.

Zodra u de metrische gegevens en Logboeken hebt geconfigureerd, selecteert u **volgende: eenmalige aanmelding**.

## <a name="configure-single-sign-on"></a>Eenmalige aanmelding configureren

Als uw organisatie gebruikmaakt van Azure Active Directory als id-provider, kunt u eenmalige aanmelding instellen vanuit de Azure Portal Datadog. Als uw organisatie een andere ID-provider gebruikt of als u op dit moment geen eenmalige aanmelding wilt instellen, kunt u deze sectie overs Laan.

Als u de Datadog-resource koppelt aan een bestaande Datadog-organisatie, kunt u eenmalige aanmelding in deze stap niet instellen. In plaats daarvan stelt u eenmalige aanmelding in nadat u de Datadog-resource hebt gemaakt. Zie [eenmalige aanmelding opnieuw configureren](manage.md#reconfigure-single-sign-on)voor meer informatie.

:::image type="content" source="media/create/linking-sso.png" alt-text="Eenmalige aanmelding voor het koppelen aan een bestaande Datadog-organisatie." border="true":::

Als u eenmalige aanmelding wilt instellen via Azure Active Directory, schakelt u het selectie vakje in voor het **inschakelen van eenmalige aanmelding via Azure Active Directory**.

De Azure Portal haalt de juiste Datadog-toepassing op uit Azure Active Directory. De app komt overeen met de Enter prise-app die u in een eerdere stap hebt geleverd.

Selecteer de naam van de Datadog-app.

:::image type="content" source="media/create/sso.png" alt-text="Schakel eenmalige aanmelding in op Datadog." border="true":::

Selecteer **Volgende: Tags**.

## <a name="add-custom-tags"></a>Aangepaste labels toevoegen

U kunt aangepaste labels opgeven voor de nieuwe Datadog-resource. Geef naam-en waardeparen op voor de tags die moeten worden toegepast op de Datadog-resource.

:::image type="content" source="media/create/tags.png" alt-text="Aangepaste labels voor de Datadog-resource toevoegen." border="true":::

Wanneer u klaar bent met het toevoegen van tags, selecteert u **volgende: controleren + maken**.

## <a name="review--create-datadog-resource"></a>Datadog-resource bekijken en maken

Controleer uw selecties en de gebruiks voorwaarden. Nadat de validatie is voltooid, selecteert u **Maken**.

:::image type="content" source="media/create/review-create.png" alt-text="Bekijk en maak een Datadog-resource." border="true":::

Azure implementeert de Datadog-resource.

Wanneer het proces is voltooid, selecteert u **naar resource gaan** om de Datadog-resource weer te geven.

:::image type="content" source="media/create/go-to-resource.png" alt-text="Datadog-bron implementatie." border="true":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [De Datadog-resource beheren](manage.md)
