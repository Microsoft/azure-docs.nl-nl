---
title: U-SQL-taken lokaal uitvoeren-Azure Data Lake U-SQL-SDK
description: Meer informatie over het lokaal uitvoeren en testen van U-SQL-taken met behulp van de opdracht regel en de programmeer interfaces op uw lokale werk station.
ms.service: data-lake-analytics
author: yanacai
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 03/01/2017
ms.openlocfilehash: 8fb60e62a63bfc4562f19d483dc84c99c37676b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92215532"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>U-SQL uitvoeren en testen met Azure Data Lake U-SQL SDK

Bij het ontwikkelen van U-SQL-script is het gebruikelijk om U-SQL-script lokaal uit te voeren en te testen voordat u het naar de Cloud verzendt. Azure Data Lake biedt een Nuget-pakket met de naam Azure Data Lake U-SQL-SDK voor dit scenario, waarmee u eenvoudig kunt schalen en testen. Het is ook mogelijk deze U-SQL-test te integreren met het CI-systeem (continue Integration) om het compileren en testen te automatiseren.

Als u meer informatie wilt over het hand matig uitvoeren en opsporen van fouten met U-SQL-script met het GUI-hulp programma, kunt u hiervoor Azure Data Lake-Hulpprogram Ma's voor Visual Studio gebruiken. Meer informatie vindt u [hier](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Azure Data Lake U-SQL-SDK installeren

U kunt de Azure Data Lake U-SQL-SDK [hier](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) ophalen op Nuget.org. En voordat u het gebruikt, moet u ervoor zorgen dat u als volgt afhankelijkheden hebt.

### <a name="dependencies"></a>Afhankelijkheden

De Data Lake U-SQL SDK vereist de volgende afhankelijkheden:

