---
title: Regels voor het lidmaatschap van dynamisch ingevulde groepen - Azure AD | Microsoft Docs
description: Lidmaatschapsregel maken om automatisch groepen in te vullen en een regelverwijzing.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: overview
ms.date: 02/18/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c25504e3313234ac6b6f80a6e00c77fce28b1400
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174526"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Dynamische lidmaatschapsregels voor groepen in Azure Active Directory

In Azure Active Directory (Azure AD) kunt u complexe op kenmerken gebaseerde regels maken om dynamische lidmaatschappen voor groepen in te schakelen. Dynamisch groepslidmaatschap vermindert de administratieve belasting van het toevoegen en verwijderen van gebruikers. Dit artikel beschrijft de eigenschappen en de syntaxis om dynamische lidmaatschapsregels voor gebruikers of apparaten te maken. U kunt een regel instellen voor dynamisch lidmaatschap voor beveiligingsgroepen of Microsoft 365-groepen.

Wanneer kenmerken van een gebruiker of apparaat worden gewijzigd, evalueert het systeem alle dynamische groepsregels in een directory om te zien of de wijziging leidt tot toevoegingen of verwijderingen van de groep. Als een gebruiker of apparaat voldoet aan een regel voor een groep, wordt deze toegevoegd als lid van die groep. Als deze niet meer voldoen aan de regel, worden ze verwijderd. U kunt een lid van een dynamische groep niet handmatig toevoegen of verwijderen.

- U kunt een dynamische groep maken voor apparaten of voor gebruikers, maar u kunt geen regel maken die zowel gebruikers als apparaten bevat.
- Het is evenmin mogelijk om een apparaatgroep te maken op basis van kenmerken van de apparaateigenaren. Regels voor apparaatlidmaatschap kunnen alleen verwijzen naar apparaatkenmerken.

> [!NOTE]
> U hebt voor deze functie een Azure AD Premium P1-licentie nodig voor elke unieke gebruiker die lid is van een of meer dynamische groepen. U hoeft geen licenties toe te wijzen aan gebruikers zodat deze lid kunnen zijn van dynamische groepen, maar u moet wel het minimumaantal licenties hebben in de Azure AD-organisatie om al deze gebruikers te dekken. Als u bijvoorbeeld een totaal van 1000 unieke gebruikers in alle dynamische groepen in uw organisatie hebt, moet u minstens 1000 licenties voor Azure AD Premium P1 hebben om aan de licentie-eis te voldoen.
> Er is geen licentie vereist voor apparaten die lid zijn van een dynamische apparaatgroep.

## <a name="rule-builder-in-the-azure-portal"></a>De opbouwfunctie voor regels in Azure Portal

Azure AD biedt een opbouwfunctie voor regels om uw belangrijke regels sneller te maken en bij te werken. De opbouwfunctie voor regels ondersteunt de constructie van maximaal vijf expressies. Met de opbouwfunctie voor regels kunt u eenvoudiger een regel maken met enkele eenvoudige expressies, maar u kunt hiermee niet elke regel reproduceren. Als de opbouwfunctie voor regels geen ondersteuning biedt voor de regel die u wilt maken, kunt u het tekstvak gebruiken.

Hier volgen enkele voorbeelden van geavanceerde regels of syntaxis die u het beste kunt maken via het tekstvak:

