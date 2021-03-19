---
title: Verificatie van de eind gebruiker-Java met Data Lake Storage Gen1-Azure
description: Meer informatie over het bezorgen van de verificatie van eind gebruikers met Azure Data Lake Storage Gen1 met behulp van Azure Active Directory met Java
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: ece996f7edb8d37cea47756c5b7e635e02409b35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87318858"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Verificatie door eind gebruikers met Azure Data Lake Storage Gen1 met behulp van Java
> [!div class="op_single_selector"]
> * [Java gebruiken](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK gebruiken](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python gebruiken](data-lake-store-end-user-authenticate-python.md)
> * [REST-API gebruiken](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

In dit artikel leert u hoe u de Java SDK kunt gebruiken om verificatie door eind gebruikers uit te voeren met Azure Data Lake Storage Gen1. Zie [service-to-service-verificatie met data Lake Storage gen1 met behulp van Java](data-lake-store-service-to-service-authenticate-java.md)voor service-naar-service verificatie met data Lake Storage gen1 met behulp van Java SDK.

## <a name="prerequisites"></a>Vereisten
* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Maak een Azure Active Directory systeem eigen toepassing**. U moet de stappen in de [verificatie van eind gebruikers hebben voltooid met data Lake Storage gen1 met behulp van Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). In deze zelfstudie wordt Maven gebruikt voor build- en projectafhankelijkheden. Hoewel het mogelijk is te ontwikkelen zonder een buildsysteem als Maven of Gradle, maken deze systemen het veel eenvoudiger om afhankelijkheden te beheren.

* (Optioneel) Een IDE zoals [IntelliJ IDEA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) of vergelijkbaar.

## <a name="end-user-authentication"></a>Verificatie van de eindgebruiker
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
   
    De eerste afhankelijkheid is het gebruik van de Data Lake Storage Gen1 SDK ( `azure-data-lake-store-sdk` ) in de Maven-opslag plaats. De tweede afhankelijkheid dient om op te geven welk framework voor logboekregistratie (`slf4j-nop`) voor deze toepassing moet worden gebruikt. De Data Lake Storage Gen1 SDK gebruikt [SLF4J](https://www.slf4j.org/) logging gevel, waarmee u kunt kiezen uit een aantal populaire logging-frameworks, zoals Log4j, Java-logboek registratie, logback, enzovoort, of geen logboek registratie. In dit voorbeeld wordt logboekregistratie uitgeschakeld. Daarom wordt de binding **slf4j-nop** gebruikt. [Hier](https://www.slf4j.org/manual.html#projectDep) vindt u andere opties voor logboekregistratie voor uw toepassing.

3. Voeg de volgende importinstructies toe aan uw toepassing.

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;
    ```

4. Gebruik het volgende code fragment in uw Java-toepassing om het token te verkrijgen voor de Active Directory systeem eigen toepassing die u eerder hebt gemaakt met behulp van de `DeviceCodeTokenProvider` . Vervang **Fill-in-hier** door de werkelijke waarden voor de Azure Active Directory systeem eigen toepassing.

    ```java
    private static String nativeAppId = "FILL-IN-HERE";
            
    AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   
    ```

De Data Lake Storage Gen1 SDK biedt handige methoden waarmee u de beveiligings tokens kunt beheren die nodig zijn om te communiceren met het Data Lake Storage Gen1-account. Dit zijn echter niet de enige methoden die met SDK kunnen worden gebruikt. U kunt elke andere methode voor het verkrijgen van een token gebruiken. Zo kunt u de [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) gebruiken, of uw persoonlijke code.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u verificatie van eind gebruikers kunt gebruiken om te verifiëren met Azure Data Lake Storage Gen1 met behulp van Java SDK. U kunt nu de volgende artikelen bekijken over het gebruik van de Java-SDK om met Azure Data Lake Storage Gen1 te werken.

* [Gegevens bewerkingen op Data Lake Storage Gen1 met behulp van Java SDK](data-lake-store-get-started-java-sdk.md)


