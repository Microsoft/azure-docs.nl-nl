---
title: On-premises gegevensgateway installeren voor Azure Analysis Services | Microsoft Docs
description: Leer hoe u een on-premises gegevensgateway installeert en configureert om verbinding te maken met on-premises gegevensbronnen vanaf een Azure Analysis Services server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 43e5b64d06a6ec145876798b2e0da6499ab94bfc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769225"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Een on-premises gegevensgateway installeren en configureren

Een on-premises gegevensgateway is vereist wanneer een of meer Azure Analysis Services servers in dezelfde regio verbinding maken met on-premises gegevensbronnen.  Hoewel de gateway die u installeert hetzelfde is als die wordt gebruikt door andere services, zoals Power BI, Power Apps en Logic Apps, moet u bij de installatie voor Azure Analysis Services enkele extra stappen voltooien. Dit installatieartikel is specifiek voor **Azure Analysis Services.** 

Zie Verbinding maken met [on-premises](analysis-services-gateway.md)gegevensbronnen voor meer informatie over hoe Azure Analysis Services met de gateway werkt. Zie documentatie over [on-premises](/data-integration/gateway/service-gateway-onprem)gegevensgateways voor meer informatie over geavanceerde installatiescenario's en de gateway in het algemeen.

## <a name="prerequisites"></a>Vereisten

**Minimumeisen:**

* .NET 4.5 Framework
* 64-bits versie van Windows 8 /Windows Server 2012 R2 (of hoger)

**Aanbevolen:**

* 8-core CPU
* 8 GB geheugen
* 64-bits versie van Windows 8 /Windows Server 2012 R2 (of hoger)

**Belangrijke overwegingen:**

* Tijdens de installatie wordt bij het registreren van uw gateway bij Azure de standaardregio voor uw abonnement geselecteerd. U kunt een ander abonnement en andere regio kiezen. Als u servers in meer dan één regio hebt, moet u voor elke regio een gateway installeren. 
* De gateway kan niet worden geïnstalleerd op een domeincontroller.
* Er kan slechts één gateway op één computer worden geïnstalleerd.
* Installeer de gateway op een computer die blijft staan en niet in de slaapstand gaat.
* Installeer de gateway niet op een computer met een draadloze alleen-verbinding met uw netwerk. De prestaties kunnen afnemen.
* Wanneer u de gateway installeert, moet het gebruikersaccount met wie u bent aangemeld bij uw computer, beschikken over de machtigingEn voor aanmelden als service. Wanneer de installatie is voltooid, gebruikt de on-premises gegevensgatewayservice het NT SERVICE\PBIEgwService-account om zich aan te melden als een service. Er kan een ander account worden opgegeven tijdens de installatie of in Services nadat de installatie is voltooid. Zorg groepsbeleid toestaan dat zowel het account waarmee u bent aangemeld bij de installatie als het serviceaccount dat u kiest, de machtiging Aanmelden als service heeft.
* Meld u aan bij Azure met een account in Azure AD voor dezelfde [tenant](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) als het abonnement waarin u de gateway registreert. Azure B2B-accounts (gastaccounts) worden niet ondersteund bij het installeren en registreren van een gateway.
* Als gegevensbronnen zich in een Azure Virtual Network (VNet) hebben, moet u de [eigenschap AlwaysUseGateway-server configureren.](analysis-services-vnet-gateway.md)

