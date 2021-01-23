---
title: Een Cloud service (klassiek) lokaal in de compute-emulator profileren | Microsoft Docs
description: Prestatie problemen in Cloud Services onderzoeken met de Visual Studio Profiler
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2f924d84967c1a1928a47b59fd3a8c28da091130
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743556"
---
# <a name="testing-the-performance-of-a-cloud-service-classic-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>De prestaties van een Cloud service (klassiek) lokaal in de Azure Compute-emulator testen met behulp van de Visual Studio Profiler

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Er zijn verschillende hulpprogram ma's en technieken beschikbaar voor het testen van de prestaties van Cloud Services.
Wanneer u een Cloud service naar Azure publiceert, kunt u met Visual Studio profilerings gegevens verzamelen en deze lokaal analyseren, zoals beschreven in [profileren van een Azure-toepassing][1].
U kunt ook diagnostische gegevens gebruiken voor het bijhouden van verschillende prestatie meter items, zoals beschreven in [prestatie meter items gebruiken in azure][2].
Mogelijk wilt u uw toepassing ook lokaal in de compute-emulator gebruiken voordat u deze implementeert in de Cloud.

Dit artikel heeft betrekking op de CPU-steekproef methode voor profile ring, die lokaal kan worden uitgevoerd in de emulator. CPU-steek proeven zijn een manier om een profileren te doen die niet zeer opvallend is. Bij een aangewezen steekproef interval maakt de Profiler een moment opname van de aanroep stack. De gegevens worden verzameld over een bepaalde tijd en weer gegeven in een rapport. Deze methode van profile ring geeft aan waar in een reken kundige, intensieve toepassing het meren deel van het CPU-werk wordt uitgevoerd.  Zo kunt u zich richten op het ' warme pad ' waar uw toepassing de meeste tijd in de praktijk brengt.

## <a name="1-configure-visual-studio-for-profiling"></a>1: Visual Studio configureren voor profile ring
Eerst zijn er enkele opties voor Visual Studio-configuratie die handig kunnen zijn bij het profileren van een profiel. Om inzicht te hebben in de profilerings rapporten, hebt u symbolen (. PDB-bestanden) nodig voor uw toepassing en ook symbolen voor systeem bibliotheken. U wilt er zeker van zijn dat u naar de beschik bare symbool servers verwijst. U doet dit door in het menu **extra** in Visual Studio **Opties** te kiezen en vervolgens **fout opsporing** en **symbolen** te kiezen. Zorg ervoor dat de micro soft-symbool servers worden weer gegeven onder **symbool bestand (. pdb)-locaties**.  U kunt ook verwijzen naar https://referencesource.microsoft.com/symbols , die mogelijk aanvullende symbool bestanden bevat.

![Symbool opties][4]

Desgewenst kunt u de rapporten die door de Profiler worden gegenereerd, vereenvoudigen door Just My Code in te stellen. Als Just My Code is ingeschakeld, worden aanroep stacks van functies vereenvoudigd, zodat ze volledig intern aan bibliotheken aanroepen en de .NET Framework in de rapporten worden verborgen. Kies **Opties** in het menu **extra** . Vouw vervolgens het knoop punt **prestatie hulpprogramma's** uit en kies **Algemeen**. Schakel het selectie vakje in voor het **inschakelen van Just my code voor Profiler rapporten**.

![Just My Code opties][17]

U kunt deze instructies gebruiken met een bestaand project of met een nieuw project.  Als u een nieuw project maakt om de hieronder beschreven technieken te proberen, kiest u een C# **Azure-Cloud service** project en selecteert u een **Ondernemingsrol** en een **werk rollen**.

![Azure Cloud service-project rollen][5]

U kunt bijvoorbeeld een code toevoegen aan uw project die veel tijd in beslag neemt en een duidelijk beeld van het prestatie probleem illustreren. Voeg bijvoorbeeld de volgende code toe aan het project worker-rol:

