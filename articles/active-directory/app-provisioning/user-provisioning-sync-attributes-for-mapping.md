---
title: Kenmerken synchroniseren met Azure Active Directory voor toewijzing
description: Bij het configureren van gebruikers inrichten met Azure Active Directory en SaaS-apps, gebruikt u de functie Directory-extensie om bron kenmerken toe te voegen die niet standaard worden gesynchroniseerd.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2021
ms.author: kenwith
ms.openlocfilehash: 102c0f7363b8d4f635762a33b82825e9ae71dfc6
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120789"
---
# <a name="syncing-extension-attributes-for-app-provisioning"></a>Extensie kenmerken voor het inrichten van apps synchroniseren

Azure Active Directory (Azure AD) moet alle gegevens (kenmerken) bevatten die nodig zijn voor het maken van een gebruikers profiel bij het inrichten van gebruikers accounts van Azure AD naar een [SaaS-app](../saas-apps/tutorial-list.md). Bij het aanpassen van kenmerk toewijzingen voor het inrichten van gebruikers, kan het kenmerk dat u wilt toewijzen, niet worden weer gegeven in de lijst **bron kenmerk** . In dit artikel leest u hoe u het ontbrekende kenmerk kunt toevoegen.

Alleen voor gebruikers in azure AD kunt u [schema-uitbrei dingen maken met behulp van Power shell of Microsoft Graph](#create-an-extension-attribute-on-a-cloud-only-user).

Voor gebruikers in een on-premises Active Directory moet u de gebruikers synchroniseren met Azure AD. U kunt gebruikers en kenmerken synchroniseren met [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md). Azure AD Connect worden automatisch bepaalde kenmerken gesynchroniseerd met Azure AD, maar niet alle kenmerken. Bovendien worden sommige kenmerken (zoals SAMAccountName) die standaard worden gesynchroniseerd, mogelijk niet weer gegeven met de Azure AD-Graph API. In dergelijke gevallen kunt u [de functie Directory-extensie Azure AD Connect gebruiken om het kenmerk te synchroniseren met Azure AD](#create-an-extension-attribute-using-azure-ad-connect). Op die manier is het kenmerk zichtbaar voor de Azure AD-Graph API en de Azure AD-inrichtings service.

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Een uitbreidings kenmerk maken op een alleen Cloud gebruiker
U kunt Microsoft Graph en Power shell gebruiken om het gebruikers schema uit te breiden voor gebruikers in azure AD. Deze extensie kenmerken worden in de meeste gevallen automatisch gedetecteerd.

Wanneer u meer dan 1000 service-principals hebt, kunt u de uitbrei dingen die ontbreken in de lijst met bron kenmerken vinden. Als een kenmerk dat u hebt gemaakt, niet automatisch wordt weer gegeven, controleert u of het kenmerk is gemaakt en voegt u het hand matig toe aan het schema. Gebruik Microsoft Graph en [Graph Explorer](/graph/graph-explorer/graph-explorer-overview.md)om te controleren of deze is gemaakt. Zie [de lijst met ondersteunde kenmerken bewerken](customize-application-attributes.md#editing-the-list-of-supported-attributes)als u deze hand matig aan uw schema wilt toevoegen.

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Een uitbreidings kenmerk maken op een alleen Cloud gebruiker die gebruikmaakt van Microsoft Graph
U kunt het schema van Azure AD-gebruikers uitbreiden met behulp van [Microsoft Graph](/graph/overview.md). 

Eerst vermeldt u de apps in uw Tenant om de ID op te halen van de app waaraan u werkt. Zie [List extensionProperties](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true)voor meer informatie.

```json
GET https://graph.microsoft.com/v1.0/applications
```

Maak vervolgens het uitbreidings kenmerk. Vervang de eigenschap **id** hieronder door de **id** die u in de vorige stap hebt opgehaald. U moet het kenmerk **' id '** en niet de ' AppID ' gebruiken. Zie [Create extensionProperty]/Graph/API/Application-post-extensionproperty.MD? View = Graph-rest-1.0&tabs = http&pres Erve-View = True) voor meer informatie.

```json
POST https://graph.microsoft.com/v1.0/applications/{id}/extensionProperties
Content-type: application/json

{
    "name": "extensionName",
    "dataType": "string",
    "targetObjects": [
        "User"
    ]
}
```

De vorige aanvraag heeft een uitbreidings kenmerk met de indeling gemaakt `extension_appID_extensionName` . U kunt nu een gebruiker met dit extensie kenmerk bijwerken. Zie [gebruiker bijwerken](/graph/api/user-update.md?view=graph-rest-1.0&tabs=http&preserve-view=true)voor meer informatie.
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Controleer ten slotte het kenmerk voor de gebruiker. Zie [een gebruiker ophalen](/graph/api/user-get.md?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true)voor meer informatie.

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>Een uitbreidings kenmerk maken op een alleen Cloud gebruiker die gebruikmaakt van Power shell
Een aangepaste extensie maken met behulp van Power shell en een waarde toewijzen aan een gebruiker. 

```
#Connect to your Azure AD tenant   
Connect-AzureAD

#Create an application (you can instead use an existing application if you would like)
$App = New-AzureADApplication -DisplayName “test app name” -IdentifierUris https://testapp

#Create a service principal
New-AzureADServicePrincipal -AppId $App.AppId

#Create an extension property
New-AzureADApplicationExtensionProperty -ObjectId $App.ObjectId -Name “TestAttributeName” -DataType “String” -TargetObjects “User”

#List users in your tenant to determine the objectid for your user
Get-AzureADUser

#Set a value for the extension property on the user. Replace the objectid with the ID of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Een uitbreidings kenmerk maken met behulp van Azure AD Connect

1. Open de wizard Azure AD Connect, kies taken en kies vervolgens **synchronisatie opties aanpassen**.

   ![Pagina extra taken Azure Active Directory Connect wizard](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Meld u aan als een globale Azure AD-beheerder. 

3. Selecteer op de pagina **optionele functies** de optie **synchronisatie van Directory-extensie kenmerken**.
 
   ![Pagina optionele functies van Azure Active Directory Connect wizard](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Selecteer de kenmerk (en) die u wilt uitbreiden naar Azure AD.
   > [!NOTE]
   > De zoek opdracht onder **beschik bare kenmerken** is hoofdletter gevoelig.

   ![Scherm afbeelding van de selectie pagina Directory-extensies](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Voltooi de Azure AD Connect wizard en sta toe dat een volledige synchronisatie cyclus kan worden uitgevoerd. Wanneer de cyclus is voltooid, wordt het schema uitgebreid en worden de nieuwe waarden gesynchroniseerd tussen uw on-premises AD en Azure AD.
 
6. In de Azure Portal, terwijl u de [toewijzingen van gebruikers kenmerken bewerkt](customize-application-attributes.md), bevat de lijst met **bron kenmerken** nu het kenmerk toegevoegd in de indeling `<attributename> (extension_<appID>_<attributename>)` . Selecteer het kenmerk en wijs dit toe aan de doel toepassing voor inrichting.

   ![Pagina Azure Active Directory Connect Directory uitbreidingen selecteren](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> De mogelijkheid om referentie kenmerken in te richten vanuit een on-premises AD, zoals **managedby** of **DN/DN**, wordt momenteel niet ondersteund. U kunt deze functie aanvragen op de [gebruikers stem](https://feedback.azure.com/forums/169401-azure-active-directory). 


## <a name="next-steps"></a>Volgende stappen

* [Definiëren wie binnen het bereik van de inrichting valt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
