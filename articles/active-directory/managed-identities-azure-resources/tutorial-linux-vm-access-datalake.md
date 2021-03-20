---
title: Zelfstudie`:` Een beheerde identiteit gebruiken voor toegang tot Azure Data Lake Store - Linux - Azure AD
description: Een zelfstudie die laat zien hoe u toegang krijgt tot Azure Data Lake Storage met een door het Linux-VM-systeem toegewezen beheerde identiteit.
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
ms.date: 01/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d465419dfe36fd5dd67abdef22a6f54fba69a98e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89267459"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Zelfstudie: Een door het Linux-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure Data Lake Store

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Deze zelfstudie laat zien hoe u toegang krijgt tot Azure Data Lake Store met een door het systeem toegewezen beheerde identiteit voor een virtuele Linux-machine (VM). In deze zelfstudie leert u procedures om het volgende te doen: 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Uw virtuele machine toegang verlenen tot Azure Data Lake Storage.
> * Een toegangstoken ophalen met behulp van de door het systeem toegewezen beheerde identiteit voor de Linux-VM om toegang te krijgen tot Azure Data Lake Storage.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-access"></a>Toegang verlenen

In deze sectie leert u hoe u uw VM toegang tot bestanden en mappen in Azure Data Lake Storage kunt verlenen. Voor deze stap kunt u een bestaand Data Lake Storage-exemplaar gebruiken, of een nieuw exemplaar maken. Als u een Data Lake Storage wilt maken met behulp van Azure Portal, volgt u deze [snelstart voor Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md). Er zijn ook snelstarts in de [documentatie over Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md) voor het gebruik van Azure CLI en Azure Powershell.

Maak een nieuwe map in Data Lake Store, en verleen de door het systeem toegewezen beheerde identiteit voor de Linux-VM toestemming voor het lezen, schrijven en uitvoeren van bestanden in die map:

1. Selecteer in Azure Portal **Data Lake Storage** in het linkerdeelvenster.
2. Selecteer het Data Lake Storage-exemplaar dat u wilt gebruiken.
3. Selecteer **Data Explorer** in de opdrachtbalk.
4. De hoofdmap van het Data Lake Storage-exemplaar wordt geselecteerd. Selecteer **Toegang** op de opdrachtbalk.
5. Selecteer **Toevoegen**.  Voer in het vak **Selecteren** de naam van uw virtuele machine in, bijvoorbeeld **DevTestVM**. Selecteer uw virtuele machine in de zoekresultaten en klik op **Selecteren**.
6. Klik op **Machtigingen selecteren**.  Selecteer **Lezen** en **Uitvoeren**, voeg deze toe aan **Deze map** als **Alleen een toegangsmachtiging**. Selecteer **OK**.  De machtiging wordt toegevoegd.
7. Sluit het deelvenster **Toegang**.
8. Voor deze zelfstudie maakt u een nieuwe map. Selecteer **Nieuwe map** in de opdrachtbalk en geef de nieuwe map een naam, bijvoorbeeld **TestFolder**.  Selecteer **OK**.
9. Selecteer de map die u hebt gemaakt en selecteer vervolgens **Toegang** op de opdrachtbalk.
10. Selecteer **Toevoegen**, net als in stap 5. Voer in het vak **Selecteren** de naam van uw virtuele machine in. Selecteer uw virtuele machine in de zoekresultaten en klik op **Selecteren**.
11. Selecteer **Machtigingen selecteren**, net als bij stap 6. Selecteer **Lezen**, **Schrijven** en **Uitvoeren**, voeg deze toe aan **Deze map** als **Een toegangsmachtiging en een standaardmachtiging**. Selecteer **OK**.  De machtiging wordt toegevoegd.

Beheerde identiteiten voor Azure-resources kunnen nu alle bewerkingen uitvoeren op bestanden in de map die u hebt gemaakt. Lees het artikel over [toegangsbeheer in Data Lake Storage](../../data-lake-store/data-lake-store-access-control.md) voor meer informatie over het beheren van de toegang tot Data Lake Storage.

