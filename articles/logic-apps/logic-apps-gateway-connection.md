---
title: Toegang tot on-premises gegevensbronnen
description: Verbinding maken met on-premises gegevensbronnen vanuit Azure Logic Apps door een gegevensgatewayresource te maken in Azure
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 49da5d7f045ed06ba16696ebd16ad212b9d140d8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763305"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Verbinding maken met on-premises gegevensbronnen vanuit Azure Logic Apps

Nadat u [de *on-premises*](../logic-apps/logic-apps-gateway-install.md) gegevensgateway op een lokale computer hebt geïnstalleerd en voordat u on-premises toegang hebt tot gegevensbronnen vanuit uw logische apps, moet u een gatewayresource maken in Azure voor de installatie van uw gateway. Vervolgens kunt u deze gatewayresource selecteren in de triggers en acties die u wilt gebruiken voor [de on-premises connectors](../connectors/managed.md#on-premises-connectors) die beschikbaar zijn in Azure Logic Apps. Azure Logic Apps ondersteunt lees- en schrijfbewerkingen via de gegevensgateway. Deze bewerkingen hebben echter [limieten voor de grootte van de nettolading.](/data-integration/gateway/service-gateway-onprem#considerations)

In dit artikel wordt beschreven hoe u uw Azure-gatewayresource maakt voor een [eerder geïnstalleerde gateway op uw lokale computer.](../logic-apps/logic-apps-gateway-install.md) Zie Hoe de gateway werkt voor meer informatie [over de gateway.](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)

> [!TIP]
> Als u rechtstreeks toegang wilt krijgen tot on-premises resources in virtuele Azure-netwerken zonder de gateway te gebruiken, kunt u in plaats daarvan een [*integratieserviceomgeving*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) maken. 

Zie de volgende artikelen voor meer informatie over het gebruik van de gateway met andere services:

* [Microsoft Power Automate on-premises gegevensgateway](/power-automate/gateway-reference)
* [Microsoft Power BI on-premises gegevensgateway](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps on-premises gegevensgateway](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services on-premises gegevensgateway](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Ondersteunde gegevensbronnen

In Azure Logic Apps ondersteunt de on-premises gegevensgateway [de on-premises connectors](../connectors/managed.md#on-premises-connectors) voor deze gegevensbronnen:

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

U kunt ook aangepaste [connectors maken die](../logic-apps/custom-connector-overview.md) verbinding maken met gegevensbronnen via HTTP of HTTPS met behulp van REST of SOAP. Hoewel er voor de gateway zelf geen [](../logic-apps/logic-apps-pricing.md) extra kosten in rekening worden Logic Apps, is het Logic Apps van toepassing op deze connectors en andere bewerkingen in Azure Logic Apps.

## <a name="prerequisites"></a>Vereisten

* U hebt [de on-premises gegevensgateway al op een lokale computer geïnstalleerd.](../logic-apps/logic-apps-gateway-install.md) Deze gatewayinstallatie moet bestaan voordat u een gatewayresource kunt maken die is koppelingen naar deze installatie.

* U hebt hetzelfde [Azure-account en -abonnement dat](../logic-apps/logic-apps-gateway-install.md#requirements) u hebt gebruikt voor de installatie van uw gateway. Dit Azure-account mag slechts deel uitmaken van een [enkele Azure Active Directory (Azure AD)-tenant of -map](../active-directory/fundamentals/active-directory-whatis.md#terminology). U moet hetzelfde Azure-account en -abonnement gebruiken om uw gatewayresource in Azure te maken, omdat alleen de gatewaybeheerder de gatewayresource in Azure kan maken. Service-principals worden momenteel niet ondersteund.

  * Wanneer u een gatewayresource in Azure maakt, selecteert u een gatewayinstallatie die u wilt koppelen aan uw gatewayresource en alleen die gatewayresource. Elke gatewayresource kan slechts aan één gatewayinstallatie worden gekoppeld. U kunt geen gatewayinstallatie selecteren die al aan een andere gatewayresource is gekoppeld.

  * Uw logische app en gatewayresource hoeven niet in hetzelfde Azure-abonnement te bestaan. In triggers en acties waar u de gatewayresource kunt gebruiken, kunt u een ander Azure-abonnement met een gatewayresource selecteren, maar alleen als dat abonnement zich in dezelfde Azure AD-tenant of -map als uw logische app bevindt. U moet ook beheerdersmachtigingen hebben voor de gateway, die een andere beheerder voor u kan instellen. Zie Data [Gateway: Automation using PowerShell - Part 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) and [PowerShell: Data Gateway - Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser)voor meer informatie.

    > [!NOTE]
    > Op dit moment kunt u een gatewayresource of installatie niet delen met meerdere abonnementen. Als u productfeedback wilt verzenden, [Microsoft Azure feedbackforum .](https://feedback.azure.com/forums/34192--general-feedback)

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Een Azure-gatewayresource maken

Nadat u de gateway op een lokale computer hebt geïnstalleerd, maakt u de Azure-resource voor uw gateway.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met hetzelfde Azure-account dat is gebruikt om de gateway te installeren.

1. Voer in Azure Portal het zoekvak 'on-premises gegevensgateway' in en selecteer **On-premises gegevensgateways.**

   !['On-premises gegevensgateway' zoeken](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. Selecteer **onder On-premises gegevensgateways** de optie **Toevoegen.**

   ![Nieuwe Azure-resource voor gegevensgateway toevoegen](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. Geef **onder Verbindingsgateway** maken deze informatie op voor uw gatewayresource. Selecteer **Maken** als u klaar bent.

   | Eigenschap | Beschrijving |
   |----------|-------------|
   | **Resourcenaam** | Geef een naam op voor de gatewayresource die alleen letters, cijfers, afbreekstreepingstekens ( `-` ), onderstrepingstekens ( ), haakjes ( , ) of `_` punten `(` `)` () `.` bevat. |
   | **Abonnement** | Selecteer het Azure-abonnement voor het Azure-account dat is gebruikt voor de installatie van de gateway. Het standaardabonnement is gebaseerd op het Azure-account waarmee u zich hebt aanmelden. |
   | **Resourcegroep** | De [Azure-resourcegroep](../azure-resource-manager/management/overview.md) die u wilt gebruiken |
   | **Locatie** | Dezelfde regio of locatie die is geselecteerd voor de gatewaycloudservice tijdens de [installatie van de gateway.](../logic-apps/logic-apps-gateway-install.md) Anders wordt de installatie van de gateway niet weergegeven in de lijst **Installatienaam.** De locatie van uw logische app kan verschillen van de locatie van uw gatewayresource. |
   | **Installatienaam** | Selecteer een gatewayinstallatie die alleen in de lijst wordt weergegeven als aan deze voorwaarden wordt voldaan: <p><p>- Bij de installatie van de gateway wordt dezelfde regio gebruikt als de gatewayresource die u wilt maken. <br>- De installatie van de gateway is niet gekoppeld aan een andere Azure-gatewayresource. <br>- De installatie van de gateway is gekoppeld aan hetzelfde Azure-account dat u gebruikt om de gatewayresource te maken. <br>- Uw Azure-account behoort tot één [Azure Active Directory (Azure AD)-tenant](../active-directory/fundamentals/active-directory-whatis.md#terminology) of -map en is hetzelfde account dat u hebt gebruikt voor de installatie van de gateway. <p><p>Zie de sectie [Veelgestelde vragen voor meer](#faq) informatie. |
   |||

   Hier ziet u een voorbeeld van een gatewayinstallatie in dezelfde regio als uw gatewayresource en die is gekoppeld aan hetzelfde Azure-account:

   ![Geef details op voor het maken van een gegevensgatewayresource](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Verbinding maken met on-premises gegevens

Nadat u uw gatewayresource hebt gemaakt en uw Azure-abonnement aan deze resource hebt koppelen, kunt u nu een verbinding maken tussen uw logische app en uw on-premises gegevensbron met behulp van de gateway.

1. Maak of Azure Portal logische app in de ontwerpfunctie voor logische apps.

1. Voeg een connector toe die ondersteuning biedt voor on-premises verbindingen, bijvoorbeeld **SQL Server**.

1. Selecteer **Verbinding maken via on-premises gegevensgateway.**

1. Selecteer **onder Gateway** in de lijst **Abonnement** uw Azure-abonnement met de gatewayresource die u wilt gebruiken.

   Uw logische app en gatewayresource hoeven niet in hetzelfde Azure-abonnement te bestaan. U kunt een keuze maken uit andere Azure-abonnementen die elk een gatewayresource hebben, maar alleen als deze abonnementen bestaan in dezelfde Azure AD-tenant of -map als uw logische app en u beheerdersmachtigingen hebt voor de gateway, die een andere beheerder voor u kan instellen. Zie Data [Gateway: Automation using PowerShell - Part 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) and [PowerShell: Data Gateway - Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser)voor meer informatie.
  
1. Selecteer in de lijst Verbindingsgateway, waarin de beschikbare gatewayresources in het geselecteerde abonnement worden weergegeven, de gatewayresource die u wilt gebruiken.  Elke gatewayresource is gekoppeld aan één gatewayinstallatie.

   > [!NOTE]
   > De lijst met gateways bevat gatewayresources in andere regio's omdat de locatie van uw logische app kan verschillen van de locatie van uw gatewayresource. 

1. Geef een unieke verbindingsnaam en andere vereiste gegevens op, die afhankelijk zijn van de verbinding die u wilt maken.

   Met een unieke verbindingsnaam kunt u die verbinding later eenvoudig vinden, met name als u meerdere verbindingen maakt. Voeg, indien van toepassing, ook het gekwalificeerde domein voor uw gebruikersnaam toe.

   Hier volgt een voorbeeld:

   ![Verbinding maken tussen logische app en gegevensgateway](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Selecteer **Maken** als u klaar bent.

De gatewayverbinding is nu gereed voor gebruik door uw logische app.

## <a name="edit-connection"></a>Verbinding bewerken

Als u de instellingen voor een gatewayverbinding wilt bijwerken, kunt u de verbinding bewerken.

1. Als u alle API-verbindingen voor alleen uw logische app wilt zoeken, selecteert u API-verbindingen in het menu van uw logische app onder **Ontwikkelhulpprogramma's.**

   ![Selecteer 'API-verbindingen' in het menu van uw logische app](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Selecteer de juiste gatewayverbinding en selecteer vervolgens **API-verbinding bewerken.**

   > [!TIP]
   > Als uw updates niet van kracht zijn, kunt u het [Windows-gatewayserviceaccount](../logic-apps/logic-apps-gateway-install.md#restart-gateway) voor de installatie van de gateway stoppen en opnieuw starten.

Alle API-verbindingen zoeken die zijn gekoppeld aan uw Azure-abonnement:

* Selecteer in Azure Portal menu Alle **services**  >  **Web**  >  **API-verbindingen.**
* Of selecteer in Azure Portal menu Alle **resources.** Stel het **typefilter** in op **API-verbinding.**

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Gatewayresource verwijderen

Als u een andere gatewayresource wilt maken, koppelt u uw gatewayinstallatie aan een andere gatewayresource of verwijdert u de gatewayresource. U kunt de gatewayresource verwijderen zonder dat dit van invloed is op de installatie van de gateway.

1. Selecteer in Azure Portal menu Alle **resources** of zoek en selecteer **Alle resources** op een pagina. Zoek en selecteer uw gatewayresource.

1. Als dit nog niet is geselecteerd, selecteert u **on-premises gegevensgateway** in het menu van de gatewayresource. Selecteer Verwijderen op de werkbalk van de **gatewayresource.**

   Bijvoorbeeld:

   ![Gatewayresource verwijderen in Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V:** Waarom wordt de installatie van mijn gateway niet weergegeven wanneer ik mijn gatewayresource in Azure maak? <br/>
**A:** Dit probleem kan zich om de volgende redenen voor doen:

* Uw Azure-account is niet hetzelfde account dat u hebt gebruikt voor de installatie van de gateway op uw lokale computer. Controleer of u zich hebt aangemeld bij Azure Portal met dezelfde identiteit die u hebt gebruikt voor de installatie van de gateway. Alleen de gatewaybeheerder kan de gatewayresource maken in Azure. Service-principals worden momenteel niet ondersteund.

* Uw Azure-account behoort niet tot slechts één [Azure AD-tenant of -directory.](../active-directory/fundamentals/active-directory-whatis.md#terminology) Controleer of u dezelfde Azure AD-tenant of -map gebruikt die u hebt gebruikt tijdens de installatie van de gateway.

* Uw gatewayresource en gatewayinstallatie bestaan niet in dezelfde regio. De locatie van uw logische app kan echter verschillen van de locatie van uw gatewayresource.

* De installatie van uw gateway is al gekoppeld aan een andere gatewayresource. Elke gatewayresource kan slechts aan één gatewayinstallatie worden gekoppeld, die kan worden gekoppeld aan slechts één Azure-account en -abonnement. U kunt dus geen gatewayinstallatie selecteren die al aan een andere gatewayresource is gekoppeld. Deze installaties worden niet weergegeven in de lijst **Installatienaam.**

  Als u uw gatewayregistraties in de Azure Portal, gaat u naar al uw Azure-resources met het resourcetype On-premises gegevensgateways *in* al uw **Azure-abonnementen.** Zie Gatewayresource verwijderen als u de installatie van de gateway wilt loskoppelen van de [andere gatewayresource.](#change-delete-gateway-resource)

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Volgende stappen

* [Uw logische apps beveiligen](./logic-apps-securing-a-logic-app.md)
* [Algemene voorbeelden en scenario's voor logische apps](./logic-apps-examples-and-scenarios.md)
