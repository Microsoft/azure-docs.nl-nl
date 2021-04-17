---
title: Zelfstudie voor rapportage-API van Azure AD met certificaten | Microsoft Docs
description: In deze zelfstudie wordt uitgelegd hoe u de rapportage-API van Azure AD gebruikt met certificaatreferenties om zonder tussenkomst van de gebruiker gegevens op te halen uit directory's.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: c4f4ac6ab3825c82ac0cb8ef5c31f9396ef7b41f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533738"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Zelfstudie: Gegevens ophalen met de Azure Active Directory rapportage-API met certificaten

De [Azure Active Directory (Azure AD) rapportage-API's](concept-reporting-api.md) bieden toegang tot de gegevens op programmeerniveau via een set op REST-gebaseerde API's. U kunt deze API's vanuit een groot aantal computertalen en hulpprogramma's aanroepen. Als u toegang wilt tot de Azure Active Directory Reporting API zonder tussenkomst van de gebruiker, moet u uw toegang tot het gebruik van certificaten configureren.

In deze zelfstudie leert u hoe u een testcertificaat kunt gebruiken om toegang te krijgen tot de MS Graph API voor rapportage. Het gebruik van testcertificaten in een productieomgeving wordt niet aanbevolen. 

## <a name="prerequisites"></a>Vereisten

1. Zorg ervoor dat u over een Azure Active Directory-tenant met een Premium-licentie (P1/P2) beschikt om toegang tot aanmeldingsgegevens te krijgen. Zie [Aan de slag met Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) om uw versie van Azure Active Directory te upgraden. Als er vóór de upgrade nog geen activiteitgegevens waren, duurt het na het upgraden naar een premium-licentie enkele dagen voordat er gegevens worden weergegeven in de rapporten. 

2. Maak of schakel naar een gebruikersaccount met de rol **Globale beheerder**, **Beveiligingsbeheerder**, **Beveiligingslezer** of **Rapportlezer** voor de tenant. 

3. Voltooi de [vereisten voor toegang tot de Azure Active Directory-rapportage API](howto-configure-prerequisites-for-reporting-api.md). 

4. Download en installeer [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Installeer [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Deze module biedt verschillende cmdlets, waaronder:
    - De ADAL-bibliotheken die nodig zijn voor verificatie
    - Toegangstokens van gebruiker, toepassingssleutels en certificaten met behulp van ADAL
    - Afhandeling van pagina's met zoekresultaten door Graph API

6. Als dit de eerste keer is dat u de module-uitvoering **Install-MSCloudIdUtilsModule** gebruikt, kunt u deze ook importeren met behulp van de PowerShell-opdracht **Import-Module**. De sessie moet er ongeveer uitzien zoals op dit scherm: ![Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Gebruik de PowerShell-commandlet **New-SelfSignedCertificate** om een testcertificaat te maken.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Gebruik de commandlet **Export-Certificate** om het te exporteren naar een certificaatbestand.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Gegevens ophalen met de Azure Active Directory rapportage-API met certificaten

1. Ga naar de [Azure Portal](https://portal.azure.com), selecteer **Azure Active Directory**, selecteer **App-registraties** en kies uw toepassing in de lijst. 

2. Selecteer **Certificaten en geheimen** onder **Beheren** op de blade Toepassingsregistratie en selecteer **Certificaat uploaden**.

3. Selecteer het certificaatbestand van de vorige stap en selecteer **Toevoegen**. 

4. Noteer de toepassings-id en de vingerafdruk van het certificaat dat u zojuist hebt geregistreerd bij uw toepassing. Als u de vingerafdruk wilt vinden, gaat u op de toepassingspagina in de portal naar **Certificaten en geheimen** onder het gedeelte **Beheren**. De vingerafdruk wordt weergegeven in de lijst **Certificaten**.

5. Open het manifest van de toepassing in de inline manifest-editor en controleer of de eigenschap *keyCredentials* wordt bijgewerkt met de nieuwe certificaatgegevens, zoals hieronder wordt weergegeven. 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ``` 
6. U kunt nu een toegangstoken verkrijgen voor de MS Graph API met behulp van dit certificaat. Gebruik de cmdlet **Get-MSCloudIdMSGraphAccessTokenFromCert** uit de MSCloudIdUtils PowerShell-module, waarbij u de toepassings-id en de vingerafdruk doorgeeft die u hebt verkregen uit de vorige stap. 

   ![Schermafbeelding van een PowerShell-venster met een opdracht die een toegangstoken aanmaakt.](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

7. Gebruik het toegangstoken in uw PowerShell-script om query's uit te voeren op de Graph API. Gebruik de cmdlet **Invoke-MSCloudIdMSGraphQuery** van de MSCloudIDUtils om de aanmeldingen en het directoryAudits-eindpunt te inventariseren. Met deze cmdlet worden meerdere pagina's met zoekresultaten verwerkt, waarna deze resultaten naar de PowerShell-pijplijn worden verstuurd.

8. Zoek het directoryAudits-eindpunt op om de auditlogboeken op te halen. 

   ![Schermafbeelding van een PowerShell-venster met een opdracht om een query uit te voeren op het directoryAudits-eindpunt met behulp van de toegangstoken van eerder in deze procedure.](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

9. Zoek het eindpunt voor aanmeldingen op om de aanmeldingslogboeken op te halen.

    ![Schermafbeelding van een PowerShell-venster met een opdracht om een query uit te voeren op het eindpunt voor aanmeldingen met behulp van de toegangtoken van eerder in deze procedure.](./media/tutorial-access-api-with-certificates/query-signins.png)

10. U kunt er nu voor kiezen om deze gegevens te exporteren naar een CSV en op te slaan in een SIEM-systeem. U kunt uw script ook verpakken in een geplande taak om periodiek gegevens van Azure AD op te halen uit uw tenant zonder dat u toepassingssleutels hoeft op te slaan in de broncode. 

## <a name="next-steps"></a>Volgende stappen

* [Een eerste indruk van de rapportage-API 's krijgen](concept-reporting-api.md)
* [Audit API-verwijzing](/graph/api/resources/directoryaudit) 
* [Verwijzing naar rapport-API voor aanmeldingsactiviteit](/graph/api/resources/signin)