## <a name="get-an-access-token"></a>Een toegangstoken opvragen 

In deze sectie leert u hoe u een toegangstoken kunt downloaden en het bestandssysteem van Data Lake Storage aanroepen. Azure Data Lake Storage biedt systeemeigen ondersteuning voor Azure Active Directory-verificatie, zodat toegangstokens die zijn verkregen met behulp van beheerde identiteiten voor Azure-resources kunnen worden geaccepteerd. Voor verificatie bij het bestandssysteem van Data Lake Storage verzendt u een door Azure AD uitgegeven toegangstoken naar het eindpunt van het Data Lake Storage-bestandssysteem. Het toegangstoken bevindt zich in een autorisatie-header met de notatie Bearer-token \<ACCESS_TOKEN_VALUE\>.  Zie [Verificatie bij Data Lake Storage met Azure Active Directory](../../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md) voor meer informatie over ondersteuning voor Azure AD-verificatie in Data Lake Storage.

In deze zelfstudie gebruikt u cURL voor het maken van REST-aanvragen voor verificatie bij de REST-API van het Data Lake Storage-bestandssysteem.

> [!NOTE]
> Beheerde identiteiten voor Azure-resources worden nog niet ondersteund in de client-SDK's van het Data Lake Storage-bestandssysteem.

U hebt een SSH-client nodig om deze stappen uit te voeren. Als u Windows gebruikt, kunt u de SSH-client in het [Windows-subsysteem voor Linux](/windows/wsl/about) gebruiken. Zie [De sleutels van uw SSH-client gebruiken onder Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) of [Een sleutelpaar met een openbare SSH-sleutel en een privé-sleutel maken en gebruiken voor virtuele Linux-machines in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md) als u hulp nodig hebt bij het configureren van de sleutels van uw SSH-client.

1. Blader in de portal naar uw Linux-VM. Selecteer **Verbinden** in **Overzicht**.  
2. Maak verbinding met de VM met behulp van de SSH-client van uw keuze. 
3. Dien in het terminalvenster met behulp van cURL een aanvraag in bij het lokale eindpunt van beheerde identiteiten voor Azure-resources om een toegangstoken voor het Data Lake Storage-bestandssysteem op te halen. De resource-id voor Data Lake Storage is `https://datalake.azure.net/`.  Het is belangrijk om de afsluitende slash in de resource-id op te nemen.
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -H Metadata:true   
   ```
    
   Een succesvol antwoord retourneert het toegangstoken dat u gebruikt voor verificatie bij Data Lake Storage:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Verzend met behulp van cURL een aanvraag naar het REST-eindpunt van het Data Lake Storage-bestandssysteem om de mappen in de hoofdmap weer te geven. Dit is een eenvoudige manier om te controleren of alles juist is geconfigureerd. Kopieer de waarde van het toegangstoken uit de vorige stap. Het is belangrijk dat de tekenreeks 'Bearer' in de autorisatie-header begint met de hoofdletter 'B'. U vindt de naam van uw Data Lake Storage-exemplaar in de sectie **Overzicht** van het deelvenster **Data Lake Storage** in Azure Portal.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Een geslaagd antwoord ziet er zo uit:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Nu kunt u proberen een bestand te uploaden naar uw Data Lake Storage-exemplaar. Maak eerst een bestand om te uploaden.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Verzend met behulp van cURL een aanvraag naar het REST-eindpunt van uw Data Lake Storage-bestandssysteem om het bestand te uploaden naar de map die u eerder hebt gemaakt. Het uploaden omvat een omleiding en cURL volgt de omleiding automatisch. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Een geslaagd antwoord ziet er zo uit:

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

Met andere API's voor het Data Lake Storage-bestandssysteem kunt u toevoegen aan bestanden, bestanden downloaden, en nog veel meer.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd een door het systeem toegewezen beheerde identiteit voor een virtuele Linux-machine (VM) te gebruiken om toegang tot een Azure Data Lake Store te krijgen. Zie voor meer informatie over Azure Data Lake Storage:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md)