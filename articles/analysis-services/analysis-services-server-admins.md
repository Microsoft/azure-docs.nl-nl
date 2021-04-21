---
title: Serverbeheerders beheren in Azure Analysis Services | Microsoft Docs
description: In dit artikel wordt beschreven hoe u serverbeheerders voor een Azure Analysis Services-server beheert met behulp van de Azure Portal-, PowerShell- of REST-API's.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 2/4/2021
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c0986b8508a7e21dee7c7c87e535eb2726944de8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769189"
---
# <a name="manage-server-administrators"></a>Serverbeheerders beheren

Serverbeheerders moeten een geldige gebruiker, service-principal of beveiligingsgroep in de Azure Active Directory (Azure AD) zijn voor de tenant waarin de server zich bevindt. U kunt **Analysis Services-beheerders** voor uw server gebruiken in Azure Portal, Servereigenschappen in SSMS, PowerShell of REST API om serverbeheerders te beheren. 

Wanneer u een **beveiligingsgroep toevoegt,** gebruikt u `obj:groupid@tenantid` . Service-principals worden niet ondersteund in beveiligingsgroepen die zijn toegevoegd aan de serverbeheerdersrol.

Zie Een service-principal toevoegen aan de serverbeheerdersrol voor meer informatie over het toevoegen van een service-principal aan de [serverbeheerdersrol.](analysis-services-addservprinc-admins.md)

Als de serverfirewall is ingeschakeld, moeten de IP-adressen van de clientcomputer met serverbeheerder worden opgenomen in een firewallregel. Zie Serverfirewall [configureren voor meer informatie.](analysis-services-qs-firewall.md)

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Serverbeheerders toevoegen met behulp van Azure Portal

1. Klik in de portal voor uw server op **Analysis Services Beheerders.**
2. Klik **\<servername> in - Analysis Services beheerders** op **Toevoegen.**
3. Selecteer **in Serverbeheerders toevoegen gebruikersaccounts** uit uw Azure AD of nodig externe gebruikers via e-mailadres uit.

    ![Serverbeheerders in Azure Portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Serverbeheerders toevoegen met behulp van SSMS

1. Klik met de rechtermuisknop op de server > **Eigenschappen**.
2. Klik **Analyseserver eigenschappen op** **Beveiliging.**
3. Klik **op Toevoegen** en voer vervolgens het e-mailadres voor een gebruiker of groep in uw Azure AD in.
   
    ![Serverbeheerders toevoegen in SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Gebruik [de cmdlet New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) om de parameter Administrator op te geven bij het maken van een nieuwe server. <br>
Gebruik [de cmdlet Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) om de parameter Administrator voor een bestaande server te wijzigen.

## <a name="rest-api"></a>REST-API

Gebruik [Maken om](/rest/api/analysisservices/servers/create) de eigenschap asAdministrator op te geven bij het maken van een nieuwe server. <br>
Gebruik [Update om](/rest/api/analysisservices/servers/update) de eigenschap asAdministrator op te geven bij het wijzigen van een bestaande server. <br>



## <a name="next-steps"></a>Volgende stappen 

[Verificatie en gebruikersmachtigingen](analysis-services-manage-users.md)  
[Databaserollen en -gebruikers beheren](analysis-services-database-users.md)  
[Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)](../role-based-access-control/overview.md)
