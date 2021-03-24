---
title: 'Zelfstudie: IntelliJ gebruiken om Azure Spring Cloud-toepassingen te implementeren'
description: IntelliJ gebruiken om toepassingen naar Azure Spring Cloud te implementeren.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 64ff778f9735c690f4622402ef2e3124185fa3ea
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879145"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>IntelliJ gebruiken om Azure Spring Cloud-toepassingen te implementeren

**Dit artikel is van toepassing op:** ✔️ Java

De IntelliJ-invoegtoepassing voor Azure Spring Cloud ondersteunt de implementatie van toepassingen vanuit de IntelliJ IDEA.  

Voordat u dit voorbeeld kunt uitvoeren, kunt u de [eenvoudige quickstart](spring-cloud-quickstart.md) uitproberen.

## <a name="prerequisites"></a>Vereisten
* [IntelliJ IDEA, Community/Ultimate Edition, versie 2020.1/2020.2](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>De invoegtoepassing installeren
U kunt de Azure-toolkit voor IntelliJ IDEA 3.43.0 toevoegen vanuit de IntelliJ-gebruikersinterface voor **invoegtoepassingen**.

1. Start IntelliJ.  Als u eerder een project hebt geopend, sluit u het project om het welkomstdialoogvenster weer te geven. Selecteer **Configureren** vanuit de links rechtsonder en klik vervolgens op **Invoegtoepassingen** om het dialoogvenster voor de configuratie van invoegtoepassingen te openen en selecteer **Invoegtoepassingen vanaf schijf installeren**.

    ![Configure selecteren](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Zoek de Azure-toolkit voor IntelliJ.  Klik op **Install**.

    ![Invoegtoepassing installeren](media/spring-cloud-intellij-howto/install-plugin.png)

1. Klik op **IDE opnieuw opstarten**.

## <a name="tutorial-procedures"></a>Procedures van de zelfstudie
Met de volgende procedures wordt een Hallo wereld-toepassing geïmplementeerd met behulp van de IntelliJ IDEA.

* Het project gs-spring-boot openen
* Implementeren in Azure Spring Cloud
* Streaminglogboeken weergeven

## <a name="open-gs-spring-boot-project"></a>Het project gs-spring-boot openen

1. De bronopslagplaats voor deze zelfstudie downloaden en uitpakken, of deze klonen met Git: git clone https://github.com/spring-guides/gs-spring-boot.git 
1. cd naar gs-spring-boot\complete.
1. Open het dialoogvenster **Welkom** van IntelliJ en selecteer **Project importeren** om de wizard Importeren te openen.
1. Selecteer de map `gs-spring-boot\complete`.

    ![Project importeren](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>Implementeren in Azure Spring Cloud
Als u naar Azure wilt implementeren, moet u zich aanmelden met uw Azure-account en uw abonnement kiezen.  Zie [Installatie en aanmelding](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in) voor meer informatie over aanmelden.

1. Klik met de rechtermuisknop op uw project in de projectverkenner van IntelliJ en selecteer **Azure** -> **Implementeren in Azure Spring Cloud**.

    ![Implementeren in Azure 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. Accepteer de naam voor de app in het veld **Naam**. **Naam** verwijst naar de configuratie, niet naar de naam van de app. Gebruikers hoeven deze doorgaans niet te wijzigen.
1. Accepteer de id van het project voor het **Artefact**.
1. Selecteer **App:** en klik vervolgens op **App maken...** .

    ![Implementeren in Azure 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. Voer **App-naam** in en klik vervolgens op **OK**.

    ![Implementeren naar Azure OK](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. Start de implementatie door op de knop **Uitvoeren** te klikken. 

    ![Implementeren in Azure 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. De invoegtoepassing voert de opdracht `mvn package` uit op het project, maakt de nieuwe app en implementeert de jar die is gegenereerd door de opdracht `package`.

1. Als de app-URL niet wordt weergegeven in het uitvoervenster, haalt u deze op uit de Azure-portal. Navigeer vanuit de resourcegroep naar het exemplaar van Azure Spring Cloud.  Klik vervolgens op **Apps**.  De app die wordt uitgevoerd wordt weergegeven.

    ![Test-URL ophalen](media/spring-cloud-intellij-howto/get-test-url.png)

1. Navigeer naar de URL in een browser.

    ![Navigeren in browser 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>Streaminglogboeken weergeven
De logboeken ophalen:
1. Selecteer **Azure Explorer**  en **Spring Cloud**.
1. Klik met de rechtermuisknop op de app die wordt uitgevoerd.
1. Selecteer **Streaminglogboeken** in de vervolgkeuzelijst.

    ![Streaminglogboeken selecteren](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Selecteer het exemplaar.

    ![Exemplaar selecteren](media/spring-cloud-intellij-howto/select-instance.png)

1. Het streaminglogboek wordt weergegeven in het uitvoervenster.

    ![Uitvoer van streaminglogboek](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>Volgende stappen
* [Spring-toepassing voorbereiden voor Azure Spring Cloud](how-to-prepare-app-deployment.md)
* [Meer informatie over de Azure-toolkit voor IntelliJ](/azure/developer/java/toolkit-for-intellij/)