```csharp
public class Concatenator
{
    public static string Concatenate(int number)
    {
        int count;
        string s = "";
        for (count = 0; count < number; count++)
        {
            s += "\n" + count.ToString();
        }
        return s;
    }
}
```

Roep deze code aan vanuit de methode RunAsync in de RoleEntryPoint-afgeleide klasse van de rol van de werk nemer. (Negeer de waarschuwing over de methode die synchroon wordt uitgevoerd.)

```csharp
private async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    while (!cancellationToken.IsCancellationRequested)
    {
        Trace.TraceInformation("Working");
        Concatenator.Concatenate(10000);
    }
}
```

Uw Cloud service lokaal bouwen en uitvoeren zonder fouten opsporen (CTRL + F5), waarbij de oplossings configuratie is ingesteld op **release**. Dit zorgt ervoor dat alle bestanden en mappen worden gemaakt om de toepassing lokaal uit te voeren en ervoor te zorgen dat alle emulators worden gestart. Start de gebruikers interface van de compute-emulator vanuit de taak balk om te controleren of uw werknemersrol actief is.

## <a name="2-attach-to-a-process"></a>2: koppelen aan een proces
In plaats van de toepassing te profileren door deze vanuit de Visual Studio 2010 IDE te starten, moet u de Profiler koppelen aan een actief proces. 

Als u de Profiler aan een proces wilt koppelen, kiest u in het menu **analyseren** de optie **Profiler** en **koppelen/loskoppelen**.

![Profiel optie bijvoegen][6]

Zoek het WaWorkerHost.exe proces voor een rol van werk rollen.

![WaWorkerHost-proces][7]

Als uw projectmap zich op een netwerk station bevindt, wordt u door de Profiler gevraagd een andere locatie op te geven voor het opslaan van de profilerings rapporten.

 U kunt ook koppelen aan een ondernemingsrol door te koppelen aan WaIISHost.exe.
Als er meerdere werk rollen worden verwerkt in uw toepassing, moet u de processID gebruiken om ze te onderscheiden. U kunt de processID programmatisch opvragen door het proces object te openen. Als u deze code bijvoorbeeld toevoegt aan de run-methode van de RoleEntryPoint-afgeleide klasse in een rol, kunt u het logboek in de interface van de compute-emulator bekijken om te weten welk proces moet worden gebruikt om verbinding te maken.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Als u het logboek wilt weer geven, start u de gebruikers interface van de compute-emulator.

![De gebruikers interface van Compute emulator starten][8]

Open het venster werk rollen logboek console in de gebruikers interface van de reken emulator door te klikken op de titel balk van het console venster. U kunt de proces-ID bekijken in het logboek.

![Proces-ID weer geven][9]

Als u een koppeling hebt gemaakt, voert u de stappen in de gebruikers interface van uw toepassing (indien nodig) uit om het scenario te reproduceren.

Wanneer u het profileren wilt stoppen, kiest u de koppeling **Profiler stoppen** .

![Optie profile ring stoppen][10]

## <a name="3-view-performance-reports"></a>3: prestatie rapporten weer geven
Het prestatie rapport voor uw toepassing wordt weer gegeven.

Op dit punt stopt de Profiler de uitvoering, worden gegevens opgeslagen in een. VSP-bestand en wordt een rapport weer gegeven met een analyse van deze gegevens.

![Rapport Profiler][11]

Als u string. wstrcpy in het warme pad ziet, klikt u op Just My Code om de weer gave te wijzigen zodat alleen gebruikers code wordt weer gegeven.  Als u teken reeks. concat ziet, drukt u op de knop alle code weer geven.

U ziet de methode en teken reeks voor samen voegen. deze functie neemt een groot deel van de uitvoerings tijd op.

![Analyse van het rapport][12]

Als u de code voor het samen voegen van teken reeksen in dit artikel hebt toegevoegd, ziet u hier een waarschuwing in de Takenlijst. Er wordt ook een waarschuwing weer gegeven dat er sprake is van een buitensporige hoeveelheid Garbage Collection, die wordt veroorzaakt door het aantal teken reeksen dat is gemaakt en verwijderd.

