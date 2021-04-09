---
title: Kenmerken synchroniseren met Azure AD voor toewijzing
description: Bij het configureren van gebruikers inrichting voor SaaS-apps, gebruikt u de functie Directory-extensie om bron kenmerken toe te voegen die niet standaard worden gesynchroniseerd.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/17/2021
ms.author: kenwith
ms.openlocfilehash: 52f34cdafac76a9bca2b4ff0b00e0b3efaa63f5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579430"
---
# <a name="syncing-extension-attributes-attributes"></a>Kenmerken van extensie kenmerken synchroniseren

Bij het aanpassen van kenmerk toewijzingen voor het inrichten van gebruikers, is het mogelijk dat het kenmerk dat u wilt toewijzen niet wordt weer gegeven in de lijst met **bron kenmerken** . Dit artikel laat u zien hoe u het ontbrekende kenmerk kunt toevoegen door het te synchroniseren van uw on-premises Active Directory (AD) naar Azure Active Directory (Azure AD) of door de extensie kenmerken in azure AD te maken voor een alleen-Cloud gebruiker. 

Azure AD moet alle gegevens bevatten die nodig zijn voor het maken van een gebruikers profiel bij het inrichten van gebruikers accounts van Azure AD naar een SaaS-app. In sommige gevallen kan het nodig zijn om de gegevens te synchroniseren van uw on-premises AD naar Azure AD. Azure AD Connect worden automatisch bepaalde kenmerken gesynchroniseerd met Azure AD, maar niet alle kenmerken. Bovendien worden sommige kenmerken (zoals SAMAccountName) die standaard worden gesynchroniseerd, mogelijk niet weer gegeven met de Azure AD-Graph API. In dergelijke gevallen kunt u de functie Directory-extensie Azure AD Connect gebruiken om het kenmerk te synchroniseren met Azure AD. Op die manier is het kenmerk zichtbaar voor de Azure AD-Graph API en de Azure AD-inrichtings service. Als de gegevens die u nodig hebt voor het inrichten zich in Active Directory bevinden, maar niet beschikbaar is voor inrichting vanwege de bovenstaande redenen, kunt u Azure AD Connect gebruiken om extensie kenmerken te maken. 

Hoewel de meeste gebruikers waarschijnlijk hybride gebruikers zijn die zijn gesynchroniseerd vanaf Active Directory, kunt u ook uitbrei dingen maken voor Cloud gebruikers zonder Azure AD Connect te gebruiken. Met Power shell of Microsoft Graph kunt u het schema van een Cloud gebruiker alleen uitbreiden. 

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

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Een uitbreidings kenmerk maken op een alleen Cloud gebruiker
Klanten kunnen Microsoft Graph en Power shell gebruiken om het gebruikers schema uit te breiden. Deze extensie kenmerken worden in de meeste gevallen automatisch gedetecteerd, maar klanten met meer dan 1000 service-principals kunnen uitbrei dingen vinden die ontbreken in de lijst met bron kenmerken. Als een kenmerk dat u maakt met behulp van de onderstaande stappen niet automatisch wordt weer gegeven in de lijst Bron kenmerk, controleert u of het kenmerk extension is gemaakt met behulp van Graph en voegt u dit [hand matig](https://docs.microsoft.com/azure/active-directory/app-provisioning/customize-application-attributes#editing-the-list-of-supported-attributes)toe aan het schema. Wanneer u de onderstaande grafiek aanvragen maakt, klikt u op meer informatie om de vereiste machtigingen voor het indienen van de aanvragen te controleren. U kunt [Graph Explorer](https://docs.microsoft.com/graph/graph-explorer/graph-explorer-overview) gebruiken om de aanvragen te maken. 

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Een uitbreidings kenmerk maken op een alleen Cloud gebruiker die gebruikmaakt van Microsoft Graph
U moet een toepassing gebruiken om het schema van uw gebruikers uit te breiden. De apps in uw Tenant weer geven om de id te identificeren van de toepassing die u wilt gebruiken om het gebruikers schema uit te breiden. [Meer informatie.](https://docs.microsoft.com/graph/api/application-list?view=graph-rest-1.0&tabs=http)

```json
GET https://graph.microsoft.com/v1.0/applications
```

Maak het uitbreidings kenmerk. Vervang de eigenschap **id** hieronder door de **id** die u in de vorige stap hebt opgehaald. U moet het kenmerk **' id '** en niet de ' AppID ' gebruiken. [Meer informatie.](https://docs.microsoft.com/graph/api/application-post-extensionproperty?view=graph-rest-1.0&tabs=http)
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

De vorige aanvraag heeft een uitbreidings kenmerk met de indeling ' extension_appID_extensionName ' gemaakt. Een gebruiker met het uitbreidings kenmerk bijwerken. [Meer informatie.](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0&tabs=http)
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Controleer de gebruiker om te controleren of het kenmerk is bijgewerkt. [Meer informatie.](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select)

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

#Set a value for the extension property on the user. Replace the objectid with the id of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="next-steps"></a>Volgende stappen

* [Definiëren wie binnen het bereik van de inrichting valt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
