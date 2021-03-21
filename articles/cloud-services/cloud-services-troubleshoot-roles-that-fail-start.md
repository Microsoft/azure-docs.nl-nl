---
title: Problemen oplossen met rollen die niet worden gestart | Microsoft Docs
description: Hier volgen enkele veelvoorkomende redenen waarom een Cloud service functie niet kan worden gestart. Er worden ook oplossingen voor deze problemen gegeven.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2453fa2d9b4e78b60d4922e09347799266a84cff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743216"
---
# <a name="troubleshoot-azure-cloud-service-classic-roles-that-fail-to-start"></a>Problemen met de functies van de Azure-Cloud service (klassiek) oplossen die niet worden gestart

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Hier volgen enkele veelvoorkomende problemen en oplossingen met betrekking tot Azure Cloud Services-rollen die niet worden gestart.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Ontbrekende Dll's of afhankelijkheden
Niet-reagerende rollen en rollen die worden uitgevoerd tussen de initialisatie, **bezet** en **het** **stoppen** van de status, kunnen worden veroorzaakt door ontbrekende dll's of assembly's.

Symptomen van ontbrekende Dll's of assembly's kunnen zijn:

* Uw rolinstantie wordt uitgevoerd door de **initialisatie**, **bezet** en de status van het **stoppen** .
* De rolinstantie is verplaatst naar **gereed** , maar als u naar uw webtoepassing navigeert, wordt de pagina niet weer gegeven.

Er zijn verschillende aanbevolen methoden om deze problemen te onderzoeken.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Ontbrekende DLL-problemen in een webfunctie diagnosticeren
Wanneer u navigeert naar een website die is geïmplementeerd in een webrole, en de browser een server fout weergeeft die lijkt op het volgende, kan dit erop duiden dat er een DLL-bestand ontbreekt.