- Regel met meer dan vijf expressies
- De regel voor direct ondergeschikten
- [Prioriteit van operator](#operator-precedence) instellen
- [Regels met complexe expressies](#rules-with-complex-expressions); bijvoorbeeld `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> De opbouwfunctie voor regels kan mogelijk geen regels weergeven die zijn gemaakt in het tekstvak. Mogelijk wordt een bericht weergegeven wanneer de opbouwfunctie voor regels de regel niet kan weergeven. Met de opbouwfunctie voor regels wijzigt u niet de ondersteunde syntaxis, validatie of verwerking van dynamische groepsregels.

Zie [Een dynamische groep maken of bijwerken](groups-create-rule.md) voor meer stapsgewijze instructies.

![Lidmaatschapsregel voor een dynamische groep toevoegen](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>Regelsyntaxis voor één expressie

Eén expressie is de eenvoudigste vorm van een lidmaatschapsregel en heeft alleen de drie onderdelen die hierboven worden genoemd. Een regel met één expressie ziet er ongeveer als volgt uit: `Property Operator Value`, waarbij de syntaxis van de eigenschap de naam van object.property is.

Hier volgt een voorbeeld van een goed opgebouwde lidmaatschapsregel met één expressie:

```
user.department -eq "Sales"
```

Haakjes zijn optioneel voor één expressie. De totale lengte van de hoofdtekst van de lidmaatschapsregel mag niet langer zijn dan 2048 tekens.

## <a name="constructing-the-body-of-a-membership-rule"></a>De hoofdtekst van een lidmaatschapsregel samenstellen

Een lidmaatschapsregel die automatisch een groep met gebruikers of apparaten invult, is een binaire expressie die resulteert in het resultaat True of False. De drie delen van een eenvoudige regel zijn:

- Eigenschap
- Operator
- Waarde

De volgorde van de onderdelen binnen een expressie is belangrijk om syntaxisfouten te voorkomen.

## <a name="supported-properties"></a>Ondersteunde eigenschappen

Er zijn drie soorten eigenschappen die kunnen worden gebruikt om een lidmaatschapsregel samen te stellen.

- Boolean
- Tekenreeks
- Tekenreeksverzameling

Hier volgen de gebruikerseigenschappen die u kunt gebruiken om één expressie te maken.

### <a name="properties-of-type-boolean"></a>Eigenschappen van het type Booleaans

| Eigenschappen | Toegestane waarden | Gebruik |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true |
| dirSyncEnabled |true false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>Eigenschappen van het type tekenreeks

| Eigenschappen | Toegestane waarden | Gebruik |
| --- | --- | --- |
| city |Elke tekenreekswaarde of *null* |(user.city -eq "value") |
| country |Elke tekenreekswaarde of *null* |(user.country -eq "value") |
| companyName | Elke tekenreekswaarde of *null* | (user.companyName -eq "value") |
| department |Elke tekenreekswaarde of *null* |(user.department -eq "value") |
| displayName |Elke tekenreekswaarde |(user.displayName -eq "value") |
| employeeId |Elke tekenreekswaarde |(user.employeeId -eq "value")<br>(user.employeeId -ne *null*) |
| facsimileTelephoneNumber |Elke tekenreekswaarde of *null* |(user.facsimileTelephoneNumber -eq "value") |
| givenName |Elke tekenreekswaarde of *null* |(user.givenName -eq "value") |
| jobTitle |Elke tekenreekswaarde of *null* |(user.jobTitle -eq "value") |
| mail |Een willekeurige tekenreekswaarde of *null* (SMTP-adres van de gebruiker) |(user.mail -eq "value") |
| mailNickName |Een willekeurige tekenreekswaarde (e-mailalias van de gebruiker) |(user.mailNickName -eq "value") |
| mobiel |Elke tekenreekswaarde of *null* |(user.mobile -eq "value") |
| objectId |GUID van het gebruikersobject |(user.objectId -eq "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Een on-premises beveiligings-id (SID) voor gebruikers die zijn gesynchroniseerd van on-premises naar de cloud. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Elke tekenreekswaarde of *null* |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |Elke tekenreekswaarde of *null* |(user.postalCode -eq "value") |
| preferredLanguage |ISO 639-1-code |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Elke tekenreekswaarde of *null* |(user.sipProxyAddress -eq "value") |
| staat |Elke tekenreekswaarde of *null* |(user.state -eq "value") |
| streetAddress |Elke tekenreekswaarde of *null* |(user.streetAddress -eq "value") |
| surname |Elke tekenreekswaarde of *null* |(user.surname -eq "value") |
| telephoneNumber |Elke tekenreekswaarde of *null* |(user.telephoneNumber -eq "value") |
| usageLocation |Land-/regiocode bestaande uit twee letters |(user.usageLocation -eq "US") |
| userPrincipalName |Elke tekenreekswaarde |(user.userPrincipalName -eq "alias@domain") |
| userType |lidgast *null* |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>Eigenschappen van tekenreeksverzameling

| Eigenschappen | Toegestane waarden | Gebruik |
| --- | --- | --- |
| otherMails |Elke tekenreekswaarde |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

Zie [Regels voor apparaten](#rules-for-devices) voor de eigenschappen die worden gebruikt voor apparaatregels.

## <a name="supported-expression-operators"></a>Ondersteunde expressieoperators

De volgende tabel geeft een lijst van alle ondersteunde operators en hun syntaxis voor één expressie. Operators kunnen worden gebruikt met of zonder het afbreekstreepje (-) als voorvoegsel.

| Operator | Syntax |
| --- | --- |
| Is niet gelijk aan |-ne |
| Is gelijk aan |-eq |
| Begint niet met |-notStartsWith |
| Begint met |-startsWith |
| Bevat geen |-notContains |
| Contains |-contains |
| Komt niet overeen met |-notMatch |
| Match |-match |
| In | -in |
| Behoort niet tot | -notIn |

### <a name="using-the--in-and--notin-operators"></a>De operators -in en -notIn gebruiken

Als u de waarde van een gebruikerskenmerk wilt vergelijken met een aantal verschillende waarden, kunt u de operators -in of -notIn gebruiken. Gebruik de vierkante haken [ en ] om de lijst met waarden te starten en te beëindigen.

 In het volgende voorbeeld evalueert de uitdrukking naar true als de waarde van user.department gelijk is aan een van de waarden in de lijst:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>De operator -match gebruiken 
De operator **-match** wordt gebruikt voor het vergelijken van een reguliere expressie. Voorbeelden:

```
user.displayName -match "Da.*"   
```
Da, Dav, David evalueert naar true, aDa evalueert naar false.

```
user.displayName -match ".*vid"
```
David evalueert naar true, Da evalueert naar false.

## <a name="supported-values"></a>Ondersteunde waarden

De waarden die in een expressie worden gebruikt, kunnen bestaan uit verschillende typen, waaronder:

* Tekenreeksen
* Booleaans – true, false
* Getallen
* Matrices – nummermatrix, tekenreeksmatrix

Wanneer u een waarde in een expressie opgeeft, is het belangrijk dat u de juiste syntaxis gebruikt om fouten te voorkomen. Enkele syntaxistips zijn:

* Dubbele aanhalingstekens zijn optioneel, tenzij de waarde een tekenreeks is.
* Tekenreeks- en regex-bewerkingen zijn niet hoofdlettergevoelig.
* Wanneer een tekenreekswaarde dubbele aanhalingstekens bevat, moeten beide aanhalingstekens een escape-teken \` krijgen, zo is user.department -eq \`"Sales\`" de juiste syntaxis als Sales de waarde is.
* U kunt ook null-controles uitvoeren, waarbij null als waarde wordt gebruikt, bijvoorbeeld `user.department -eq null`.

### <a name="use-of-null-values"></a>Null-waarden gebruiken

Als u een null-waarde in een regel wilt opgeven, gebruikt u de *null*-waarde. 

* Gebruik -eq of -ne bij het vergelijken van de *null*-waarde in een expressie.
* Gebruik alleen aanhalingstekens rond het woord *null* als u wilt dat het wordt geïnterpreteerd als een letterlijke tekenreekswaarde.
* De operator -not kan niet worden gebruikt als een vergelijkingsoperator voor null. Als u deze gebruikt, wordt een foutbericht weergegeven over of u null of $null gebruikt.

De juiste manier om te verwijzen naar de null-waarde is als volgt:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Regels met meerdere expressies

Een regel voor groepslidmaatschap kan bestaan uit meer dan één expressie die is verbonden door de operators -and, -or en -not. Logische operators kunnen ook worden gebruikt in combinatie. 

Hier volgen enkele voorbeelden van goed samengestelde lidmaatschapsregels met meerdere expressies:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Bewerkingsvolgorde van operators

Alle operators worden hieronder weergegeven in volgorde van prioriteit van hoog naar laag. Operators op dezelfde regel hebben dezelfde prioriteit:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Hier volgt een voorbeeld van de prioriteit van operators waarbij twee expressies worden geëvalueerd voor de gebruiker:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Haakjes zijn alleen nodig wanneer de prioriteit niet voldoet aan uw vereisten. Als u bijvoorbeeld wilt dat de afdeling eerst wordt geëvalueerd, ziet u in het volgende voorbeeld hoe haakjes kunnen worden gebruikt om de volgorde te bepalen:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Regels met complexe expressies

Een lidmaatschapsregel kan bestaan uit complexe expressies waarbij de eigenschappen, operators en waarden meer complexe vormen hebben. Expressies worden beschouwd als complex wanneer een van de volgende voorwaarden waar is:

* De eigenschap bestaat uit een verzameling waarden; met name eigenschappen met meerdere waarden
* De expressies gebruiken de operators -any en -all
* De waarde van de expressie kan zelf een of meer expressies zijn

## <a name="multi-value-properties"></a>Eigenschappen met meerdere waarden

Eigenschappen met meerdere waarden zijn verzamelingen objecten van hetzelfde type. Ze kunnen worden gebruikt om lidmaatschapsregels te maken met behulp van de logische operators -any en -all.

| Eigenschappen | Waarden | Gebruik |
| --- | --- | --- |
| assignedPlans | Elk object in de verzameling legt de volgende tekenreekseigenschappen bloot: capabilityStatus, service, serviceePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |
| proxyAddresses| SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -any (\_ -contains "contoso")) |

### <a name="using-the--any-and--all-operators"></a>De operators -any en -all gebruiken

U kunt de operators -any en -all gebruiken om een voorwaarde toe te passen op respectievelijk één of alle items in de verzameling.

* -any (er wordt aan de voorwaarde voldaan wanneer ten minste één item in de verzameling overeenkomt met die voorwaarde)
* -all (er wordt aan de voorwaarde voldaan wanneer alle items in de verzameling overeenkomen met die voorwaarde)

#### <a name="example-1"></a>Voorbeeld 1

assignedPlans is een eigenschap met meerdere waarden waarmee alle serviceplannen worden weergegeven die aan de gebruiker zijn toegewezen. Met de volgende expressie selecteert u gebruikers die het serviceplan Exchange Online (abonnement 2) hebben (als GUID-waarde) die ook is ingeschakeld:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Een regel als deze kan worden gebruikt om alle gebruikers te groeperen voor wie een Microsoft 365-mogelijkheid (of andere Microsoft Online Service) is ingeschakeld. Vervolgens kunt u een set beleidsregels op de groep toepassen.

#### <a name="example-2"></a>Voorbeeld 2

Met de volgende uitdrukking selecteert u alle gebruikers die een serviceplan hebben dat is gekoppeld aan de Intune-service (geïdentificeerd met de servicenaam SCO):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

#### <a name="example-3"></a>Voorbeeld 3

Met de volgende expressie worden alle gebruikers geselecteerd die geen asigned-service plan hebben:

```
user.assignedPlans -all (assignedPlan.servicePlanId -eq "")
```

### <a name="using-the-underscore-_-syntax"></a>De onderstrepingssyntaxis (\_) gebruiken

De onderstrepingssyntaxis (\_) komt overeen met instanties van een specifieke waarde in een van de eigenschappen met meerdere waarden van tekenreeksenverzamelingen om gebruikers of apparaten aan een dynamische groep toe te voegen. Deze wordt gebruikt met de operators -any of -all.

Hier volgt een voorbeeld van het gebruik van de onderstreping (\_) in een regel om leden toe te voegen op basis van user.proxyAddress (het werkt hetzelfde voor user.otherMails). Met deze regel wordt een gebruiker met een proxyadres dat contoso bevat aan de groep toegevoegd.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Andere eigenschappen en algemene regels

### <a name="create-a-direct-reports-rule"></a>Een regel voor direct ondergeschikten maken

U kunt een groep maken met alle directe ondergeschikten van een manager. Wanneer de direct ondergeschikten van de manager in de toekomst worden gewijzigd, wordt het lidmaatschap van de groep automatisch aangepast.

De regel voor direct ondergeschikten wordt samengesteld met behulp van de volgende syntaxis:

```
Direct Reports for "{objectID_of_manager}"
```

Dit is een voorbeeld van een geldige regel waarbij 62e19b97-8b3d-4d4a-a106-4ce66896a863 de object-id van de manager is:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

Aan de hand van de volgende tips kunt u de regel op de juiste manier gebruiken.

- De **Manager-id** is de object-id van de manager. Dit kan worden gevonden in het **profiel** van de manager.
- Om de regel te laten werken, moet u ervoor zorgen dat de eigenschap **Manager** correct is ingesteld voor gebruikers in uw organisatie. U kunt de huidige waarde in het **profiel** van de gebruiker controleren.
- Deze regel ondersteunt alleen de direct ondergeschikten van de manager. Met andere woorden, u kunt geen groep maken met de direct ondergeschikten van de manager *en* hun ondergeschikten.
- Deze regel kan niet worden gecombineerd met andere lidmaatschapsregels.

### <a name="create-an-all-users-rule"></a>Een regel voor alle gebruikers maken

U kunt een groep met alle gebruikers in een organisatie maken met behulp van een lidmaatschapsregel. Wanneer gebruikers in de toekomst worden toegevoegd aan of verwijderd uit de organisatie, wordt het lidmaatschap van de groep automatisch aangepast.

De regel voor alle gebruikers wordt samengesteld met behulp van een enkele expressie met behulp van de operator -ne en de null-waarde. Met deze regel voegt u B2B-gastgebruikers toe, evenals gebruikers van leden aan de groep.

```
user.objectId -ne null
```
Als u wilt dat voor de groep gastgebruikers worden uitgesloten en alleen leden van uw organisatie omvat, kunt u de volgende syntaxis gebruiken:

```
(user.objectId -ne null) -and (user.userType -eq "Member")
```

### <a name="create-an-all-devices-rule"></a>Een regel voor alle apparaten maken

U kunt een groep met alle apparaten in een organisatie maken met behulp van een lidmaatschapsregel. Wanneer apparaten in de toekomst worden toegevoegd aan of verwijderd uit de organisatie, wordt het lidmaatschap van de groep automatisch aangepast.

De regel voor alle apparaten wordt samengesteld met behulp van een enkele expressie met behulp van de operator -ne en de null-waarde:

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Extensie-eigenschappen en aangepaste extensie-eigenschappen

Extensiekenmerken en aangepaste extensie-eigenschappen worden ondersteund als tekenreekseigenschappen in dynamische lidmaatschapsregels. [Extensiekenmerken](/graph/api/resources/onpremisesextensionattributes) worden gesynchroniseerd vanaf de on-premises Windows Server AD en hebben de indeling ExtensionAttributeX, waarbij X gelijk is aan 1-15. Hier volgt een voorbeeld van een regel die waarin gebruik wordt gemaakt van een uitbreidingskenmerk als eigenschap:

```
(user.extensionAttribute15 -eq "Marketing")
```

[Aangepaste extensie-eigenschappen](../hybrid/how-to-connect-sync-feature-directory-extensions.md) worden gesynchroniseerd vanuit de on-premises Windows Server AD of vanuit een verbonden SaaS-toepassing, en hebben de indeling `user.extension_[GUID]_[Attribute]`, waarbij:

* [GUID] de unieke id in Azure AD is voor de toepassing die de eigenschap heeft gemaakt in Azure AD
* [Kenmerk] de naam is van de eigenschap die is gemaakt

Een voorbeeld van een regel waarin gebruik wordt gemaakt van een aangepaste extensie-eigenschap is:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

De aangepaste naam van de eigenschap kan worden gevonden in de map door een query uit te voeren op de eigenschap van een gebruiker met behulp van Graph Explorer en te zoeken naar de naam van de eigenschap. Ook kunt u nu klikken op de koppeling **Aangepaste extensie-eigenschappen ophalen** in de opbouwfunctie voor regels voor de dynamische gebruikersgroep om een unieke app-id in te voeren en de volledige lijst van aangepaste extensie-eigenschappen te ontvangen om toe te passen bij het maken van een dynamische lidmaatschapsregel. Deze lijst kan ook worden vernieuwd om nieuwe aangepaste extensie-eigenschappen voor die app op te halen.

## <a name="rules-for-devices"></a>Regels voor apparaten

U kunt ook een regel maken waarmee apparaatobjecten worden geselecteerd voor het lidmaatschap van een groep. U kunt niet zowel gebruikers als apparaten als groepsleden hebben. 

> [!NOTE]
> Het kenmerk **organizationalUnit** wordt niet meer weergegeven en mag niet worden gebruikt. Deze tekenreeks wordt in specifieke gevallen door Intune ingesteld, maar wordt niet herkend door Azure AD, zodat er geen apparaten worden toegevoegd aan groepen op basis van dit kenmerk.

> [!NOTE]
> systemlabels is een alleen-lezenkenmerk dat niet kan worden ingesteld met Intune.
>
> Voor Windows 10 is de juiste indeling van het kenmerk deviceOSVersion als volgt: (device.deviceOSVersion -eq "10.0.17763"). De opmaak kan worden gevalideerd met de Get-MsolDevice PowerShell cmdlet.

De volgende apparaatkenmerken kunnen worden gebruikt.

 Apparaatkenmerk  | Waarden | Voorbeeld
 ----- | ----- | ----------------
 accountEnabled | true false | (device.accountEnabled -eq true)
 displayName | elke tekenreekswaarde |(device.displayName -eq "Rob iPhone")
 deviceOSType | elke tekenreekswaarde | (device.deviceOSType -eq "iPad") -or (device.deviceOSType -eq "iPhone")<br>(device.deviceOSType -contains "AndroidEnterprise")<br>(device.deviceOSType -eq "AndroidForWork")<br>(Device. deviceOSType-EQ "Windows")
 deviceOSVersion | elke tekenreekswaarde | (device.deviceOSVersion -eq "9.1")<br>(Device. deviceOSVersion-EQ "10.0.17763.0")
 deviceCategory | een geldige naam voor een apparaatcategorie | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | elke tekenreekswaarde | (device.deviceManufacturer -eq "Samsung")
 deviceModel | elke tekenreekswaarde | (device.deviceModel -eq "iPad Air")
 deviceOwnership | Persoonlijk, Bedrijf, Onbekend | (device.deviceOwnership -eq "Company")
 enrollmentProfileName | Inschrijvingsprofielen van Apple- of Android Enterprise-apparaat in het bezit van het bedrijf of profielnaam van Windows Autopilot | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | true false | (device.isRooted -eq true)
 managementType | MDM (voor mobiele apparaten)<br>PC (voor computers die worden beheerd door de Intune PC-agent) | (device.managementType -eq "MDM")
 deviceId | een geldig apparaat-id voor Azure AD | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | een geldige Azure AD-object-id |  (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d")
 devicePhysicalIds | een tekenreekswaarde die wordt gebruikt door Autopilot, zoals alle Autopilot-apparaten, OrderID of PurchaseOrderID  | (device.devicePhysicalIDs -any _ -contains "[ZTDId]") (device.devicePhysicalIds -any _ -eq "[OrderID]:179887111881") (device.devicePhysicalIds -any _ -eq "[PurchaseOrderId]:76222342342")
 systemLabels | een tekenreeks die overeenkomt met de eigenschap van het Intune-apparaat voor het labelen van moderne werkplekapparaten | (device.systemLabels -contains "M365Managed")

> [!Note]  
> Voor de deviceOwnership bij het maken van dynamische groepen voor apparaten moet u de waarde instellen op Bedrijf. Op Intune wordt het apparaateigendom in plaats daarvan vertegenwoordigd als Bedrijf. Zie [OwnerTypes](/intune/reports-ref-devices#ownertypes) voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over Azure Active Directory-groepen.

- [Bestaande groepen weergeven](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Een nieuwe groep maken en leden toevoegen](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Instellingen van een groep beheren](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Lidmaatschappen van een groep beheren](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Dynamische regels voor gebruikers in een groep beheren](groups-create-rule.md)