- [Microsoft .NET Framework 4,6 of nieuwer](https://www.microsoft.com/download/details.aspx?id=17851).
- Micro soft Visual C++ 14 en Windows SDK 10.0.10240.0 of nieuwer (dit wordt CppSDK genoemd in dit artikel). Er zijn twee manieren om CppSDK op te halen:

  - Installeer de [Visual Studio Community-editie](https://developer.microsoft.com/downloads/vs-thankyou). U hebt een map \Windows Kits\10 in de map Program Files, bijvoorbeeld: C:\Program Files (x86) \Windows Kits\10\. U vindt ook de Windows 10 SDK-versie onder \Windows Kits\10\Lib. Als u deze mappen niet ziet, installeert u Visual Studio opnieuw en moet u de Windows 10 SDK selecteren tijdens de installatie. Als u dit hebt geïnstalleerd met Visual Studio, wordt dit door de lokale U-SQL-compiler automatisch gedetecteerd.

    ![Windows 10 SDK voor Data Lake-Hulpprogram Ma's voor Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Installeer [Data Lake-Hulpprogram ma's voor Visual Studio](https://aka.ms/adltoolsvs). U kunt de voorverpakte Visual C++-en Windows SDK-bestanden vinden op `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK.`

    In dit geval kan de lokale U-SQL-compiler de afhankelijkheden niet automatisch vinden. U moet het pad naar de CppSDK opgeven. U kunt de bestanden kopiëren naar een andere locatie of gebruiken als.

## <a name="understand-basic-concepts"></a>Basis concepten begrijpen

### <a name="data-root"></a>Data root

De hoofdmap van het gegevens archief is een lokale Store voor het lokale reken account. Het is gelijk aan het Azure Data Lake Store account van een Data Lake Analytics-account. Overschakelen naar een andere gegevens-hoofdmap is net als het overschakelen naar een ander opslag account. Als u toegang wilt krijgen tot veelgebruikte gegevens met verschillende gegevens-root-mappen, moet u absolute paden in uw scripts gebruiken. U kunt ook symbolische koppelingen voor bestands systemen maken (bijvoorbeeld **mklink** op NTFS) onder de gegevens-root-map om naar de gedeelde gegevens te verwijzen.

De hoofdmap van het gegevens gebruik wordt gebruikt voor het volgende:

- Lokale meta gegevens opslaan, zoals data bases, tabellen, functies voor tabel waarden (TVFs) en assembly's.
- Zoek naar de invoer-en uitvoer paden die als relatieve paden in U-SQL zijn gedefinieerd. Het gebruik van relatieve paden maakt het gemakkelijker om uw U-SQL-projecten te implementeren in Azure.

### <a name="file-path-in-u-sql"></a>Bestandspad in U-SQL

U kunt zowel een relatief pad als een lokaal absoluut pad gebruiken in U-SQL-scripts. Het relatieve pad is relatief ten opzichte van het opgegeven pad naar de hoofdmap van de gegevens. U wordt aangeraden "/" als padscheidingsteken te gebruiken om uw scripts compatibel te maken met de server zijde. Hier volgen enkele voor beelden van relatieve paden en hun equivalente absolute paden. In deze voor beelden is C:\LocalRunDataRoot de hoofdmap van de gegevens.

|Relatief pad|Absoluut pad|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Werkmap

Bij het lokaal uitvoeren van het U-SQL-script wordt een werkmap gemaakt tijdens de compilatie onder de huidige actieve directory. Naast de compilatie uitvoer worden de benodigde runtime bestanden voor lokale uitvoeringen schaduw kopie gekopieerd naar deze werkmap. De hoofdmap van de werkmap heet ' ScopeWorkDir ' en de bestanden in de werkmap zijn als volgt:

|Map/bestand|Map/bestand|Map/bestand|Definitie|Beschrijving|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Hash-teken reeks van runtime versie|Schaduw kopie van runtime-bestanden die nodig zijn voor lokale uitvoering|
| |Script_66AE4909AA0ED06C| |Script naam + hash-teken reeks van scriptpad|Compilatie-uitvoer en logboek registratie van uitvoerings stappen|
| | |\_script \_ . ABR|Uitvoer van compiler|Algebra-bestand|
| | |\_ScopeCodeGen \_ . *|Uitvoer van compiler|Gegenereerde beheerde code|
| | |\_ScopeCodeGenEngine \_ . *|Uitvoer van compiler|Gegenereerde systeem eigen code|
| | |assembly's waarnaar wordt verwezen|Assembly-verwijzing|Assembly bestanden waarnaar wordt verwezen|
| | |deployed_resources|Resource-implementatie|Resource-implementatie bestanden|
| | |xxxxxxxx. xxx [1.. n] \_ \* . *|Uitvoerings logboek|Logboek van uitvoerings stappen|

## <a name="use-the-sdk-from-the-command-line"></a>De SDK vanaf de opdracht regel gebruiken

### <a name="command-line-interface-of-the-helper-application"></a>Opdracht regel interface van de Help-toepassing

Onder SDK directory\build\runtime is LocalRunHelper.exe de opdracht regel hulpprogramma toepassing die interfaces biedt voor de meeste veelgebruikte functies voor lokaal uitvoeren. Houd er rekening mee dat zowel de opdracht als de para meters hoofdletter gevoelig zijn. Om het te activeren:

```console
LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]
```

Voer LocalRunHelper.exe uit zonder argumenten of met de schakel optie **Help** om de Help-informatie weer te geven:

```console
> LocalRunHelper.exe help
    Command 'help' :  Show usage information
    Command 'compile' :  Compile the script
    Required Arguments :
        -Script param
                Script File Path
    Optional Arguments :
        -Shallow [default value 'False']
                Shallow compile
```

In de Help-informatie:

- **Opdracht** geeft de naam van de opdracht.  
- Het **vereiste argument** bevat de argumenten die moeten worden opgegeven.  
- **Optioneel argument** bevat argumenten die optioneel zijn, met standaard waarden.  Optionele Booleaanse argumenten hebben geen para meters en hun vormgeving betekent negatief voor de standaard waarde.

### <a name="return-value-and-logging"></a>Retour waarde en logboek registratie

De helper-toepassing retourneert **0** voor geslaagd en **-1** voor een fout. De helper verzendt standaard alle berichten naar de huidige console. De meeste van de opdrachten ondersteunen echter het argument **-MessageOut path_to_log_file** optioneel, waarmee de uitvoer wordt omgeleid naar een logboek bestand.

### <a name="environment-variable-configuring"></a>Omgevings variabele configureren

Voor de lokale uitvoering van U-SQL is een opgegeven Data root vereist als lokale opslag account, evenals een opgegeven CppSDK-pad voor afhankelijkheden. U kunt het argument instellen in de opdracht regel of stel de omgevings variabele in.

- Stel de omgevings variabele **SCOPE_CPP_SDK** in.

  Als u micro soft Visual C++ en de Windows SDK krijgt door Data Lake-Hulpprogram Ma's voor Visual Studio te installeren, controleert u of u de volgende map hebt:

    `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`

  Definieer een nieuwe omgevings variabele met de naam **SCOPE_CPP_SDK** om naar deze map te verwijzen. Of kopieer de map naar de andere locatie en geef **SCOPE_CPP_SDK** op als dat.

  Naast het instellen van de omgevings variabele, kunt u het argument **-CppSDK** opgeven wanneer u de opdracht regel gebruikt. Dit argument overschrijft uw standaard omgevings variabele CppSDK.

- Stel de omgevings variabele **LOCALRUN_DATAROOT** in.

  Definieer een nieuwe omgevings variabele met de naam **LOCALRUN_DATAROOT** die verwijst naar de hoofdmap van de gegevens.

  Naast het instellen van de omgevings variabele, kunt u het argument **-Data root** opgeven met het pad naar de hoofdmap wanneer u een opdracht regel gebruikt. Met dit argument wordt de standaard omgevings variabele van de gegevens basis overschreven. U moet dit argument toevoegen aan elke opdracht regel die u uitvoert, zodat u de standaard omgevings variabele van de gegevens basis voor alle bewerkingen kunt overschrijven.

### <a name="sdk-command-line-usage-samples"></a>Voor beelden van SDK-opdracht regel gebruik

#### <a name="compile-and-run"></a>Compileren en uitvoeren

De opdracht **uitvoeren** wordt gebruikt om het script te compileren en vervolgens gecompileerde resultaten uit te voeren. De opdracht regel argumenten zijn een combi natie van deze van **compileren** en **uitvoeren**.

```console
LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]
```

Hier volgen enkele optionele argumenten voor **uitvoeren**:

|Argument|Standaardwaarde|Beschrijving|
|--------|-------------|-----------|
|-CodeBehind|Niet waar|Het script heeft. cs-code achter|
|-CppSDK| |CppSDK Directory|
|-Data root| Omgevings variabele data root|Data root voor lokale uitvoering, standaard ingesteld op de omgevings variabele LOCALRUN_DATAROOT|
|-MessageOut| |Berichten op de console naar een bestand dumpen|
|-Parallel|1|Het plan uitvoeren met de opgegeven parallellisme|
|-Verwijzingen| |Lijst met paden naar extra referentie-assembly's of gegevens bestanden van code achter, gescheiden door '; '|
|-UdoRedirect|Niet waar|Udo assembly-omleidings configuratie genereren|
|-UseDatabase|model|Te gebruiken Data Base voor code achter de tijdelijke assembly registratie|
|-Verbose|Niet waar|Gedetailleerde uitvoer van runtime weer geven|
|-WorkDir|Huidige map|Map voor het gebruik en uitvoer van het Compileer programma|
|-RunScopeCEP|0|Te gebruiken ScopeCEP-modus|
|-ScopeCEPTempPath|temp|Tijdelijk pad dat moet worden gebruikt voor het streamen van gegevens|
|-OptFlags| |Door komma's gescheiden lijst met optimalisatie vlaggen|

Hier volgt een voorbeeld:

`LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose`

Naast het combi neren van **compileren** en **uitvoeren**, kunt u de gecompileerde uitvoer bare bestanden afzonderlijk compileren en uitvoeren.

#### <a name="compile-a-u-sql-script"></a>Een U-SQL-script compileren

De **compiler** opdracht wordt gebruikt voor het compileren van een U-SQL-script naar uitvoer bare bestanden.

```console
LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]
```

Hier volgen enkele optionele argumenten voor **compileren**:

|Argument|Description|
|--------|-----------|
| -CodeBehind [standaard waarde ' false ']|Het script heeft. cs-code achter|
| -CppSDK [standaard waarde]|CppSDK Directory|
| -Data root [standaard waarde ' data root-omgevings variabele ']|Data root voor lokale uitvoering, standaard ingesteld op de omgevings variabele LOCALRUN_DATAROOT|
| -MessageOut [standaard waarde]|Berichten op de console naar een bestand dumpen|
| -Verwijzingen [standaard waarde "']|Lijst met paden naar extra referentie-assembly's of gegevens bestanden van code achter, gescheiden door '; '|
| -Recente [standaard waarde ' false ']|Recente compilatie|
| -UdoRedirect [standaard waarde ' false ']|Udo assembly-omleidings configuratie genereren|
| -UseDatabase [standaard waarde ' Master ']|Te gebruiken Data Base voor code achter de tijdelijke assembly registratie|
| -WorkDir [standaard waarde ' huidige map ']|Map voor het gebruik en uitvoer van het Compileer programma|
| -RunScopeCEP [standaard waarde ' 0 ']|Te gebruiken ScopeCEP-modus|
| -ScopeCEPTempPath [standaard waarde]|Tijdelijk pad dat moet worden gebruikt voor het streamen van gegevens|
| -OptFlags [standaard waarde]|Door komma's gescheiden lijst met optimalisatie vlaggen|

Hier volgen enkele gebruiks voorbeelden.

Een U-SQL-script compileren:

```console
LocalRunHelper compile -Script d:\test\test1.usql
```

Compileer een U-SQL-script en stel de map Data-root in. Houd er rekening mee dat hiermee de omgevings variabele set wordt overschreven.

```console
LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot
```

Een U-SQL-script compileren en een werkmap, referentie-assembly en data base instellen:

```console
LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB
```

#### <a name="execute-compiled-results"></a>Gecompileerde resultaten uitvoeren

De opdracht **uitvoeren** wordt gebruikt om gecompileerde resultaten uit te voeren.

```console
LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]
```

Hier volgen enkele optionele argumenten voor **uitvoeren**:

|Argument|Standaardwaarde|Beschrijving|
|--------|-------------|-----------|
|-Data root | '' |Data root voor het uitvoeren van meta gegevens. De standaard instelling is de omgevings variabele **LOCALRUN_DATAROOT** .|
|-MessageOut | '' |Dump berichten op de console naar een bestand.|
|-Parallel | i |Indicator voor het uitvoeren van de gegenereerde stappen voor lokaal uitvoeren met het opgegeven parallellisme-niveau.|
|-Verbose | Terecht |Indicator om gedetailleerde uitvoer van runtime weer te geven.|

Hier volgt een voor beeld van het gebruik:

```console
LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5
```

## <a name="use-the-sdk-with-programming-interfaces"></a>De SDK met programmeer interfaces gebruiken

De programmeer interfaces bevinden zich allemaal in de LocalRunHelper.exe. U kunt ze gebruiken om de functionaliteit van de U-SQL-SDK en het C#-test raamwerk te integreren om uw U-SQL-script lokale test te schalen. In dit artikel gebruikt u het standaard test project van C#-eenheid om te laten zien hoe u deze interfaces kunt gebruiken om uw U-SQL-script te testen.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Stap 1: test project en configuratie van C#-eenheid maken

- Maak een test project van een C#-eenheid via file > New > project > Visual C# > test project > unit test.
- Voeg LocalRunHelper.exe toe als referentie voor het project. Het LocalRunHelper.exe bevindt zich op \build\runtime\LocalRunHelper.exe in het Nuget-pakket.

   ![Naslag informatie voor Azure Data Lake U-SQL SDK toevoegen](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- U-SQL SDK ondersteunt **alleen** x64-omgeving. Stel platform doel bouwen in op x64. U kunt instellen dat via Project eigenschap > bouwen > platform doel.

   ![X64-project Azure Data Lake U-SQL-SDK configureren](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Zorg ervoor dat u uw test omgeving als x64 hebt ingesteld. In Visual Studio kunt u deze instellen met behulp van test-> test instellingen > architectuur van de standaard processor > x64.

   ![X64-test omgeving Azure Data Lake U-SQL SDK configureren](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Zorg ervoor dat u alle afhankelijkheids bestanden onder NugetPackage\build\runtime\ naar een werk directory van project kopieert die meestal onder ProjectFolder\bin\x64\Debug.

### <a name="step-2-create-u-sql-script-test-case"></a>Stap 2: een test case maken voor U-SQL-script

Hieronder ziet U de voorbeeld code voor de U-SQL-script test. Voor het testen moet u scripts, invoer bestanden en verwachte uitvoer bestanden voorbereiden.

```usql
using System;
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.IO;
using System.Text;
using System.Security.Cryptography;
using Microsoft.Analytics.LocalRun;
namespace UnitTestProject1
{
    [TestClass]
    public class USQLUnitTest
    {
        [TestMethod]
        public void TestUSQLScript()
        {
            //Specify the local run message output path
            StreamWriter MessageOutput = new StreamWriter("../../../log.txt");
            LocalRunHelper localrun = new LocalRunHelper(MessageOutput);
            //Configure the DateRoot path, Script Path and CPPSDK path
            localrun.DataRoot = "../../../";
            localrun.ScriptPath = "../../../Script/Script.usql";
            localrun.CppSdkDir = "../../../CppSDK";
            //Run U-SQL script
            localrun.DoRun();
            //Script output
            string Result = Path.Combine(localrun.DataRoot, "Output/result.csv");
            //Expected script output
            string ExpectedResult = "../../../ExpectedOutput/result.csv";
            Test.Helpers.FileAssert.AreEqual(Result, ExpectedResult);
            //Don't forget to close MessageOutput to get logs into file
            MessageOutput.Close();
        }
    }
}
namespace Test.Helpers
{
    public static class FileAssert
    {
        static string GetFileHash(string filename)
        {
            Assert.IsTrue(File.Exists(filename));
            using (var hash = new SHA1Managed())
            {
                var clearBytes = File.ReadAllBytes(filename);
                var hashedBytes = hash.ComputeHash(clearBytes);
                return ConvertBytesToHex(hashedBytes);
            }
        }
        static string ConvertBytesToHex(byte[] bytes)
        {
            var sb = new StringBuilder();
            for (var i = 0; i < bytes.Length; i++)
            {
                sb.Append(bytes[i].ToString("x"));
            }
            return sb.ToString();
        }
        public static void AreEqual(string filename1, string filename2)
        {
            string hash1 = GetFileHash(filename1);
            string hash2 = GetFileHash(filename2);
            Assert.AreEqual(hash1, hash2);
        }
    }
}
```

### <a name="programming-interfaces-in-localrunhelperexe"></a>Programmeer interfaces in LocalRunHelper.exe

LocalRunHelper.exe biedt de programmeer interfaces voor lokale U-SQL-compilatie,-uitvoering, enzovoort. De interfaces worden als volgt weer gegeven.

### <a name="constructor"></a>Constructor

open bare LocalRunHelper ([System. IO. TextWriter messageOutput = NULL])

|Parameter|Type|Description|
|---------|----|-----------|
|messageOutput|System. IO. TextWriter|Stel voor uitvoer berichten in op NULL om console te gebruiken|

### <a name="properties"></a>Eigenschappen

|Eigenschap|Type|Description|
|--------|----|-----------|
|AlgebraPath|tekenreeks|Het pad naar het algebra-bestand (algebra-bestand is een van de compilatie resultaten)|
|CodeBehindReferences|tekenreeks|Als het script extra code achter verwijzingen bevat, geeft u de paden op, gescheiden door|
|CppSdkDir|tekenreeks|CppSDK Directory|
|CurrentDir|tekenreeks|Huidige map|
|Data root|tekenreeks|Pad naar Data root|
|DebuggerMailPath|tekenreeks|Het pad naar de mail slot voor fout opsporing|
|GenerateUdoRedirect|booleaans|Als we de omleidings configuratie voor het laden van assembly willen genereren, overschrijven|
|HasCodeBehind|booleaans|Als het script onderliggende code bevat|
|InputDir|tekenreeks|Map voor invoer gegevens|
|MessagePath|tekenreeks|Pad naar bericht dump bestand|
|OutputDir|tekenreeks|Map voor uitvoer gegevens|
|Parallelle uitvoering|int|Parallelle uitvoering voor het uitvoeren van de algebra|
|ParentPid|int|PID van het bovenliggende item waarop de service wordt gecontroleerd, ingesteld op 0 of negatief om te negeren|
|ResultPath|tekenreeks|Pad naar resultaat dump bestand|
|RuntimeDir|tekenreeks|Runtime Directory|
|ScriptPath|tekenreeks|Waar u het script kunt vinden?|
|Diep|booleaans|Een recente compilatie of niet|
|TempDir|tekenreeks|Tijdelijke map|
|UseDataBase|tekenreeks|Geef de Data Base op die moet worden gebruikt voor code achter tijdelijke assembly registratie, standaard model|
|WorkDir|tekenreeks|Voorkeurs werkmap|

### <a name="method"></a>Methode

|Methode|Beschrijving|Opvragen|Parameter|
|------|-----------|------|---------|
|open bare BOOL DoCompile ()|Het U-SQL-script compileren|Waar op geslaagd| |
|open bare BOOL DoExec ()|Het gecompileerde resultaat uitvoeren|Waar op geslaagd| |
|open bare BOOL DoRun ()|Het U-SQL-script uitvoeren (compileren en uitvoeren)|Waar op geslaagd| |
|open bare BOOL IsValidRuntimeDir (pad naar teken reeks)|Controleren of het opgegeven pad een geldig runtime-pad is|Waar geldig|Het pad van de runtime Directory|

## <a name="faq-about-common-issue"></a>Veelgestelde vragen over het algemene probleem

### <a name="error-1"></a>Fout 1

E_CSC_SYSTEM_INTERNAL: interne fout. Kan bestand of Assembly ' ScopeEngineManaged.dll ' of een van de bijbehorende afhankelijkheden niet laden. De opgegeven module is niet gevonden.

Controleer het volgende:

- Zorg ervoor dat u beschikt over een x64-omgeving. Het doel platform van de build en de test omgeving moeten x64 zijn, Zie **stap 1: C#-eenheids test project en configuratie hierboven maken** .
- Zorg ervoor dat u alle afhankelijkheids bestanden onder NugetPackage\build\runtime\ naar werk directory van project hebt gekopieerd.

## <a name="next-steps"></a>Volgende stappen

- Zie [Aan de slag met de Azure Data Lake Analytics U-SQL-taal](data-lake-analytics-u-sql-get-started.md) om U-SQL te leren.
- Zie [toegang tot Diagnostische logboeken voor Azure data Lake Analytics voor](data-lake-analytics-diagnostic-logs.md)informatie over het vastleggen van diagnostische gegevens.
- Zie [website logboeken analyseren met Azure data Lake Analytics](data-lake-analytics-analyze-weblogs.md)voor een complexere query.
- Zie [taak browser en taak weergave gebruiken voor Azure data Lake Analytics taken voor](data-lake-analytics-data-lake-tools-view-jobs.md)het weer geven van taak Details.
- Zie [de weer gave vertex Execution gebruiken in data Lake-Hulpprogram ma's voor Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)voor meer informatie over het gebruik van de vertex Execution-weer gave.