![Server fout in/-toepassing.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Problemen vaststellen door aangepaste fouten uit te scha kelen
Meer informatie over de fout kan worden weer gegeven door de web.config voor de webfunctie te configureren om de aangepaste fout modus in te stellen op uit en de service opnieuw te implementeren.

Meer volledige fouten weer geven zonder Extern bureaublad te gebruiken:

1. Open de oplossing in micro soft Visual Studio.
2. Zoek in het **Solution Explorer** het web.config bestand en open het.
3. Zoek in het web.config-bestand de sectie System. Web en voeg de volgende regel toe:

    ```xml
    <customErrors mode="Off" />
    ```
4. Sla het bestand op.
5. De service opnieuw te verpakken en opnieuw te implementeren.

Zodra de service opnieuw is geïmplementeerd, wordt er een fout bericht weer gegeven met de naam van de ontbrekende assembly of DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Problemen vaststellen door de fout op afstand te bekijken
U kunt Extern bureaublad gebruiken om toegang te krijgen tot de rol en meer informatie over de fout extern te bekijken. Gebruik de volgende stappen om de fouten weer te geven met behulp van Extern bureaublad:

1. Zorg ervoor dat Azure SDK 1,3 of hoger is geïnstalleerd.
2. Schakel tijdens de implementatie van de oplossing met behulp van Visual Studio Extern bureaublad in. Zie [verbinding met extern bureaublad inschakelen voor een rol in Azure Cloud Services met behulp van Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)voor meer informatie.
3. In de Microsoft Azure-portal, zodra het exemplaar de status **gereed**, extern in het exemplaar toont. Zie voor meer informatie over het gebruik van extern bureau blad met Cloud Services op [afstand in rolinstanties](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Meld u aan bij de virtuele machine met behulp van de referenties die zijn opgegeven tijdens de Extern bureaublad configuratie.
6. Open een opdrachtvenster.
7. Typ `IPconfig`.
8. Noteer de waarde van het IPV4-adres.
9. Open Internet Explorer.
10. Typ het adres en de naam van de webtoepassing. Bijvoorbeeld `http://<IPV4 Address>/default.aspx`.

Als u naar de website navigeert, worden er nu meer expliciete fout berichten weer gegeven:

* Server fout in/-toepassing.
* Beschrijving: er is een onverwerkte uitzonde ring opgetreden tijdens het uitvoeren van de huidige webaanvraag. Raadpleeg de stack tracering voor meer informatie over de fout en de herkomst van de-code.
* Details van uitzonde ring: System. IO. FIleNotFoundException: kan bestand of Assembly ' micro soft. WindowsAzure. StorageClient, version = 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35 ' of een van de bijbehorende afhankelijkheden niet laden. Het systeem kan het opgegeven bestand niet vinden.

Bijvoorbeeld:

![Expliciete server fout in/-toepassing](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Problemen vaststellen met behulp van de compute-emulator
U kunt de Microsoft Azure Compute emulator gebruiken om problemen met ontbrekende afhankelijkheden en web.config fouten op te sporen en op te lossen.

Voor de beste resultaten bij het gebruik van deze diagnose methode moet u een computer of virtuele machine gebruiken die een schone installatie van Windows heeft. Gebruik Windows Server 2008 R2 x64 om de Azure-omgeving het beste te simuleren.

1. Installeer de zelfstandige versie van de [Azure SDK](https://azure.microsoft.com/downloads/).
2. Bouw het Cloud service project op de ontwikkel machine.
3. Navigeer in Windows Verkenner naar de map bin\debug van het Cloud service-project.
4. Kopieer de CSX-map en het cscfg-bestand naar de computer die u gebruikt om de problemen op te lossen.
5. Open op de schone computer een Azure SDK-opdracht prompt venster en typ `csrun.exe /devstore:start` .
6. Typ bij de opdracht prompt `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser` .
7. Wanneer de rol wordt gestart, worden gedetailleerde fout gegevens weer geven in Internet Explorer. U kunt ook de standaard hulpprogram ma's voor probleem oplossing van Windows gebruiken om het probleem verder te onderzoeken.

## <a name="diagnose-issues-by-using-intellitrace"></a>Problemen vaststellen met behulp van IntelliTrace
Voor werk nemers en webrollen die gebruikmaken van .NET Framework 4, kunt u [IntelliTrace](/visualstudio/debugger/intellitrace)gebruiken, dat beschikbaar is in micro soft Visual Studio Enter prise.

Voer de volgende stappen uit om de service te implementeren met IntelliTrace ingeschakeld:

1. Controleer of Azure SDK 1,3 of hoger is geïnstalleerd.
2. Implementeer de oplossing met behulp van Visual Studio. Schakel tijdens de implementatie het selectie vakje **IntelliTrace inschakelen voor .net 4-rollen** in.
3. Zodra het exemplaar is gestart, opent u het **Server Explorer**.
4. Vouw het knoop punt **Azure \\ Cloud Services** uit en zoek de implementatie.
5. Vouw de implementatie uit totdat u de rolinstanties ziet. Klik met de rechter muisknop op een van de exemplaren.
6. Kies **IntelliTrace-logboeken weer geven**. De **IntelliTrace-samen vatting** wordt geopend.
7. Zoek de sectie uitzonde ringen van de samen vatting. Als er uitzonde ringen zijn, wordt de sectie **uitzonderings gegevens** genoemd.
8. Vouw de **uitzonderings gegevens** uit en zoek naar **System. io. FileNotFoundException** -fouten die vergelijkbaar zijn met het volgende:

![Uitzonderings gegevens, ontbrekend bestand of assembly](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Ontbrekende Dll's en assembly's in adres
Ga als volgt te werk om ontbrekende DLL-en assembly fouten op te lossen:

1. Open de oplossing in Visual Studio.
2. Open de map **References** in **Solution Explorer**.
3. Klik op de assembly die in de fout is geïdentificeerd.
4. Zoek in het deel venster **Eigenschappen** naar **lokale eigenschap kopiëren** en stel de waarde in op **waar**.
5. Implementeer de Cloud service opnieuw.

Wanneer u hebt gecontroleerd of alle fouten zijn gecorrigeerd, kunt u de service implementeren zonder het selectie vakje **IntelliTrace inschakelen voor .net 4-rollen** in te scha kelen.

## <a name="next-steps"></a>Volgende stappen
Bekijk meer [artikelen over probleem oplossing](../index.yml?product=cloud-services&tag=top-support-issue) voor Cloud Services.

Voor informatie over het oplossen van problemen met Cloud service rollen met behulp van Azure PaaS computer diagnostische gegevens raadpleegt u [de blog serie van Kevin Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