## <a name="download"></a>Downloaden

 [De gateway downloaden](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Installeren

1. Voer setup uit.

2. Selecteer **On-premises gegevensgateway.**

   ![Selecteer](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Selecteer een locatie, accepteer de voorwaarden en klik vervolgens op **Installeren.**

   ![Locatie en licentievoorwaarden installeren](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Meld u aan bij Azure. Het account moet zich in het account van uw tenant Azure Active Directory. Dit account wordt gebruikt voor de gatewaybeheerder. Azure B2B-accounts (gastaccounts) worden niet ondersteund bij het installeren en registreren van de gateway.

   ![Aanmelden bij Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Als u zich met een domeinaccount aanmelden, wordt dit aan uw organisatieaccount in Azure AD. Uw organisatieaccount wordt gebruikt als gatewaybeheerder.

## <a name="register"></a>Registreren

Als u een gatewayresource in Azure wilt maken, moet u het lokale exemplaar registreren dat u hebt geïnstalleerd bij de Gateway Cloud Service. 

1.  Selecteer **Een nieuwe gateway registreren op deze computer.**

    ![Schermopname met de optie Een nieuwe gateway registreren op deze computer.](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Typ een naam en herstelsleutel voor uw gateway. De gateway maakt standaard gebruik van de standaardregio van uw abonnement. Als u een andere regio wilt selecteren, selecteert u **Regio wijzigen.**

    > [!IMPORTANT]
    > Sla uw herstelsleutel op een veilige plaats op. De herstelsleutel is vereist om een gateway te kunnen overnemen, migreren of herstellen. 

   ![Registreren](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a>Een Azure-gatewayresource maken

Nadat u uw gateway hebt geïnstalleerd en geregistreerd, moet u een gatewayresource maken in Azure. Meld u aan bij Azure met hetzelfde account dat u hebt gebruikt bij het registreren van de gateway.

1. Klik Azure Portal op **Een resource maken,** zoek naar **On-premises gegevensgateway** en klik vervolgens op **Maken.**

   ![Een gatewayresource maken](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. Voer **in Verbindingsgateway maken** de volgende instellingen in:

   * **Naam:** voer een naam in voor de gatewayresource. 

   * **Abonnement:** selecteer het Azure-abonnement dat u aan uw gatewayresource wilt koppelen. 
   
     Het standaardabonnement is gebaseerd op het Azure-account waarmee u zich hebt aanmelden.

   * **Resourcegroep**: maak een resourcegroep of selecteer een bestaande resourcegroep.

   * **Locatie:** selecteer de regio waarin u uw gateway hebt geregistreerd.

   * **Installatienaam:** als de installatie van de gateway nog niet is geselecteerd, selecteert u de gateway die u op uw computer hebt geïnstalleerd en die u hebt geregistreerd. 

     Wanneer u klaar bent, klikt u op **Maken.**

## <a name="connect-gateway-resource-to-server"></a>Gatewayresource verbinden met server

> [!NOTE]
> Verbinding maken met een gatewayresource in een ander abonnement van uw server wordt niet ondersteund in de portal, maar wordt ondersteund met behulp van PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Klik in Azure Analysis Services serveroverzicht op **On-premises gegevensgateway.**

   ![Server verbinden met gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. Selecteer **in Kies een on-premises gegevensgateway om verbinding te maken** uw gatewayresource en klik vervolgens op Geselecteerde gateway **verbinden.**

   ![Server verbinden met gatewayresource](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Als uw gateway niet wordt weergegeven in de lijst, is de server waarschijnlijk niet in dezelfde regio als de regio die u hebt opgegeven bij het registreren van de gateway.

    Wanneer de verbinding tussen de server en de gatewayresource tot stand is gebracht, wordt de status **Verbonden weergeven.**


    ![Server verbinden met gatewayresource geslaagd](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik [Get-AzResource om](/powershell/module/az.resources/get-azresource) de resource-id van de gateway op te halen. Verbind vervolgens de gatewayresource met een bestaande of nieuwe server door **-GatewayResourceID** op te geven in [Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) of [New-AzAnalysisServicesServer.](/powershell/module/az.analysisservices/new-azanalysisservicesserver)

De resource-id van de gateway op te halen:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforGateway -Environment "AzureCloud"
$GatewayResourceId = $(Get-AzResource -ResourceType "Microsoft.Web/connectionGateways" -Name $gatewayName).ResourceId  

```

Een bestaande server configureren:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforAzureAS -Environment "AzureCloud"
Set-AzAnalysisServicesServer -ResourceGroupName $RGName -Name $servername -GatewayResourceId $GatewayResourceId

```
---

Dat is alles. Als u poorten moet openen of problemen wilt oplossen, raadpleegt u [On-premises gegevensgateway.](analysis-services-gateway.md)

## <a name="next-steps"></a>Volgende stappen

* [Analysis Services beheren](analysis-services-manage.md)   
* [Gegevens ophalen uit Azure Analysis Services](analysis-services-connect.md)   
* [Gateway gebruiken voor gegevensbronnen in een virtueel Azure-netwerk](analysis-services-vnet-gateway.md)
