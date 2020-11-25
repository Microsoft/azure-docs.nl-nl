---
title: Verwijdering van gebruikers buiten het bereik overs Laan
description: Meer informatie over het negeren van het standaard gedrag van het ongedaan maken van de inrichting van de gebruikers van het bereik.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 12/10/2019
ms.author: kenwith
ms.reviewer: celested
ms.openlocfilehash: f459a804b4c375eea17cbc22ded2f41f808c1b82
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995366"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Verwijdering van gebruikers accounts die buiten het bereik vallen, overs Laan

De Azure AD Provisioning-engine laadt standaard gebruikers die buiten het bereik vallen, of schakelt deze uit. Voor bepaalde scenario's zoals workday voor inrichtende AD-gebruikers is dit gedrag mogelijk niet de verwachte en wilt u dit standaard gedrag negeren.  

In dit artikel wordt beschreven hoe u de Microsoft Graph-API en de Microsoft Graph API Explorer kunt gebruiken om de vlag ***SkipOutOfScopeDeletions** _ in te stellen voor de verwerking van accounts die buiten het bereik vallen. _ Als ***SkipOutOfScopeDeletions** _ is ingesteld op 0 (ONWAAR), worden accounts die buiten het bereik vallen, uitgeschakeld in het doel.
_ Als ***SkipOutOfScopeDeletions** _ is ingesteld op 1 (waar), worden accounts die zich buiten het bereik bevinden niet in het doel uitgeschakeld. Deze markering wordt ingesteld op het niveau van de app _Provisioning * en kan worden geconfigureerd met behulp van de Graph API. 

Omdat deze configuratie veel wordt gebruikt in combi natie met de *werkdag voor het Active Directory* van de app voor het inrichten van gebruikers, bevatten de volgende stappen scherm opnamen van de werkdag-toepassing. De configuratie kan echter ook worden gebruikt met *alle andere apps*, zoals ServiceNow, Sales Force en Dropbox.

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Stap 1: de principal-ID van uw inrichtings App Service ophalen (object-ID)

1. Start de [Azure Portal](https://portal.azure.com)en navigeer naar de sectie eigenschappen van uw inrichtings toepassing. Bijvoorbeeld, als u uw werkdag wilt exporteren *naar AD User Provisioning toepassings* toewijzing, navigeert u naar de sectie eigenschappen van de app. 
1. In de sectie eigenschappen van uw inrichtings app kopieert u de GUID-waarde die is gekoppeld aan het veld *object-id* . Deze waarde wordt ook wel de **ServicePrincipalId** van uw app genoemd en wordt gebruikt in Graph Explorer-bewerkingen.

   ![App Service Principal-ID van workday](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Stap 2: aanmelden bij Microsoft Graph Explorer

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) starten
1. Klik op de knop Aanmelden met micro soft en meld u aan met Azure AD Global admin of de referenties van de app-beheerder.

    ![Graph-aanmelding](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. Wanneer de aanmelding is geslaagd, worden de gegevens van het gebruikers account in het linkerdeel venster weer gegeven.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Stap 3: de bestaande app-referenties en connectiviteits gegevens ophalen

Voer in de Microsoft Graph Explorer de volgende GET-query Vervang [servicePrincipalId] uit met de **servicePrincipalId** geëxtraheerd uit [stap 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![Taak query ophalen](./media/skip-out-of-scope-deletions/skip-03.png)

Kopieer het antwoord naar een tekst bestand. Deze ziet eruit als de JSON-tekst die hieronder wordt weer gegeven, met waarden die geel zijn gemarkeerd voor uw implementatie. Voeg de regels die zijn gemarkeerd in groen toe aan het einde en werk het wacht woord voor workday-verbindingen in op blauw gemarkeerd. 

   ![Taak respons ophalen](./media/skip-out-of-scope-deletions/skip-04.png)

Dit is het JSON-blok dat aan de toewijzing moet worden toegevoegd. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Stap 4: het geheimen-eind punt bijwerken met de vlag SkipOutOfScopeDeletions

Voer in de Graph Explorer de onderstaande opdracht uit om het geheimen-eind punt bij te werken met de vlag **_SkipOutOfScopeDeletions_* _. 

Vervang in de onderstaande URL [servicePrincipalId] door de _ *servicePrincipalId** geëxtraheerd uit [stap 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id). 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Kopieer de bijgewerkte tekst uit stap 3 naar de ' hoofd tekst van de aanvraag ' en stel de header ' content-type ' in ' application/json ' in ' aanvraag headers ' in. 

   ![Aanvraag plaatsen](./media/skip-out-of-scope-deletions/skip-05.png)

Klik op query uitvoeren. 

U moet de uitvoer als ' geslaagd – status code 204 ' ophalen. 

   ![Antwoord plaatsen](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Stap 5: controleren of gebruikers buiten het bereik niet worden uitgeschakeld

U kunt deze vlag testen op het verwachte gedrag door de scope regels bij te werken om een specifieke gebruiker over te slaan. In het onderstaande voor beeld wordt de werk nemer met ID 21173 (die eerder in bereik was) uitgesloten door een nieuwe scope regel toe te voegen: 

   ![Scherm afbeelding met de sectie bereik filter toevoegen, met een voor beeld van een gebruiker gemarkeerd.](./media/skip-out-of-scope-deletions/skip-07.png)

In de volgende inrichtings cyclus identificeert de Azure AD-inrichtings service dat de gebruiker 21173 zich buiten het bereik bevindt. als de eigenschap SkipOutOfScopeDeletions is ingeschakeld, wordt in de synchronisatie regel voor die gebruiker een bericht weer gegeven zoals hieronder wordt weer gegeven: 

   ![Voor beeld van scoping](./media/skip-out-of-scope-deletions/skip-08.png)


