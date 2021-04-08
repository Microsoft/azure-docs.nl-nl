---
title: Bekende problemen met het systeem voor Identity Management (SCIM) 2,0 van het domein-Azure AD
description: Veelvoorkomende problemen met protocol compatibiliteit oplossen bij het toevoegen van een niet-galerie toepassing die ondersteuning biedt voor SCIM 2,0 in azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 08/05/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: d13629b4cb05995b9652e862f769a0ffcae30a8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256895"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Bekende problemen en oplossingen met SCIM 2,0-protocol compatibiliteit van de Azure AD User Provisioning Service

Azure Active Directory (Azure AD) kan gebruikers en groepen automatisch inrichten voor elke toepassing of elk systeem dat wordt gefronteerd door een webservice met de interface die is gedefinieerd in het [systeem voor Identity Management (scim) 2,0-protocol specificatie](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

De ondersteuning van Azure AD voor het SCIM 2,0-protocol wordt beschreven in [met behulp van systeem voor Cross-Domain Identity Management (scim) voor het automatisch inrichten van gebruikers en groepen van Azure Active Directory naar toepassingen](use-scim-to-provision-users-and-groups.md), waarin de specifieke onderdelen van het door IT geïmplementeerde protocol worden vermeld om automatisch gebruikers en groepen van Azure AD in te richten op toepassingen die scim 2,0 ondersteunen.

In dit artikel worden huidige en eerdere problemen beschreven met betrekking tot het SCIM 2,0-protocol van Azure AD User Provisioning Service, en hoe u deze problemen omzeilt.

## <a name="understanding-the-provisioning-job"></a>Informatie over de inrichtings taak
De inrichtings service maakt gebruik van het concept van een taak voor het uitvoeren van een toepassing. De jobID bevindt zich in de [voortgangs balk](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar). Alle nieuwe inrichtings toepassingen worden gemaakt met een jobID die begint met ' scim '. De taak scim vertegenwoordigt de huidige status van de service. Oudere taken hebben de ID ' customappsso '. Deze taak vertegenwoordigt de status van de service in 2018. 

Als u een toepassing in de galerie gebruikt, bevat de taak doorgaans de naam van de app (bijvoorbeeld zoom sneeuw, dataBricks, enzovoort). U kunt deze documentatie overs Laan bij gebruik van een galerie toepassing. Dit geldt voornamelijk voor niet-galerie toepassingen met jobID SCIM of customAppSSO.

## <a name="scim-20-compliance-issues-and-status"></a>Nalevings problemen en status van SCIM 2,0
In de onderstaande tabel staat een item dat is gemarkeerd als vast, dat het juiste gedrag kan worden gevonden in de SCIM-taak. We hebben ervoor gezorgd om achterwaartse compatibiliteit te garanderen voor de wijzigingen die we hebben aangebracht. Het is echter niet raadzaam oude gedrag te implementeren. Het is raadzaam om het nieuwe gedrag voor nieuwe implementaties te gebruiken en bestaande implementaties bij te werken.

> [!NOTE]
> Voor de wijzigingen die zijn aangebracht in 2018, kunt u terugkeren naar het customappsso-gedrag. Voor de wijzigingen die zijn aangebracht sinds 2018, kunt u de Url's gebruiken om terug te keren naar het oudere gedrag. We hebben ervoor gezorgd om achterwaartse compatibiliteit te garanderen voor de wijzigingen die we hebben aangebracht, zodat u terugkeert naar de oude jobID of met behulp van een vlag. Zoals eerder vermeld, raden we echter niet aan het implementeren van het oude gedrag. Het is raadzaam om het nieuwe gedrag voor nieuwe implementaties te gebruiken en bestaande implementaties bij te werken.

| **Compatibiliteits probleem met SCIM 2,0** |  **Vaste?** | **Datum herstellen**  |  **Achterwaartse compatibiliteit** |
|---|---|---|
| Voor Azure AD moet '/scim ' zich in de hoofdmap van de SCIM-eind punt-URL van de toepassing bevindt  | Yes  |  18 december 2018 | downgrade naar customappSSO |
| Extensie kenmerken gebruiken de notatie dot '. ' vóór kenmerk namen in plaats van dubbele punt notatie ': ' |  Yes  | 18 december 2018  | downgrade naar customappSSO |
| Patch aanvragen voor kenmerken met meerdere waarden bevatten ongeldige syntaxis van het filter | Yes  |  18 december 2018  | downgrade naar customappSSO |
| Aanvragen voor het maken van groepen bevatten een ongeldige schema-URI | Yes  |  18 december 2018  |  downgrade naar customappSSO |
| Update van PATCH-gedrag om te zorgen voor naleving (bijvoorbeeld actief als Booleaanse en de juiste verwijdering van groepslid maatschappen) | No | NOG TE BEPALEN| Preview-vlag gebruiken |

## <a name="flags-to-alter-the-scim-behavior"></a>Vlaggen voor het wijzigen van het SCIM-gedrag
Gebruik de onderstaande vlaggen in de Tenant-URL van uw toepassing om het standaard gedrag van de SCIM-client te wijzigen.

:::image type="content" source="media/application-provisioning-config-problem-scim-compatibility/scim-flags.jpg" alt-text="SCIM markeert tot later gedrag.":::

* Gebruik de volgende URL om het PATCH gedrag bij te werken en te zorgen voor SCIM-naleving (bijvoorbeeld actief als Booleaanse en de juiste verwijdering van groepslid maatschappen). Dit gedrag is momenteel alleen beschikbaar wanneer u de vlag gebruikt, maar zal de standaard instelling in de komende maanden worden. Opmerking deze preview-vlag werkt momenteel niet met inrichten op aanvraag. 
  * **URL (scim-compatibel):** AzureAdScimPatch062020
  * **SCIM RFC-verwijzingen:** 
    * https://tools.ietf.org/html/rfc7644#section-3.5.2
  * **Tabtoets**
  ```json
   PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
   {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "remove",
            "path": "members[value eq \"16b083c0-f1e8-4544-b6ee-27a28dc98761\"]"
        }
    ]
   }

    PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "members",
            "value": [
                {
                    "value": "10263a6910a84ef9a581dd9b8dcc0eae"
                }
            ]
        }
    ]
    } 

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].value",
            "value": "someone@contoso.com"
        },
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].primary",
            "value": true
        },
        {
            "op": "replace",
            "value": {
                "active": false,
                "userName": "someone"
            }
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "active",
            "value": false
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department",
            "value": "Tech Infrastructure"
        }
    ]
    }
   
  ```

  * **Downgrade-URL:** Zodra het nieuwe SCIM-compatibele gedrag wordt ingesteld als de standaard instelling voor de toepassing niet-galerie, kunt u de volgende URL gebruiken om terug te draaien naar het oude, niet-SCIM compatibele gedrag: AzureAdScimPatch2017
  


## <a name="upgrading-from-the-older-customappsso-job-to-the-scim-job"></a>Een upgrade uitvoeren van de oudere customappsso-taak naar de SCIM-taak
Volg de onderstaande stappen om uw bestaande customappsso-taak te verwijderen en een nieuwe scim-taak te maken. 
 
1. Meld u aan bij de Azure Portal op https://portal.azure.com .
2. Zoek en selecteer uw bestaande SCIM-toepassing in de sectie **Azure Active Directory > Enter prise Applications** van de Azure Portal.
3. Kopieer de **object-id** in het gedeelte **Eigenschappen** van uw bestaande scim-app.
4. In een nieuw browser venster gaat u naar https://developer.microsoft.com/graph/graph-explorer en meldt u zich aan als beheerder voor de Azure AD-Tenant waar uw app is toegevoegd.
5. Voer in de Graph Explorer de onderstaande opdracht uit om de ID van uw inrichtings taak te vinden. Vervang ' [object-id] ' door de Service-Principal-ID (object-ID) die uit de derde stap is gekopieerd.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Taken ophalen](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Taken ophalen") 


6. In de resultaten kopieert u de volledige ID-teken reeks die begint met ' customappsso ' of ' scim '.
7. Voer de onderstaande opdracht uit om de configuratie voor kenmerk toewijzing op te halen, zodat u een back-up kunt maken. Gebruik dezelfde [object-id] als voorheen en vervang [job-id] door de ID van de inrichtings taak die u uit de laatste stap hebt gekopieerd.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Schema ophalen](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Schema ophalen") 

8. Kopieer de JSON-uitvoer van de laatste stap en sla deze op in een tekst bestand. De JSON bevat alle aangepaste kenmerk toewijzingen die u aan uw oude app hebt toegevoegd, en moet ongeveer een paar duizend regels JSON zijn.
9. Voer de onderstaande opdracht uit om de inrichtings taak te verwijderen:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Voer de onderstaande opdracht uit om een nieuwe inrichtings taak te maken met de meest recente service oplossingen.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. In de resultaten van de laatste stap kopieert u de volledige ID-teken reeks die begint met ' scim '. Pas eventueel uw oude kenmerk toewijzingen opnieuw toe door de onderstaande opdracht uit te voeren, waarbij u [New-Job-ID] vervangt door de nieuwe taak-ID die u hebt gekopieerd, en u de JSON-uitvoer van stap #7 als aanvraag tekst.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Ga terug naar het eerste webbrowser venster en selecteer het tabblad **inrichten** voor uw toepassing.
13. Controleer uw configuratie en start vervolgens de inrichtings taak. 

## <a name="downgrading-from-the-scim-job-to-the-customappsso-job-not-recommended"></a>Downgrade van de SCIM-taak naar de customappsso-taak (niet aanbevolen)
 We bieden u de mogelijkheid om terug te gaan naar het oude gedrag, maar dit wordt niet aanbevolen omdat de customappsso niet van toepassing is op een aantal updates die we aanbrengen. dit wordt mogelijk niet altijd ondersteund. 

1. Meld u aan bij de Azure Portal op https://portal.azure.com .
2. Maak een nieuwe **niet-galerie** toepassing in de **Azure Active Directory > bedrijfs toepassingen > toepassings sectie maken** van de Azure Portal.
3. Kopieer de **object-id** in het gedeelte **Eigenschappen** van de nieuwe aangepaste app.
4. In een nieuw browser venster gaat u naar https://developer.microsoft.com/graph/graph-explorer en meldt u zich aan als beheerder voor de Azure AD-Tenant waar uw app is toegevoegd.
5. Voer in de Graph Explorer de onderstaande opdracht uit om de inrichtings configuratie voor uw app te initialiseren.
   Vervang ' [object-id] ' door de Service-Principal-ID (object-ID) die uit de derde stap is gekopieerd.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Ga terug naar het eerste webbrowser venster en selecteer het tabblad **inrichten** voor uw toepassing.
7. Voltooi de configuratie van de gebruikers inrichting zoals u dat gewend bent.


## <a name="next-steps"></a>Volgende stappen
[Meer informatie over het inrichten en ongedaan maken van de inrichting van SaaS-toepassingen](user-provisioning.md)
