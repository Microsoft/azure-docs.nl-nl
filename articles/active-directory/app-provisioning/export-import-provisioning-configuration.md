---
title: De inrichtings configuratie exporteren en terugkeren naar een bekende goede status voor herstel na nood gevallen
description: Meer informatie over het exporteren van uw inrichtings configuratie en het terugdraaien van een bekende goede status voor herstel na nood gevallen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 03/19/2020
ms.author: kenwith
ms.openlocfilehash: c6af42c78bda66c4b397cbb99b26af7d6a5c7f07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256370"
---
# <a name="how-to-export-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Instructies: inrichtings configuratie exporteren en terugdraaien naar een bekende goede staat

In dit artikel leert u het volgende:

- Uw inrichtings configuratie exporteren en importeren vanuit de Azure Portal
- Uw inrichtings configuratie exporteren en importeren met behulp van de Microsoft Graph-API

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Uw inrichtings configuratie exporteren en importeren vanuit de Azure Portal

### <a name="export-your-provisioning-configuration"></a>Uw inrichtings configuratie exporteren

De configuratie exporteren:

1. Selecteer **Azure Active Directory** in [Azure Portal](https://portal.azure.com/) in het navigatiepaneel aan de linkerkant.
1. Selecteer in het deel venster **Azure Active Directory** de optie **bedrijfs toepassingen** en kies uw toepassing.
1. Selecteer **inrichten** in het navigatie deel venster links. Klik op de pagina inrichtings configuratie op **kenmerk toewijzingen**, **Geef geavanceerde opties weer** en **Controleer vervolgens het schema**. Hiermee gaat u naar de schema-editor.
1. Klik op downloaden in de opdracht balk boven aan de pagina om uw schema te downloaden.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Herstel na nood geval: terugdraaien naar een bekende goede staat

Door de configuratie te exporteren en op te slaan, kunt u teruggaan naar een eerdere versie van uw configuratie. We raden u aan uw inrichtings configuratie te exporteren en deze op te slaan voor later gebruik wanneer u een wijziging aanbrengt in de kenmerk toewijzingen of filters bereikt. U hoeft alleen maar het JSON-bestand te openen dat u in de bovenstaande stappen hebt gedownload, de volledige inhoud van het JSON-bestand te kopiëren, de volledige inhoud van de JSON-nettolading in de schema-editor te vervangen en vervolgens op te slaan. Als er een actieve inrichtings cyclus is, wordt deze voltooid en zal de volgende cyclus het bijgewerkte schema gebruiken. De volgende cyclus is ook een eerste cyclus, die elke gebruiker en groep opnieuw evalueert op basis van de nieuwe configuratie. Houd rekening met het volgende wanneer u terugkeert naar een eerdere configuratie:

- Gebruikers worden opnieuw geëvalueerd om te bepalen of ze binnen het bereik moeten zijn. Als de bereik filters zijn gewijzigd, is een gebruiker niet meer in het bereik. deze worden uitgeschakeld. Hoewel dit in de meeste gevallen het gewenste gedrag is, is het mogelijk dat u dit wilt voor komen en dat u de functionaliteit voor het [verwijderen van verwijderde bereiken](./skip-out-of-scope-deletions.md) kunt gebruiken. 
- Als u de inrichtings configuratie wijzigt, wordt de service opnieuw gestart en wordt een [eerste cyclus](./how-provisioning-works.md#provisioning-cycles-initial-and-incremental)geactiveerd.

## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Uw inrichtings configuratie exporteren en importeren met behulp van de Microsoft Graph-API

U kunt de Microsoft Graph-API en de Microsoft Graph Explorer gebruiken om de kenmerk toewijzingen en het schema voor het inrichten van gebruikers te exporteren naar een JSON-bestand en dit weer te importeren in azure AD. U kunt ook de stappen die hier zijn vastgelegd, gebruiken om een back-up van uw inrichtings configuratie te maken.

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Stap 1: de principal-ID van uw inrichtings App Service ophalen (object-ID)

1. Start de [Azure Portal](https://portal.azure.com)en navigeer naar de sectie eigenschappen van uw inrichtings toepassing. Als u bijvoorbeeld uw werkdag wilt exporteren *naar AD User Provisioning toepassings* toewijzing navigeert u naar de sectie eigenschappen van de app.
1. In de sectie eigenschappen van uw inrichtings app kopieert u de GUID-waarde die is gekoppeld aan het veld *object-id* . Deze waarde wordt ook wel de **ServicePrincipalId** van uw app genoemd en wordt gebruikt in Microsoft Graph Explorer-bewerkingen.

   ![App Service Principal-ID van workday](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Stap 2: aanmelden bij Microsoft Graph Explorer

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) starten
1. Klik op de knop Aanmelden met micro soft en meld u aan met Azure AD Global admin of de referenties van de app-beheerder.

    ![Microsoft Graph aanmelden](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Wanneer de aanmelding is geslaagd, worden de gegevens van het gebruikers account in het linkerdeel venster weer gegeven.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Stap 3: de inrichtings taak-ID van de inrichtings-app ophalen

Voer in de Microsoft Graph Explorer de volgende GET-query Vervang [servicePrincipalId] uit met de **servicePrincipalId** geëxtraheerd uit [stap 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

U ontvangt een antwoord zoals hieronder wordt weer gegeven. Kopieer het kenmerk id dat aanwezig is in het antwoord. Deze waarde is de **ProvisioningJobId** en wordt gebruikt voor het ophalen van de onderliggende meta gegevens van het schema.

   [![Inrichtings taak-ID](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>Stap 4: het inrichtings schema downloaden

Voer in de Microsoft Graph Explorer de volgende GET-query uit en vervang [servicePrincipalId] en [ProvisioningJobId] door de ServicePrincipalId en de ProvisioningJobId die in de vorige stappen zijn opgehaald.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Kopieer het JSON-object van het antwoord en sla het op in een bestand om een back-up van het schema te maken.

### <a name="step-5-import-the-provisioning-schema"></a>Stap 5: het inrichtings schema importeren

> [!CAUTION]
> Voer deze stap alleen uit als u het schema voor configuratie wilt wijzigen dat niet kan worden gewijzigd met behulp van de Azure Portal of als u de configuratie wilt herstellen vanuit een eerder back-up van een bestand met een geldig en werk schema.

Configureer in de Microsoft Graph Explorer de volgende PUT-query en vervang [servicePrincipalId] en [ProvisioningJobId] door de ServicePrincipalId en de ProvisioningJobId die in de vorige stappen zijn opgehaald.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Kopieer de inhoud van het JSON-schema bestand op het tabblad aanvraag tekst.

   [![Aanvraagbody](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

Voeg op het tabblad aanvraag headers het kenmerk content-type header toe met de waarde ' application/json '

   [![Aanvraag headers](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Selecteer **query uitvoeren** om het nieuwe schema te importeren.