---
title: Toegang tot gegevens bronnen on-premises
description: Verbinding maken met on-premises gegevens bronnen vanuit Azure Logic Apps door een gegevens gateway resource in azure te maken
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 356e63bb0a749ad0f41d886e75971e9b05c7f9dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99218991"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Verbinding maken met on-premises gegevensbronnen vanuit Azure Logic Apps

Nadat u [de *on-premises gegevens gateway* op een lokale computer hebt geïnstalleerd](../logic-apps/logic-apps-gateway-install.md) en voordat u toegang hebt tot gegevens bronnen on-premises vanuit uw Logic apps, moet u een gateway bron maken in azure voor de installatie van de gateway. U kunt deze gateway bron vervolgens selecteren in de triggers en acties die u wilt gebruiken voor [on-premises Connect oren](../connectors/apis-list.md#on-premises-connectors) die beschikbaar zijn in azure Logic apps. Azure Logic Apps ondersteunt Lees-en schrijf bewerkingen via de gegevens gateway. Deze bewerkingen hebben echter [limieten voor de grootte van de nettolading](/data-integration/gateway/service-gateway-onprem#considerations).

In dit artikel wordt beschreven hoe u een Azure gateway-resource maakt voor een eerder [geïnstalleerde gateway op uw lokale computer](../logic-apps/logic-apps-gateway-install.md). Zie [hoe de gateway werkt](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)voor meer informatie over de gateway.

> [!TIP]
> Als u rechtstreeks toegang wilt krijgen tot on-premises resources in azure Virtual Networks zonder dat u de gateway hoeft te gebruiken, kunt u overwegen om in plaats daarvan een [*integratie service omgeving*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) te maken. 

Zie de volgende artikelen voor meer informatie over het gebruik van de gateway met andere services:

* [Micro soft power on-premises gegevens gateway automatiseren](/power-automate/gateway-reference)
* [On-premises gegevens gateway van micro soft Power BI](/power-bi/service-gateway-onprem)
* [On-premises gegevens gateway van micro soft power apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services on-premises gegevens gateway](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Ondersteunde gegevensbronnen

De on-premises gegevens gateway in Azure Logic Apps ondersteunt de [on-premises connectors](../connectors/apis-list.md#on-premises-connectors) voor deze gegevens bronnen:

* BizTalk Server 2016
* Bestandssysteem
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

U kunt ook [aangepaste connectors](../logic-apps/custom-connector-overview.md) maken die via http of HTTPS verbinding maken met gegevens bronnen met behulp van rest of SOAP. Hoewel de gateway zelf geen extra kosten in rekening brengt, is het [Logic apps prijs model](../logic-apps/logic-apps-pricing.md) van toepassing op deze connectors en andere bewerkingen in azure Logic apps.

## <a name="prerequisites"></a>Vereisten

* U hebt [de on-premises gegevens gateway al geïnstalleerd op een lokale computer](../logic-apps/logic-apps-gateway-install.md). Deze Gateway-installatie moet bestaan voordat u een gateway bron kunt maken die is gekoppeld aan deze installatie.

* U hebt [hetzelfde Azure-account en-abonnement](../logic-apps/logic-apps-gateway-install.md#requirements) dat u hebt gebruikt voor de installatie van de gateway. Dit Azure-account mag alleen deel uitmaken van een enkele [Azure Active Directory (Azure AD)-Tenant of-map](../active-directory/fundamentals/active-directory-whatis.md#terminology). U moet hetzelfde Azure-account en-abonnement gebruiken om uw gateway resource in azure te maken, omdat alleen de Gateway beheerder de gateway bron in azure kan maken. Service-principals worden momenteel niet ondersteund.

  * Wanneer u een gateway bron in azure maakt, selecteert u een gateway-installatie om een koppeling met uw gateway bron en alleen die gateway bron te maken. Elke gateway bron kan slechts worden gekoppeld aan één gateway-installatie. U kunt geen gateway-installatie selecteren die al is gekoppeld aan een andere gateway resource.

  * Uw logische app en gateway resource hoeven niet te bestaan in hetzelfde Azure-abonnement. In triggers en acties waar u de gateway bron kunt gebruiken, kunt u een ander Azure-abonnement met een gateway bron selecteren, maar alleen als dit abonnement bestaat in dezelfde Azure AD-Tenant of-map als uw logische app. U moet ook beschikken over beheerders machtigingen op de gateway, die een andere beheerder voor u kan instellen. Zie [Data gateway: Automation met Power shell-deel 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) en [Power shell: data gateway-add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser)voor meer informatie.

    > [!NOTE]
    > Op dit moment kunt u geen gateway bron of installatie van meerdere abonnementen delen. Zie [Microsoft Azure feedback forum](https://feedback.azure.com/forums/34192--general-feedback)voor informatie over het verzenden van product feedback.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Azure gateway-resource maken

Nadat u de gateway op een lokale computer hebt geïnstalleerd, maakt u de Azure-resource voor uw gateway.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met hetzelfde Azure-account dat is gebruikt om de gateway te installeren.

1. Voer in het zoekvak Azure Portal "on-premises gegevens gateway" in en selecteer **on-premises gegevens gateways**.

   ![On-premises gegevens gateway zoeken](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. Selecteer **toevoegen** onder **on-premises gegevens gateways**.

   ![Nieuwe Azure-resource toevoegen voor gegevens gateway](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. Geef onder **verbindings gateway maken** deze informatie voor uw gateway bron op. Selecteer **Maken** als u klaar bent.

   | Eigenschap | Beschrijving |
   |----------|-------------|
   | **Resourcenaam** | Geef een naam op voor de gateway resource die alleen letters, cijfers, afbreek streepjes ( `-` ), onderstrepings tekens ( `_` ), haakjes ( `(` , `)` ) of punten ( `.` ) bevat. |
   | **Abonnement** | Selecteer het Azure-abonnement voor het Azure-account dat is gebruikt voor de installatie van de gateway. Het standaard abonnement is gebaseerd op het Azure-account dat u hebt gebruikt om u aan te melden. |
   | **Resourcegroep** | De [Azure-resource groep](../azure-resource-manager/management/overview.md) die u wilt gebruiken |
   | **Locatie** | Dezelfde regio of locatie die is geselecteerd voor de gateway-Cloud service tijdens de installatie van de [Gateway](../logic-apps/logic-apps-gateway-install.md). Anders wordt de installatie van de gateway niet weer gegeven in de lijst **installatie naam** . De locatie van de logische app kan verschillen van de resource locatie van uw gateway. |
   | **Installatie naam** | Selecteer een gateway-installatie die alleen in de lijst wordt weer gegeven als aan deze voor waarden wordt voldaan: <p><p>-De gateway-installatie maakt gebruik van dezelfde regio als de gateway resource die u wilt maken. <br>-De gateway-installatie is niet gekoppeld aan een andere Azure gateway-resource. <br>-De gateway-installatie is gekoppeld aan hetzelfde Azure-account dat u gebruikt om de gateway bron te maken. <br>-Uw Azure-account hoort bij een enkele [Azure Active Directory (Azure AD)-Tenant of-map](../active-directory/fundamentals/active-directory-whatis.md#terminology) en is hetzelfde account dat u hebt gebruikt voor de installatie van de gateway. <p><p>Zie het gedeelte Veelgestelde [vragen](#faq) voor meer informatie. |
   |||

   Hier volgt een voor beeld waarin een gateway-installatie wordt weer gegeven die zich in dezelfde regio bevindt als de gateway bron en is gekoppeld aan hetzelfde Azure-account:

   ![Details opgeven voor het maken van een gegevens gateway resource](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Verbinding maken met on-premises gegevens

Nadat u de gateway resource hebt gemaakt en uw Azure-abonnement aan deze resource hebt gekoppeld, kunt u nu een verbinding maken tussen uw logische app en uw on-premises gegevens bron met behulp van de gateway.

1. In de Azure Portal maakt of opent u de logische app in de ontwerp functie voor logische apps.

1. Voeg een connector toe die on-premises verbindingen ondersteunt, bijvoorbeeld **SQL Server**.

1. Selecteer **verbinding via on-premises gegevens gateway**.

1. Selecteer in de lijst **abonnement** onder **Gateway** het Azure-abonnement met de gewenste gateway resource.

   Uw logische app en gateway resource hoeven niet te bestaan in hetzelfde Azure-abonnement. U kunt kiezen uit andere Azure-abonnementen die elk een gateway Resource hebben, maar alleen als deze abonnementen in dezelfde Azure AD-Tenant of Directory aanwezig zijn als uw logische app, en u beschikt over beheerders rechten op de gateway, die een andere beheerder voor u kan instellen. Zie [Data gateway: Automation met Power shell-deel 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) en [Power shell: data gateway-add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser)voor meer informatie.
  
1. Selecteer in de lijst **verbindings gateway** , waarin de beschik bare gateway bronnen in het geselecteerde abonnement worden weer gegeven, de gateway resource die u wilt. Elke gateway bron is gekoppeld aan één gateway-installatie.

   > [!NOTE]
   > De lijst met gateways bevat gateway bronnen in andere regio's omdat de locatie van de logische app kan verschillen van de locatie van de gateway bron. 

1. Geef een unieke verbindings naam en andere vereiste informatie op die afhankelijk is van de verbinding die u wilt maken.

   Met een unieke verbindings naam kunt u die verbinding later gemakkelijk vinden, met name als u meerdere verbindingen maakt. Indien van toepassing moet u ook het gekwalificeerde domein voor uw gebruikers naam toevoegen.

   Hier volgt een voorbeeld:

   ![Verbinding maken tussen logische app en gegevens gateway](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Selecteer **Maken** als u klaar bent.

Uw gateway verbinding is nu klaar voor gebruik door uw logische app.

## <a name="edit-connection"></a>Verbinding bewerken

Als u de instellingen voor een gateway verbinding wilt bijwerken, kunt u de verbinding bewerken.

1. Als u alle API-verbindingen voor uw logische app wilt vinden, selecteert u in het menu van de logische app onder **ontwikkelingsprogram ma's** de optie **API-verbindingen**.

   ![Selecteer API-verbindingen in het menu van de logische app.](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Selecteer de gewenste gateway verbinding en selecteer vervolgens API- **verbinding bewerken**.

   > [!TIP]
   > Als uw updates niet van kracht worden, kunt u proberen om het gateway Windows-Service account voor de gateway-installatie te [stoppen en opnieuw te starten](../logic-apps/logic-apps-gateway-install.md#restart-gateway) .

Om te zoeken naar alle API-verbindingen die zijn gekoppeld aan uw Azure-abonnement:

* Selecteer in het menu Azure Portal **alle services**  >  **Web**  >  **API-verbindingen**.
* U kunt ook **alle resources** in het menu Azure Portal selecteren. Stel het **type** filter in op **API-verbinding**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Gateway resource verwijderen

Als u een andere gateway bron wilt maken, koppelt u de gateway-installatie aan een andere gateway bron of verwijdert u de gateway bron, kunt u de gateway bron verwijderen zonder dat dit van invloed is op de installatie van de gateway.

1. Selecteer in het menu Azure Portal **alle resources**, of zoek **alle resources** op een wille keurige pagina en selecteer deze. Zoek en selecteer de bron van de gateway.

1. Als dit nog niet is geselecteerd, selecteert u in het menu van de gateway resource de optie **on-premises gegevens gateway**. Selecteer **verwijderen** op de werk balk van de gateway resource.

   Bijvoorbeeld:

   ![Gateway resource verwijderen in azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V**: Waarom wordt de installatie van mijn gateway niet weer gegeven wanneer ik mijn gateway resource Maak in azure? <br/>
**Een**: dit probleem kan om de volgende redenen optreden:

* Uw Azure-account is niet hetzelfde account dat u hebt gebruikt voor de installatie van de gateway op uw lokale computer. Controleer of u bent aangemeld bij de Azure Portal met dezelfde identiteit die u hebt gebruikt voor de installatie van de gateway. Alleen de Gateway-beheerder kan de gateway bron maken in Azure. Service-principals worden momenteel niet ondersteund.

* Uw Azure-account behoort niet tot één [Azure AD-Tenant of-map](../active-directory/fundamentals/active-directory-whatis.md#terminology). Controleer of u gebruikmaakt van dezelfde Azure AD-Tenant of-map die u hebt gebruikt tijdens de installatie van de gateway.

* De installatie van de gateway bron en-gateway bestaat niet in dezelfde regio. De locatie van uw logische app kan echter verschillen van de resource locatie van uw gateway.

* De installatie van de gateway is al gekoppeld aan een andere gateway resource. Elke gateway bron kan slechts worden gekoppeld aan één gateway-installatie, die kan worden gekoppeld aan één Azure-account en-abonnement. U kunt dus geen gateway-installatie selecteren die al aan een andere gateway resource is gekoppeld. Deze installaties worden niet weer gegeven in de lijst **installatie naam** .

  Als u uw gateway registraties wilt controleren in de Azure Portal, zoekt u alle Azure-resources met het resource type **on-premises gegevens gateways** in *al* uw Azure-abonnementen. Zie [Gateway resource verwijderen](#change-delete-gateway-resource)als u de gateway-installatie wilt ontkoppelen van de andere gateway resource.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Volgende stappen

* [Uw logische apps beveiligen](./logic-apps-securing-a-logic-app.md)
* [Algemene voor beelden en scenario's voor Logic apps](./logic-apps-examples-and-scenarios.md)
