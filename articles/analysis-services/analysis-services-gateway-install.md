---
title: On-premises gegevens gateway installeren voor Azure Analysis Services | Microsoft Docs
description: Meer informatie over het installeren en configureren van een on-premises gegevens gateway om verbinding te maken met on-premises gegevens bronnen van een Azure Analysis Services-server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 64bd9e4a4cf78d2628e946af30c2d290ff002cf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93081141"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Een on-premises gegevensgateway installeren en configureren

Een on-premises gegevens gateway is vereist wanneer een of meer Azure Analysis Services servers in dezelfde regio verbinding maken met on-premises gegevens bronnen.  De gateway die u installeert, is hetzelfde als die wordt gebruikt door andere services, zoals Power BI, Power apps en Logic Apps, wanneer u voor Azure Analysis Services installeert, zijn er enkele extra stappen die u moet volt ooien. Dit installatie artikel is specifiek voor **Azure Analysis Services**. 

Zie [verbinding maken met on-premises gegevens bronnen](analysis-services-gateway.md)voor meer informatie over de werking van Azure Analysis Services met de gateway. Zie de [documentatie over on-premises gegevens gateways](/data-integration/gateway/service-gateway-onprem)voor meer informatie over geavanceerde installatie scenario's en de gateway in het algemeen.

## <a name="prerequisites"></a>Vereisten

**Minimale vereisten:**

* .NET 4.5 Framework
* 64-bits versie van Windows 8/Windows Server 2012 R2 (of hoger)

**Aanbevelingen**

* 8-core CPU
* 8 GB geheugen
* 64-bits versie van Windows 8/Windows Server 2012 R2 (of hoger)

**Belang rijke overwegingen:**

* Tijdens de installatie wordt de standaard regio voor uw abonnement geselecteerd wanneer u uw gateway registreert bij Azure. U kunt een ander abonnement en een andere regio kiezen. Als u servers in meer dan één regio hebt, moet u een gateway voor elke regio installeren. 
* De gateway kan niet worden geïnstalleerd op een domein controller.
* Er kan slechts één gateway op één computer worden geïnstalleerd.
* Installeer de gateway op een computer die nog moet blijven en gaat niet naar de slaap stand.
* Installeer de gateway niet op een computer met een draadloze verbinding met uw netwerk. Prestaties kunnen worden verminderd.
* Bij de installatie van de gateway moet het gebruikers account waarmee u bent aangemeld bij uw computer, aanmelden als service bevoegdheden hebben. Wanneer de installatie is voltooid, maakt de on-premises gegevens Gateway-Service gebruik van het NT SERVICE\PBIEgwService-account om u aan te melden als een service. U kunt een ander account opgeven tijdens de installatie of in Services nadat de installatie is voltooid. Zorg ervoor dat met groepsbeleid instellingen zowel het account waarmee u bent aangemeld bij het installeren en het service account dat u kiest aanmelden als service privileges.
* Meld u aan bij Azure met een account in azure AD voor dezelfde [Tenant](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) als het abonnement waarmee u de gateway wilt registreren. Accounts voor Azure B2B (gast) worden niet ondersteund bij het installeren en registreren van een gateway.
* Als gegevens bronnen zich op een Azure Virtual Network (VNet) bevinden, moet u de eigenschap [AlwaysUseGateway](analysis-services-vnet-gateway.md) van de server configureren.

