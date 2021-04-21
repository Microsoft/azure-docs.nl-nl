---
title: Automatiseer Azure Analysis Services taken met service-principals | Microsoft Docs
description: Meer informatie over het maken van een service-principal voor het automatiseren Azure Analysis Services beheertaken.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 04bc6ecd6e0a32e9234d07e995a7e012b17e3bbe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769153"
---
# <a name="automation-with-service-principals"></a>Automatisering met service-principals

Service-principals zijn een Azure Active Directory-toepassingsresource die u in uw tenant maakt om onbeheerde bewerkingen op resource- en serviceniveau uit te voeren. Ze zijn een uniek type *gebruikers-id met* een toepassings-id en wachtwoord of certificaat. Een service-principal heeft alleen de machtigingen die nodig zijn om taken uit te voeren die zijn gedefinieerd door de rollen en machtigingen waaraan deze is toegewezen. 

In Analysis Services worden service-principals gebruikt met Azure Automation, powershell-modus zonder toezicht, aangepaste clienttoepassingen en web-apps om algemene taken te automatiseren. Het inrichten van servers, het implementeren van modellen, het vernieuwen van gegevens, omhoog/omlaag schalen en onderbreken/hervatten kunnen bijvoorbeeld allemaal worden geautomatiseerd met behulp van service-principals. Machtigingen worden toegewezen aan service-principals via rollidmaatschap, net als gewone Azure AD UPN-accounts.

Analysis Services ondersteunt ook bewerkingen die worden uitgevoerd door beheerde identiteiten met behulp van service-principals. Zie Beheerde identiteiten [voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) en Azure-services die ondersteuning bieden voor [Azure AD-verificatie voor meer informatie.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services)    

## <a name="create-service-principals"></a>Service-principals maken
 
Service-principals kunnen worden gemaakt in de Azure Portal of met behulp van PowerShell. Raadpleeg voor meer informatie:

[Service-principal maken - Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Service-principal maken - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Referenties en certificaatactiva opslaan in Azure Automation

Referenties en certificaten van de service-principal kunnen veilig worden opgeslagen in Azure Automation runbookbewerkingen. Raadpleeg voor meer informatie:

[Referentie-assets in Azure Automation](../automation/shared-resources/credentials.md)   
[Verbindingsassets in Azure Automation](../automation/shared-resources/certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Service-principals toevoegen aan serverbeheerdersrol

Voordat u een service-principal kunt gebruiken Analysis Services serverbeheerbewerkingen, moet u deze toevoegen aan de rol serverbeheerders. Service-principals moeten rechtstreeks worden toegevoegd aan de serverbeheerdersrol. Het toevoegen van een service-principal aan een beveiligingsgroep en vervolgens het toevoegen van die beveiligingsgroep aan de serverbeheerdersrol wordt niet ondersteund. Zie Een service-principal toevoegen aan de [serverbeheerdersrol voor meer informatie.](analysis-services-addservprinc-admins.md)

## <a name="service-principals-in-connection-strings"></a>Service-principals in verbindingsreeksen

AppID en wachtwoord of certificaat van de service-principal kunnen in verbindingsreeksen worden gebruikt die vrijwel hetzelfde zijn als een UPN.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule"></a>Module Az.AnalysisServices gebruiken

Wanneer u een service-principal gebruikt voor resourcebeheerbewerkingen met de module [Az.AnalysisServices,](/powershell/module/az.analysisservices)  gebruikt `Connect-AzAccount` u de cmdlet . 

In het volgende voorbeeld worden appID en een wachtwoord gebruikt om besturingsvlakbewerkingen uit te voeren voor synchronisatie met alleen-lezen replica's en omhoog/uit te schalen:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force
$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

# Connect using Az module
Connect-AzAccount -Credential $Credential -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx"

# Syncronize a database for query scale out
Sync-AzAnalysisServicesInstance -Instance "asazure://westus.asazure.windows.net/testsvr" -Database "testdb"

# Scale up the server to an S1, set 2 read-only replicas, and remove the primary from the query pool. The new replicas will hydrate from the synchronized data.
Set-AzAnalysisServicesServer -Name "testsvr" -ResourceGroupName "testRG" -Sku "S1" -ReadonlyReplicaCount 2 -DefaultConnectionMode Readonly
```

#### <a name="using-sqlserver-module"></a>De module SQLServer gebruiken

In het volgende voorbeeld worden appID en een wachtwoord gebruikt om een bewerking voor het vernieuwen van een modeldatabase uit te voeren:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full" -ServicePrincipal -ApplicationId $AppId -TenantId $TenantId -Credential $Credential
```

### <a name="amo-and-adomd"></a>AMO en ADOMD 

Wanneer u verbinding maakt met clienttoepassingen en web-apps, bieden [AMO- en ADOMD-clientbibliotheken](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) versie 15.0.2 en hogere versies van installeerbare pakketten van NuGet, ondersteuning voor het gebruik van service-principals in verbindingsreeksen met de volgende syntaxis: `app:AppID` en wachtwoord of `cert:thumbprint`. 

In het volgende voorbeeld worden `appID` en een `password` gebruikt voor het uitvoeren van een bewerking voor het vernieuwen van een modeldatabase:

```csharp
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>Volgende stappen
[Aanmelden met Azure PowerShell](/powershell/azure/authenticate-azureps)   
[Vernieuwen met Logic Apps](analysis-services-refresh-logic-app.md)  
[Vernieuwen met Azure Automation](analysis-services-refresh-azure-automation.md)  
[Een service-principal toevoegen aan de serverbeheerdersrol](analysis-services-addservprinc-admins.md)  
[Taken Power BI Premium werkruimten en gegevenssets automatiseren met service-principals](/power-bi/admin/service-premium-service-principal)
