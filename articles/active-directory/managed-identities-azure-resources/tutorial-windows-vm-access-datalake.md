---
title: Zelfstudie`:` Een beheerde identiteit gebruiken voor toegang tot Azure Data Lake Store - Windows - Azure AD
description: Een zelfstudie die laat zien hoe u toegang krijgt tot Azure Data Lake Storage met een door het Windows-VM-systeem toegewezen beheerde identiteit.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 908fb1ac869ec2b22085af2e07ced6ff64229308
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592480"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Zelfstudie: Een door het Windows-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure Data Lake Store

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Deze zelfstudie laat zien hoe u toegang krijgt tot een Azure Data Lake Store met een door het systeem toegewezen beheerde identiteit voor een virtuele Windows-machine (VM). Managed Service Identity's worden automatisch beheerd in Azure en stellen u in staat om te verifiëren bij services die Azure AD-verificatie ondersteunen, zonder referenties in code te hoeven invoegen. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Uw virtuele machine toegang verlenen tot Azure Data Lake Storage
> * Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine en dat token gebruiken om toegang te krijgen tot Azure Data Lake Storage

## <a name="prerequisites"></a>Vereisten

- Inzicht in beheerde identiteiten. Als u niet bekend bent met de functie voor beheerde identiteiten voor Azure-resources, raadpleegt u dit [overzicht](overview.md). 
- Een Azure-account, [meld u aan voor een gratis account](https://azure.microsoft.com/free/).
- 'Eigenaar'-machtigingen voor het juiste bereik (uw abonnement of resourcegroep) om de vereiste stappen voor resource-aanmaak en rolbeheer uit te voeren. Voor hulp bij roltoewijzing gaat u naar [Op rollen gebaseerd toegangsbeheer gebruiken voor het beheer van de toegang tot de resources van uw Azure-abonnement](../../role-based-access-control/role-assignments-portal.md).
- U hebt ook een virtuele Windows-machine nodig waarvoor door het systeem toegewezen beheerde identiteiten zijn ingeschakeld.
  - Als u voor deze zelfstudie een virtuele machine moet maken, kunt u dat doen met behulp van het artikel [Een virtuele machine maken waarvoor door het systeem toegewezen identiteiten ingeschakeld zijn](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)




## <a name="enable"></a>Inschakelen

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Toegang verlenen

U kunt nu uw virtuele machine toegang tot bestanden en mappen in Azure Data Lake Storage verlenen.  Voor deze stap kunt u een bestaande Data Lake Storage gebruiken, of een nieuwe maken.  Als u een nieuwe Data Lake Storage wilt maken met behulp van Azure Portal, volgt u deze [snelstart voor Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md). Er zijn ook snelstarts in de [documentatie over Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md) voor het gebruik van Azure CLI en Azure Powershell.

Maak een nieuwe map in uw Data Lake Storage, en verleen de door het systeem toegewezen identiteit van uw VM toestemming voor het lezen, schrijven en uitvoeren van bestanden in die map:

1. Klik in Azure Portal op **Data Lake Storage** in het linkernavigatievenster.
2. Klik op de Data Lake Storage die u wilt gebruiken voor deze zelfstudie.
3. Klik op **Data Explorer** in de opdrachtbalk.
4. De hoofdmap van de Data Lake Storage is geselecteerd.  Klik op **Toegang** in de opdrachtbalk.
5. Klik op **Add**.  Voer in het veld **Selecteren** de naam van uw virtuele machine in, bijvoorbeeld **DevTestVM**.  Klik op uw virtuele machine in de zoekresultaten om deze te selecteren, en klik vervolgens op **Selecteren**.
6. Klik op **Machtigingen selecteren**.  Selecteer **Lezen** en **Uitvoeren**, voeg deze toe aan **Deze map** als **Alleen een toegangsmachtiging**.  Klik op **OK**.  De machtiging wordt toegevoegd.
7. Sluit de blade **Toegang**.
8. Voor deze zelfstudie maakt u een nieuwe map.  Klik op **Nieuwe map** in de opdrachtbalk en geef een naam voor de nieuwe map op, bijvoorbeeld **TestFolder**.  Klik op **OK**.
9. Klik op de map die u hebt gemaakt en klik vervolgens op **Toegang** in de opdrachtbalk.
10. Klik, net zoals bij stap 5, op **Toevoegen**, voer de naam van uw virtuele machine in het veld **Selecteren** in, selecteer deze, en klik op **Selecteren**.
11. Klik, net zoals bij stap 6, op **Machtigingen selecteren**, selecteer **Lezen**, **Schrijven** en **Uitvoeren**, voeg deze toe aan **Deze map** als **Een vermelding van een toegangsmachtiging en een vermelding van een standaardmachtiging**.  Klik op **OK**.  De machtiging wordt toegevoegd.

De door het systeem toegewezen beheerde identiteit van uw virtuele machine kan nu alle bewerkingen uitvoeren op bestanden in de map die u hebt gemaakt.  Lees dit artikel over [toegangsbeheer in Data Lake Storage](../../data-lake-store/data-lake-store-access-control.md) voor meer informatie over het beheren van de toegang tot Data Lake Storage.

## <a name="access-data"></a>Toegang tot gegevens

Azure Data Lake Storage biedt systeemeigen ondersteuning voor Azure Active Directory-verificatie, zodat toegangstokens die zijn verkregen met behulp van beheerde identiteiten voor Azure-resources kunnen worden geaccepteerd.  Om te verifiëren bij het bestandssysteem van Data Lake Storage verzendt u een toegangstoken dat door Azure AD is uitgegeven aan het eindpunt van uw Data Lake Storage-bestandssysteem, in een autorisatie-header in de indeling 'Bearer <WAARDE_VAN_TOEGANGSTOKEN>'.  Lees [Verificatie bij Data Lake Storage met Azure Active Directory](../../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md) voor meer informatie over ondersteuning voor Azure AD-verificatie in Data Lake Storage

> [!NOTE]
> Beheerde identiteiten voor Azure-resources worden nog niet ondersteund in de client-SDK's van het Data Lake Storage-bestandssysteem.  Deze zelfstudie wordt bijgewerkt zodra ondersteuning voor MSI wordt toegevoegd aan de SDK.

In deze zelfstudie gebruikt u PowerShell voor het maken van REST-aanvragen om te verifiëren bij de REST-API van het Data Lake Storage-bestandssysteem. Als u de door het systeem toegewezen beheerde identiteit van de virtuele machine wilt gebruiken voor verificatie, moet u de aanvragen verzenden vanaf de virtuele machine.

1. Navigeer in Azure Portal naar **Virtuele machines**, ga naar uw virtuele Windows-machine, en klik op de pagina **Overzicht** op **Verbinden**.
2. Voer uw referenties (**gebruikersnaam** en **wachtwoord**) in die u hebt toegevoegd bij het maken van de virtuele Windows-machine. 
3. Nu u een **Verbinding met extern bureaublad** met de virtuele machine hebt gemaakt, opent u **PowerShell** in de externe sessie. 
4. Gebruik de cmdlet `Invoke-WebRequest` van Powershell om een aanvraag te versturen naar de lokale beheerde identiteiten om een toegangstoken voor Azure Data Lake Store op te halen voor het Azure-resources-eindpunt.  De resource-id voor Data Lake Storage is `https://datalake.azure.net/`.  Voor Data Lake moet u een exact overeenkomende resource-id opgeven, met de schuine streep op het einde.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -Method GET -Headers @{Metadata="true"}
   ```
    
   Converteer de reactie van een JSON-object naar een PowerShell-object. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extraheer het toegangstoken uit de reactie.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Gebruik in PowerShell de Invoke-WebRequest-opdracht om een aanvraag te verzenden naar het REST-eindpunt van Data Lake Storage om de mappen in de hoofdmap weer te geven.  Dit is een eenvoudige manier om te controleren of alles juist is geconfigureerd.  Het is belangrijk dat de tekenreeks 'Bearer' in de autorisatie-header begint met de hoofdletter 'B'.  U vindt de naam van uw Data Lake Storage in de sectie **Overzicht** van de Data Lake Storage-blade in Azure Portal.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   Een geslaagde reactie ziet er als volgt uit:

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. Nu kunt u proberen een bestand te uploaden naar uw Data Lake Storage.  Maak eerst een bestand om te uploaden.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. Verzend met behulp van `Invoke-WebRequest` in PowerShell een aanvraag naar het REST-eindpunt van uw Data Lake Storage voor het uploaden van het bestand naar de map die u eerder hebt gemaakt.  Deze aanvraag bestaat uit twee stappen.  In de eerste stap verzendt u een aanvraag haalt u omleidingsgegevens op zodat u weet waarnaar het bestand moet worden geüpload.  In de tweede stap uploadt u het bestand.  Vergeet niet de naam van de map en het bestand aan te passen als u andere waarden gebruikt dan die in deze zelfstudie. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Als u de waarde van `$HdfsRedirectResponse` inspecteert, ziet er ongeveer als volgt uit:

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   Voltooi het uploaden door een aanvraag te verzenden naar het omleidingseindpunt:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Een geslaagde reactie ziet er ongeveer als volgt uit:

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

Met andere API's van het Data Lake Storage-bestandssysteem kunt u toevoegen aan bestanden, bestanden downloaden, en nog veel meer.


## <a name="disable"></a>Uitschakelen

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u toegang krijgt tot een Azure Data Lake Store met behulp van een door het systeem toegewezen beheerde identiteit voor een virtuele Windows-machine. Zie voor meer informatie over Azure Data Lake Storage:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md)