![Prestatie waarschuwingen][14]

## <a name="4-make-changes-and-compare-performance"></a>4: wijzigingen aanbrengen en prestaties vergelijken
U kunt de prestaties ook vergelijken vóór en na een code wijziging.  Stop het actieve proces en bewerk de code om de bewerking voor het samen voegen van teken reeksen te vervangen door het gebruik van string Builder:

```csharp
public static string Concatenate(int number)
{
    int count;
    System.Text.StringBuilder builder = new System.Text.StringBuilder("");
    for (count = 0; count < number; count++)
    {
        builder.Append("\n" + count.ToString());
    }
    return builder.ToString();
}
```

Voer een andere prestaties uit en vergelijkt de prestaties. Als de uitvoeringen zich in dezelfde sessie in de performance Explorer bevinden, kunt u beide rapporten selecteren, het snelmenu openen en **prestatie rapporten vergelijken** kiezen. Als u wilt vergelijken met een uitvoering in een andere prestatie sessie, opent u het menu **analyseren** en kiest u **prestatie rapporten vergelijken**. Geef beide bestanden op in het dialoog venster dat wordt weer gegeven.

![De optie prestatie rapporten vergelijken][15]

De rapporten markeren de verschillen tussen de twee uitvoeringen.

![Vergelijkend rapport][16]

Gefeliciteerd U bent klaar met de Profiler.

## <a name="troubleshooting"></a>Problemen oplossen
* Zorg ervoor dat u een release-build profileert en start zonder fouten opsporen.
* Als de optie koppelen/loskoppelen niet is ingeschakeld in het menu Profiler, voert u de wizard prestaties uit.
* Gebruik de compute emulator-gebruikers interface om de status van uw toepassing weer te geven. 
* Als u problemen ondervindt bij het starten van toepassingen in de emulator of het koppelen van de Profiler, sluit u de compute-emulator af en start u deze opnieuw. Als het probleem hiermee niet is opgelost, probeert u opnieuw op te starten. Dit probleem kan optreden als u de compute-emulator gebruikt voor het onderbreken en verwijderen van implementaties die worden uitgevoerd.
* Als u een van de profilerings opdrachten van de opdracht regel hebt gebruikt, met name de algemene instellingen, moet u ervoor zorgen dat VSPerfClrEnv/globaloff is aangeroepen en dat VsPerfMon.exe is afgesloten.
* Als bij de steek proef het bericht ' PRF0025: er zijn geen gegevens verzameld, ' wordt weer gegeven, controleert u of het proces dat u hebt gekoppeld, een CPU-activiteit heeft. Toepassingen die geen reken werkzaamheden uitvoeren, produceren mogelijk geen steekproef gegevens.  Het is ook mogelijk dat het proces is afgesloten voordat er een steek proef is uitgevoerd. Controleer of de uitvoerings methode voor een rol waarvoor u een profile ring maakt, niet wordt beëindigd.

## <a name="next-steps"></a>Volgende stappen
Het instrumenteren van Azure binaire bestanden in de emulator wordt niet ondersteund in de Visual Studio Profiler, maar als u geheugen toewijzing wilt testen, kunt u die optie kiezen tijdens het profileren. U kunt ook gelijktijdigheids profilering kiezen, waarmee u kunt bepalen of de duur van threads wordt verspild op vergren delingen of laag interactie Profiler, waarmee u prestatie problemen kunt volgen wanneer interactie tussen lagen van een toepassing, het vaakst tussen de gegevenslaag en een werk rollen.  U kunt de database query's weer geven die door uw app worden gegenereerd en gebruikmaken van de profilerings gegevens voor het verbeteren van het gebruik van de data base. Zie het blog bericht [scenario: using the tier interactie Profiler in Visual Studio Team System 2010][3]voor informatie over de profile ring van lagen.

[1]: ../azure-monitor/app/profiler.md
[2]: /previous-versions/azure/hh411542(v=azure.100)
[3]: /archive/blogs/habibh/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png