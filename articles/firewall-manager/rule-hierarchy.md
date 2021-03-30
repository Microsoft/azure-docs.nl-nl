---
title: Azure Firewall-beleid gebruiken om een regel hiërarchie te definiëren
description: Meer informatie over het gebruik van Azure Firewall-beleid voor het definiëren van een regel hiërarchie en het afdwingen van naleving.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 1ba683e3d616f52854f1055dab9b9fe2d389116a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92331733"
---
# <a name="use-azure-firewall-policy-to-define-a-rule-hierarchy"></a>Azure Firewall-beleid gebruiken om een regel hiërarchie te definiëren

Beveiligings beheerders moeten firewalls beheren en ervoor zorgen dat wordt voldaan aan on-premises en Cloud implementaties. Een belang rijk onderdeel is de mogelijkheid om toepassings teams te voorzien van flexibiliteit voor het implementeren van CI/CD-pijp lijnen om firewall regels op een geautomatiseerde manier te maken.

Met Azure Firewall-beleid kunt u een regel hiërarchie definiëren en naleving afdwingen:

- Voorziet in een hiërarchische structuur voor het bedekken van een centraal basis beleid boven op een onderliggend toepassings team beleid. Het basis beleid heeft een hogere prioriteit en wordt uitgevoerd vóór het onderliggende beleid.
- Gebruik een aangepaste functie definitie van Azure om te voor komen dat er onbedoeld basis beleid wordt verwijderd en geef selectieve toegang tot regel verzamelings groepen binnen een abonnement of resource groep op. 

## <a name="solution-overview"></a>Oplossingenoverzicht

De stappen op hoog niveau voor dit voor beeld zijn:

1. Maak een basis firewall-beleid in de resource groep van het beveiligings team. 
3. Definieer IT-beveiligings regels in het basis beleid. Hiermee voegt u een algemene set regels toe om verkeer toe te staan of te weigeren.
4. Maak een toepassings team beleid dat het basis beleid overneemt. 
5. Definieer specifieke regels voor het toepassings team in het beleid. U kunt ook regels migreren van bestaande firewalls.
6. Maak Azure Active Directory aangepaste rollen om nauw keurige toegang te bieden tot regel verzamelings groep en rollen toe te voegen aan het bereik van een firewall beleid. In het volgende voor beeld kunnen leden van het verkoop team regel verzamelings groepen bewerken voor het firewall beleid verkoop teams. Hetzelfde geldt voor de data base en technische teams.
7. Koppel het beleid aan de bijbehorende firewall. Een Azure-firewall kan slechts één toegewezen beleid hebben. Hiervoor moet elk toepassings team een eigen firewall hebben.



:::image type="content" source="media/rule-hierarchy/contoso-teams.png" alt-text="Teams en vereisten" border="false":::

### <a name="create-the-firewall-policies"></a>Het firewall beleid maken

- Een base firewall-beleid.

Beleids regels maken voor elk van de toepassings teams:

- Een Sales firewall-beleid. Het firewall beleid verkoop neemt het basis-firewall beleid over.
- Een database firewall beleid. Het firewall beleid van de data base neemt het basis firewall beleid over.
- Een engineering firewall-beleid. Het beleid van de technische firewall neemt ook het basis beleid voor firewalls over.

:::image type="content" source="media/rule-hierarchy/policy-hierarchy.png" alt-text="Beleids hiërarchie" border="false":::

### <a name="create-custom-roles-to-access-the-rule-collection-groups"></a>Aangepaste rollen maken voor toegang tot de regel verzamelings groepen 

Voor elk toepassings team worden aangepaste rollen gedefinieerd. De rol definieert bewerkingen en bereik. De toepassings teams mogen regel verzamelings groepen voor hun respectieve toepassingen bewerken.

Gebruik de volgende procedure op hoog niveau om aangepaste rollen te definiëren:

1. Het abonnement ophalen:

   `Select-AzSubscription -SubscriptionId xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`
2. Voer de volgende opdracht uit:

   `Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize`
3. Gebruik de Get-AzRoleDefinition opdracht om de rol van lezer in JSON-indeling uit te voeren. 

   `Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json`
4. Open de ReaderSupportRole.jsvoor het bestand in een editor.

   Hieronder ziet u de JSON-uitvoer. Zie voor meer informatie over de verschillende eigenschappen [Azure aangepaste rollen](../role-based-access-control/custom-roles.md).

