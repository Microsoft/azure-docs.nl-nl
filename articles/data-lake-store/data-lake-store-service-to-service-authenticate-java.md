---
title: Service-naar-service-verificatie-Data Lake Storage Gen1 – Java SDK
description: Meer informatie over service-to-service verificatie met Azure Data Lake Storage Gen1 met behulp van Azure Active Directory met Java
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: 0e320557a7372af6a41038d9b3196db23d2496c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000382"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Service-naar-service verificatie met Azure Data Lake Storage Gen1 met behulp van Java

> [!div class="op_single_selector"]
> * [Java gebruiken](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK gebruiken](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python gebruiken](data-lake-store-service-to-service-authenticate-python.md)
> * [REST-API gebruiken](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

In dit artikel leert u hoe u de Java SDK gebruikt om service-to-service-verificatie met Azure Data Lake Storage Gen1 uit te voeren. Verificatie van eind gebruikers met Data Lake Storage Gen1 met behulp van Java SDK wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Een Azure Active Directory Web-toepassing maken**. U moet de stappen in [service-to-service-verificatie met data Lake Storage gen1 met behulp van Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md)hebben voltooid.

* [Maven](https://maven.apache.org/install.html). In deze zelfstudie wordt Maven gebruikt voor build- en projectafhankelijkheden. Hoewel het mogelijk is om te bouwen zonder een build-systeem zoals Maven of Gradle, maken deze systemen het veel eenvoudiger om afhankelijkheden te beheren.

* Beschrijving Een IDE zoals [IntelliJ-idee](https://www.jetbrains.com/idea/download/) of- [eclips](https://www.eclipse.org/downloads/) of vergelijk bare.

## <a name="service-to-service-authentication"></a>Verificatie van service-tot-service

1. Maak een Maven-project met behulp van [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) vanaf de opdrachtregel of met behulp van een IDE. [Hier](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html) vindt u instructies over het maken van een Java-project met behulp van IntelliJ. [Hier](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm) vindt u instructies over het maken van een Java-project met behulp van Eclipse.

2. Voeg de volgende afhankelijkheden toe aan het **pom.xml**-bestand in Maven. Voeg het volgende fragment toe vóór de **\</project>** Tag:

    ```xml
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-data-lake-store-sdk</artifactId>
        <version>2.2.3</version>
      </dependency>
      <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.21</version>
      </dependency>
    </dependencies>
    ```

    De eerste afhankelijkheid is het gebruik van de Data Lake Storage Gen1 SDK ( `azure-data-lake-store-sdk` ) in de Maven-opslag plaats. De tweede afhankelijkheid dient om op te geven welk framework voor logboekregistratie (`slf4j-nop`) voor deze toepassing moet worden gebruikt. De Data Lake Storage Gen1 SDK gebruikt [slf4j](https://www.slf4j.org/) logging gevel, waarmee u kunt kiezen uit een aantal populaire logging-frameworks, zoals Log4j, Java-logboek registratie, logback, enzovoort, of geen logboek registratie. In dit voorbeeld wordt logboekregistratie uitgeschakeld. Daarom wordt de binding **slf4j-nop** gebruikt. [Hier](https://www.slf4j.org/manual.html#projectDep) vindt u andere opties voor logboekregistratie voor uw toepassing.

3. Voeg de volgende importinstructies toe aan uw toepassing.

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;
    ```

4. Gebruik het volgende code fragment in uw Java-toepassing om het token te verkrijgen voor de Active Directory-webtoepassing die u eerder hebt gemaakt met behulp van een van de subklassen van `AccessTokenProvider` (het volgende voor beeld `ClientCredsTokenProvider` ). De tokenprovider slaat de referenties die worden gebruikt voor het ophalen van het token, op in de cache en vernieuwt het token automatisch als het bijna is verlopen. Het is mogelijk om uw eigen subklassen te maken `AccessTokenProvider` , zodat er tokens worden verkregen door de code van uw klant. We gebruiken nu alleen de versie die in de SDK is opgenomen.

    Vervang **FILL-IN-HERE** met de daadwerkelijke waarden voor de Azure Active Directory-webtoepassing.

    ```java
    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   
    ```

De Data Lake Storage Gen1 SDK biedt handige methoden waarmee u de beveiligings tokens kunt beheren die nodig zijn om te communiceren met het Data Lake Storage Gen1-account. Dit zijn echter niet de enige methoden die met SDK kunnen worden gebruikt. U kunt elke andere methode voor het verkrijgen van een token gebruiken. Zo kunt u de [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) gebruiken, of uw persoonlijke code.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u verificatie van eind gebruikers kunt gebruiken om te verifiëren met Data Lake Storage Gen1 met behulp van Java SDK. U kunt nu de volgende artikelen bekijken over het gebruik van de Java-SDK om met Data Lake Storage Gen1 te werken.

* [Gegevens bewerkingen op Data Lake Storage Gen1 met behulp van Java SDK](data-lake-store-get-started-java-sdk.md)
