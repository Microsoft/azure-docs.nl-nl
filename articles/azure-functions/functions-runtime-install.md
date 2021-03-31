---
title: Installatie van Azure Functions-runtime
description: De Azure Functions-runtime Preview 2 installeren
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 73b9533fa00c783e1cfb85270198f5c00c66afd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86540380"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>De Azure Functions-runtime Preview 2 installeren

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Als u de Azure Functions-runtime Preview 2 wilt installeren, voert u de volgende stappen uit:

1. Zorg ervoor dat uw computer voldoet aan de minimale vereisten.
1. Down load het [installatie programma voor de Azure functions-runtime preview](https://aka.ms/azafrv2).
1. Verwijder de Azure Functions-runtime preview 1.
1. Installeer de Azure Functions-runtime Preview 2.
1. Voltooi de configuratie van de Azure Functions-runtime Preview 2.
1. Uw eerste functie maken in Azure Functions-runtime preview

## <a name="prerequisites"></a>Vereisten

Voordat u de preview-versie van Azure Functions-runtime installeert, moet u de volgende resources beschikbaar hebben:

1. Een computer met micro soft Windows Server 2016 of micro soft Windows 10 Crea tors update (Professional of ENTER prise Edition).
1. Een SQL Server exemplaar dat wordt uitgevoerd in uw netwerk.  De vereiste minimum versie van de editie is SQL Server Express.

## <a name="uninstall-previous-version"></a>Vorige versie verwijderen

Als u de Azure Functions-runtime preview eerder hebt geïnstalleerd, moet u de installatie ongedaan maken voordat u de nieuwste versie installeert.  Verwijder de Azure Functions-runtime preview door het programma te verwijderen in het onderdeel Software van Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>De Azure Functions-runtime Preview installeren

Het Azure Functions-runtime preview-installatie programma begeleidt u bij de installatie van het Azure Functions-runtime preview-beheer en de werk rollen.  Het is mogelijk om de rol management en worker op dezelfde computer te installeren.  Bij het toevoegen van meer functie-apps moet u echter meer werk rollen implementeren op extra machines zodat u uw functies kunt schalen op meerdere werk nemers.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>De rol management en worker op dezelfde computer installeren

1. Voer het installatie programma voor de Azure Functions-runtime Preview uit.

    ![Installatie programma voor Azure Functions-runtime preview][1]

1. Klik op **Volgende**.
1. Wanneer u de voor waarden van de **gebruiksrecht overeenkomst** hebt gelezen, **schakelt u het selectie vakje** in om de voor waarden te accepteren en klikt u op **volgende** om door te gaan.
1. Selecteer de functies die u wilt installeren op deze computer **functions Role** en/of **functions worker** en klik op **volgende**.

    ![Preview-versie van Azure Functions-runtime-installatie programma-rol selecteren][3]

    > [!NOTE]
    > U kunt de **functie Workers** op veel andere computers installeren. Volg hiervoor deze instructies en selecteer in het installatie programma alleen **functies werk rollen** .

1. Klik op **volgende** om de installatie van de **wizard Azure functions-runtime** -installatie op uw computer te starten.
1. Zodra de wizard is voltooid, wordt het **Azure functions-runtime** configuratie programma gestart.

    ![Installatie programma voor Azure Functions-runtime preview is voltooid][6]

    > [!NOTE]
    > Als u installeert op **Windows 10** en de **container** functie nog niet is ingeschakeld, wordt u door de **Azure functions-runtime Setup** gevraagd om de computer opnieuw op te starten om de installatie te volt ooien.

## <a name="configure-the-azure-functions-runtime"></a>De Azure Functions-runtime configureren

U moet de configuratie volt ooien om de Azure Functions-runtime-installatie te volt ooien.

1. Het hulp programma **Azure functions-runtime** configuratie laat zien welke rollen op uw computer zijn geïnstalleerd.

    ![Preview-configuratie programma Azure Functions-runtime][7]

1. Klik op het tabblad **Data Base** , voer de verbindings gegevens voor uw SQL Server-exemplaar in, inclusief het opgeven van een [database hoofd sleutel](/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine), en klik op **Toep assen**.  Er is een verbinding met een SQL Server-exemplaar vereist om de Azure Functions-runtime een Data Base te maken voor de ondersteuning van de runtime.

    ![Voorbeeld database configuratie Azure Functions-runtime][8]

1. Klik op het tabblad **referenties** .  Hier moet u twee nieuwe referenties maken voor gebruik met een bestands share voor het hosten van al uw functie-apps.  Geef combi Naties van **gebruikers naam** en **wacht woord** op voor de **Bestands share-eigenaar** en de **Bestands share gebruiker** en klik vervolgens op **Toep assen**.

    ![Preview-referenties Azure Functions-runtime][9]

1. Klik op het tabblad **Bestands share** .  Hier moet u de details van de bestands share locatie opgeven.  U kunt de bestands share voor u maken, maar u kunt ook een bestaande bestands share gebruiken en op **Toep assen** klikken.  Als u een nieuwe locatie voor de bestands share selecteert, moet u een map opgeven die door de Azure Functions-runtime wordt gebruikt.

    ![Preview-bestands share Azure Functions-runtime][10]

1. Klik op het tabblad **IIS** .  Dit tabblad bevat de details van de websites in IIS die worden gemaakt door het Azure Functions-runtime-configuratie hulpprogramma.  U kunt hier een aangepaste DNS-naam opgeven voor de Azure Functions-runtime preview-Portal.  Klik op **Toep assen** om te volt ooien.

    ![Azure Functions-runtime preview-versie van IIS][11]

1. Klik op het tabblad **Services** .  Op dit tabblad wordt de status weer gegeven van de services in uw Azure Functions-runtime-configuratie programma.  Als de  **Azure functions host Activation-Service** niet wordt uitgevoerd na de initiële configuratie, klikt u op **service starten**.

    ![Azure Functions-runtime preview-configuratie voltooid][12]

1. Ga naar de **Azure functions-runtime Portal** als `https://<machinename>.<domain>/` .

    ![Preview-Portal Azure Functions-runtime][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Uw eerste functie maken in Azure Functions-runtime preview

Uw eerste functie maken in Azure Functions-runtime preview

1. Blader bijvoorbeeld naar de **Azure functions-runtime Portal** `https://<machinename>.<domain>` `https://mycomputer.mydomain.com` .

1. U wordt gevraagd om u aan te **melden** als de implementatie in een domein gebruikmaakt van uw gebruikers naam en wacht woord voor het domein account. anders gebruikt u de gebruikers naam en het wacht woord van uw lokale account om u aan te melden bij de portal.

    ![Azure Functions-runtime preview-Portal aanmelden][14]

1. Als u functie-Apps wilt maken, moet u een abonnement maken.  Klik in de linkerbovenhoek van de portal op de **+** optie naast de abonnementen.

    ![Preview-versie van portal-abonnementen Azure Functions-runtime][15]

1. Kies **DefaultPlan**, voer een naam in voor uw abonnement en klik op **maken**.

    ![Azure Functions-runtime preview-abonnement en-naam van de portal][16]

1. Al uw functie-apps worden weer gegeven in het linkerdeel venster van de portal.  Als u een nieuwe functie-app wilt maken, selecteert u de **functie apps** en klikt u op de **+** optie.

1. Voer een naam in voor de functie-app, selecteer het juiste abonnement, Kies welke versie van de Azure Functions runtime u wilt Program meren en klik op **maken**

    ![Nieuwe functie-app voor Azure Functions-runtime preview-Portal][17]

1. De nieuwe functie-app wordt weer gegeven in het linkerdeel venster van de portal.  Selecteer functies en klik boven aan het middelste deel venster van de portal op **nieuwe functie** .

    ![Preview-sjablonen Azure Functions-runtime][18]

1. Selecteer de functie Timer activeren en wijzig de planning in de naam van de rechter muisknop in `*/5 * * * * *` (deze cron-expressie zorgt ervoor dat uw timer functie elke vijf seconden wordt uitgevoerd) en klik op **maken**

    ![Azure Functions-runtime voor beeld van nieuwe configuratie van de timer functie][19]

1. De functie is nu gemaakt.  U kunt het uitvoerings logboek van uw functie-app weer geven door het deel venster **logboek** aan de onderkant van de portal uit te vouwen.

    ![Azure Functions-runtime preview-functie uitvoeren][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