```json
   { 
     "Name": "Reader", 
     "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7", 
     "IsCustom": false, 
     "Description": "Lets you view everything, but not make any changes.", 
     "Actions": [ 
      "*/read" 
     ], 
     "NotActions": [], 
     "DataActions": [], 
     "NotDataActions": [], 
     "AssignableScopes": [ 
       "/" 
     ] 
   } 
```
5. Bewerk het JSON-bestand om het toe te voegen 

   `*/read", "Microsoft.Network/*/read", "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write` 

   bewerking naar de eigenschap **Actions**   . Vergeet niet om een komma toe te voegen na de read-bewerking. Met deze actie kan de gebruiker regel verzamelings groepen maken en bijwerken.
6. Voeg in **AssignableScopes** uw abonnements-id toe met de volgende indeling: 

   `/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`

   U moet expliciete abonnement-id's toevoegen, anders is het niet mogelijk om de rol in uw abonnement te importeren.
7. Verwijder de regel voor de **id-**   eigenschap en wijzig de eigenschap **IsCustom**   in True.
8. Wijzig de  ****   Eigenschappen van de naam en  **Beschrijving** in de   AZFM- *regel verzamelings groep Auteur* en *gebruikers met deze rol kunnen Firewall beleids regel verzamelings groepen bewerken*

Het JSON-bestand moet er ongeveer uitzien als in het volgende voor beeld:

```
{ 

    "Name":  "AZFM Rule Collection Group Author", 
    "IsCustom":  true, 
    "Description":  "Users in this role can edit Firewall Policy rule collection groups", 
    "Actions":  [ 
                    "*/read", 
                    "Microsoft.Network/*/read", 
                     "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write" 
                ], 
    "NotActions":  [ 
                   ], 
    "DataActions":  [ 
                    ], 
    "NotDataActions":  [ 
                       ], 
    "AssignableScopes":  [ 
                             "/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx"] 
} 
```
9. Als u de nieuwe aangepaste rol wilt maken, gebruikt u de opdracht New-AzRoleDefinition en geeft u het definitie bestand van de JSON-rol op. 

   `New-AzRoleDefinition -InputFile "C:\CustomRoles\RuleCollectionGroupRole.json`

### <a name="list-custom-roles"></a>Aangepaste rollen opvragen

U kunt de Get-AzRoleDefinition opdracht gebruiken om alle aangepaste rollen weer te geven:

   `Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom`

U kunt ook de aangepaste rollen bekijken in de Azure Portal. Ga naar uw abonnement en selecteer **toegangs beheer (IAM)**, **rollen**.

:::image type="content" source="media/rule-hierarchy/sales-app-policy.png" alt-text="SalesAppPolicy":::

:::image type="content" source="media/rule-hierarchy/sales-app-policy-read.png" alt-text="Lees machtiging SalesAppPolicy":::

Zie [zelf studie: een aangepaste Azure-rol maken met Azure PowerShell](../role-based-access-control/tutorial-custom-role-powershell.md)voor meer informatie.

### <a name="add-users-to-the-custom-role"></a>Gebruikers toevoegen aan de aangepaste rol

In de portal kunt u gebruikers toevoegen aan de rol groep auteurs van AZFM regel en toegang verlenen tot het firewall beleid.

1. Selecteer in de portal het toepassings team firewall beleid (bijvoorbeeld SalesAppPolicy).
2. Selecteer **Access Control**.
3. Selecteer **Roltoewijzing toevoegen**.
4. Voeg gebruikers/gebruikers groepen (bijvoorbeeld het verkoop team) toe aan de rol.

Herhaal deze procedure voor het andere firewall beleid.

### <a name="summary"></a>Samenvatting

Het firewall beleid met aangepaste rollen biedt nu selectieve toegang tot de verzamelings groepen firewall beleidsregel.

Gebruikers hebben geen machtigingen voor het volgende:
- Verwijder het Azure Firewall-of firewall-beleid.
- De hiërarchie van firewall beleid of DNS-instellingen of bedreigings informatie bijwerken.
- Update het firewall beleid waarbij deze niet lid zijn van de groep AZFM.

Beveiligings beheerders kunnen basis beleid gebruiken om Guardrails af te dwingen en bepaalde soorten verkeer te blok keren (bijvoorbeeld ICMP) zoals vereist door hun bedrijf. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure firewall-beleid](policy-overview.md).

