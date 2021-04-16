---
title: Kenmerken synchroniseren naar Azure Active Directory voor toewijzing
description: Wanneer u het inrichten van gebruikers configureert met Azure Active Directory- en SaaS-apps, gebruikt u de functie voor directory-extensies om bronkenmerken toe te voegen die niet standaard worden gesynchroniseerd.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2021
ms.author: kenwith
ms.openlocfilehash: f7a2429161cebe867d844b4ca7aa08ec3613edcd
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388207"
---
# <a name="syncing-extension-attributes-for-app-provisioning"></a>Extensiekenmerken synchroniseren voor app-inrichting

Azure Active Directory (Azure AD) moet alle gegevens (kenmerken) bevatten die nodig zijn om een gebruikersprofiel te maken bij het inrichten van gebruikersaccounts van Azure AD naar een [SaaS-app.](../saas-apps/tutorial-list.md) Wanneer u kenmerktoewijzingen voor het inrichten van gebruikers aanwijst,  wordt het kenmerk dat u wilt toewijzen mogelijk niet weergegeven in de lijst Bronkenmerk. In dit artikel wordt beschreven hoe u het ontbrekende kenmerk toevoegt.

Voor gebruikers alleen in Azure AD kunt u [schema-extensies maken met behulp van PowerShell of Microsoft Graph.](#create-an-extension-attribute-on-a-cloud-only-user)

Voor gebruikers in on-premises Active Directory moet u de gebruikers synchroniseren met Azure AD. U kunt gebruikers en kenmerken synchroniseren met behulp [van Azure AD Connect.](../hybrid/whatis-azure-ad-connect.md) Azure AD Connect worden bepaalde kenmerken automatisch gesynchroniseerd met Azure AD, maar niet alle kenmerken. Bovendien worden sommige kenmerken (zoals SAMAccountName) die standaard worden gesynchroniseerd, mogelijk niet weergegeven met behulp van de Azure AD-Graph API. In dergelijke gevallen kunt u de functie Azure AD Connect [directory-extensie gebruiken](#create-an-extension-attribute-using-azure-ad-connect)om het kenmerk te synchroniseren met Azure AD. Op die manier is het kenmerk zichtbaar voor de Azure AD-Graph API en de Azure AD-inrichtingsservice.

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Een extensiekenmerk maken voor een cloudgebruiker
U kunt Microsoft Graph en PowerShell gebruiken om het gebruikersschema voor gebruikers in Azure AD uit te breiden. Deze extensiekenmerken worden in de meeste gevallen automatisch ontdekt.

Wanneer u meer dan 1000 service-principals hebt, ontbreken extensies mogelijk in de lijst met bronkenmerken. Als een kenmerk dat u hebt gemaakt niet automatisch wordt weergegeven, controleert u of het kenmerk is gemaakt en voegt u het handmatig toe aan uw schema. Gebruik Microsoft Graph Graph Explorer om te controleren [of het is gemaakt.](/graph/graph-explorer/graph-explorer-overview) Zie De lijst met ondersteunde kenmerken bewerken om deze handmatig toe te voegen [aan uw](customize-application-attributes.md#editing-the-list-of-supported-attributes)schema.

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Een extensiekenmerk voor een cloudgebruiker maken met behulp van Microsoft Graph
U kunt het schema van Azure AD-gebruikers uitbreiden met behulp [van Microsoft Graph](/graph/overview). 

Vermeld eerst de apps in uw tenant om de id op te halen van de app waarmee u werkt. Zie [LijstextensieProperties voor meer informatie.](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true)

```json
GET https://graph.microsoft.com/v1.0/applications
```

Maak vervolgens het extensiekenmerk. Vervang de **onderstaande** id-eigenschap door de **id** die u in de vorige stap hebt opgehaald. U moet het kenmerk **Id** gebruiken en niet de appId. Zie [Create extensionProperty]/graph/api/application-post-extensionproperty.md?view=graph-rest-1.0&tabs=http&preserve-view=true) voor meer informatie.

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

Met de vorige aanvraag is een extensiekenmerk gemaakt met de indeling `extension_appID_extensionName` . U kunt nu een gebruiker bijwerken met dit extensiekenmerk. Zie Gebruiker bijwerken [voor meer informatie.](/graph/api/user-update?view=graph-rest-1.0&tabs=http&preserve-view=true)
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Controleer ten slotte het kenmerk voor de gebruiker. Zie Een gebruiker krijgen [voor meer informatie.](/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true)

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>Een extensiekenmerk voor een cloudgebruiker maken met behulp van PowerShell
Maak een aangepaste extensie met behulp van PowerShell en wijs een waarde toe aan een gebruiker. 

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

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Een extensiekenmerk maken met Azure AD Connect

1. Open de wizard Azure AD Connect, kies Taken en kies vervolgens **Synchronisatieopties aanpassen.**

   ![Azure Active Directory Connect wizard Aanvullende taken](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Meld u aan als globale beheerder van Azure AD. 

3. Selecteer op **de pagina Optionele** functies de optie **Synchronisatie van directory-extensiekenmerken.**
 
   ![Azure Active Directory Connect wizard Optionele functies](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Selecteer de kenmerken die u wilt uitbreiden naar Azure AD.
   > [!NOTE]
   > De zoekopdracht onder **Beschikbare kenmerken** is casegevoelig.

   ![Schermopname van de selectiepagina Mapextensies](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Voer de Azure AD Connect uit en sta toe dat een volledige synchronisatiecyclus wordt uitgevoerd. Wanneer de cyclus is voltooid, wordt het schema uitgebreid en worden de nieuwe waarden gesynchroniseerd tussen uw on-premises AD en Azure AD.
 
6. In de Azure Portal, terwijl u toewijzingen van gebruikerskenmerken [bewerkt,](customize-application-attributes.md)bevat de lijst Bronkenmerk nu het toegevoegde kenmerk in de indeling  `<attributename> (extension_<appID>_<attributename>)` . Selecteer het kenmerk en wijs het toe aan de doeltoepassing voor inrichting.

   ![Azure Active Directory Connect wizard Directory-extensies selecteren](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> De mogelijkheid om referentiekenmerken van on-premises AD in terichten, zoals **managedby** of **DN/DistinguishedName,** wordt momenteel niet ondersteund. U kunt deze functie aanvragen via [User Voice.](https://feedback.azure.com/forums/169401-azure-active-directory) 


## <a name="next-steps"></a>Volgende stappen

* [Definieer wie binnen het bereik voor inrichting valt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