## <a name="download"></a>Downloaden

 [De gateway downloaden](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Installeren

1. Voer Setup uit.

2. Selecteer **on-premises gegevens gateway**.

   ![Selecteer](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Selecteer een locatie, accepteer de voor waarden en klik vervolgens op **installeren**.

   ![Installatie locatie en licentie voorwaarden](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Meld u aan bij Azure. Het account moet in de Azure Active Directory van uw Tenant staan. Dit account wordt gebruikt voor de Gateway beheerder. Accounts van Azure B2B (gast) worden niet ondersteund bij het installeren en registreren van de gateway.

   ![Aanmelden bij Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Als u zich aanmeldt met een domein account, wordt deze toegewezen aan uw organisatie-account in azure AD. Uw organisatie account wordt gebruikt als gateway beheerder.

## <a name="register"></a>Registreren

Als u een gateway bron in azure wilt maken, moet u het lokale exemplaar dat u hebt geïnstalleerd met de gateway-Cloud service registreren. 

1.  Selecteer **een nieuwe gateway registreren op deze computer**.

    ![Scherm afbeelding die de optie een nieuwe gateway registreren op deze computer markeert.](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Typ een naam en herstel sleutel voor uw gateway. De gateway maakt standaard gebruik van de standaard regio van uw abonnement. Als u een andere regio wilt selecteren, selecteert u **regio wijzigen**.

    > [!IMPORTANT]
    > Sla uw herstel sleutel op een veilige plaats op. De herstel sleutel is vereist in-volg orde voor overname, migratie of herstel van een gateway. 

   ![Registreren](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a>Een Azure gateway-resource maken

Nadat u de gateway hebt geïnstalleerd en geregistreerd, moet u een gateway bron maken in Azure. Meld u aan bij Azure met hetzelfde account dat u hebt gebruikt bij het registreren van de gateway.

1. In Azure Portal klikt u op **een resource maken**, zoekt u naar de **on-premises gegevens gateway** en klikt u vervolgens op **maken**.

   ![Een gateway resource maken](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. Voer bij **verbindings gateway maken** de volgende instellingen in:

   * **Naam**: Voer een naam in voor de gateway bron. 

   * **Abonnement**: Selecteer het Azure-abonnement dat u wilt koppelen aan uw gateway resource. 
   
     Het standaard abonnement is gebaseerd op het Azure-account dat u hebt gebruikt om u aan te melden.

   * **Resourcegroep**: maak een resourcegroep of selecteer een bestaande resourcegroep.

   * **Locatie**: Selecteer de regio waarin u uw gateway hebt geregistreerd.

   * **Installatie naam**: als de gateway-installatie nog niet is geselecteerd, selecteert u de gateway die u op uw computer hebt geïnstalleerd en geregistreerd. 

     Wanneer u klaar bent, klikt u op **maken**.

## <a name="connect-gateway-resource-to-server"></a>Gateway bron verbinden met server

> [!NOTE]
> Het is niet mogelijk om verbinding te maken met een gateway bron in een ander abonnement op uw server, maar deze wordt wel ondersteund in de portal.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Klik in het overzicht van Azure Analysis Services server op **on-premises gegevens gateway**.

   ![Server verbinden met gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. Selecteer in **een on-premises gegevens gateway kiezen om verbinding te maken** de gateway bron en klik vervolgens op **geselecteerde gateway verbinden**.

   ![Verbinding maken tussen server en gateway resource](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Als uw gateway niet in de lijst wordt weer gegeven, is uw server waarschijnlijk niet in dezelfde regio als de regio die u hebt opgegeven bij het registreren van de gateway.

    Wanneer de verbinding tussen de server en de gateway bron is geslaagd, wordt de status **verbonden** weer gegeven.


    ![De verbinding tussen de server en de gateway bron is geslaagd](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik [Get-AzResource](/powershell/module/az.resources/get-azresource) om de gateway ResourceID op te halen. Verbind vervolgens de gateway bron met een bestaande of nieuwe server door **GatewayResourceID** in [set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) of [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver)op te geven.

De resource-ID van de gateway ophalen:

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

Dat is alles. Als u poorten moet openen of problemen wilt oplossen, moet u ervoor zorgen dat u de [on-premises gegevens gateway](analysis-services-gateway.md)bekijkt.

## <a name="next-steps"></a>Volgende stappen

* [Analysis Services beheren](analysis-services-manage.md)   
* [Gegevens ophalen uit Azure Analysis Services](analysis-services-connect.md)   
* [Gateway gebruiken voor gegevensbronnen in een virtueel Azure-netwerk](analysis-services-vnet-gateway.md)