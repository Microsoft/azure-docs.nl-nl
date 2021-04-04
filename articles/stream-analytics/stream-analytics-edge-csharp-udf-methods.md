---
title: .NET-standaard functies ontwikkelen voor Azure Stream Analytics-taken (preview-versie)
description: Meer informatie over het schrijven van door de gebruiker gedefinieerde C#-functies voor Stream Analytics taken.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/10/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 52fa6f05db5452a2e7b8ec4f93d65525873c8c7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020567"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Door de gebruiker gedefinieerde .NET Standard-functies ontwikkelen voor Azure Stream Analytics-taken (preview-versie)

Azure Stream Analytics biedt een SQL-achtige query taal voor het uitvoeren van trans formaties en berekeningen op basis van gegevens stromen van gebeurtenissen. Er zijn veel ingebouwde functies, maar voor sommige complexe scenario's is extra flexibiliteit vereist. Met .NET Standard door de gebruiker gedefinieerde functies (UDF) kunt u uw eigen functies aanroepen die zijn geschreven in een .NET Standard-taal (C#, F #, etc.) om de Stream Analytics query taal uit te breiden. Met UDFs kunt u complexe wiskundige berekeningen uitvoeren, aangepaste ML-modellen importeren met behulp van ML.NET en aangepaste toerekenings logica voor ontbrekende gegevens gebruiken. De UDF-functie voor Stream Analytics-taken is momenteel in de previewfase en mag niet worden gebruikt voor productieworkloads.

Door de gebruiker gedefinieerde .NET-functie voor Cloud taken is beschikbaar in:
* VS - west-centraal
* Europa - noord
* VS - oost
* VS - west
* VS - oost 2
* Europa -west

Als u geïnteresseerd bent in het gebruik van deze functie in een andere regio, kunt u [toegang aanvragen](https://aka.ms/ccodereqregion).

## <a name="package-path"></a>Pad naar pakket

De indeling van een UDF-pakket heeft het pad `/UserCustomCode/CLR/*` . Dynamic Link Libraries (Dll's) en bronnen worden gekopieerd in de `/UserCustomCode/CLR/*` map, waarmee gebruikers-dll's van systeem-en Azure stream Analytics-dll's kunnen worden geïsoleerd. Het pad naar het pakket wordt gebruikt voor alle functies, ongeacht de methode die wordt gebruikt om deze te gebruiken.

## <a name="supported-types-and-mapping"></a>Ondersteunde typen en toewijzing

Voor Azure Stream Analytics waarden die in C# moeten worden gebruikt, moeten ze worden gemarshald van de ene omgeving naar de andere. Marshaling gebeurt voor alle invoer parameters van een UDF. Elk type Azure Stream Analytics heeft een overeenkomend type in C# dat wordt weer gegeven in de onderstaande tabel:

|**Azure Stream Analytics type** |**C#-type** |
|---------|---------|
|bigint | long |
|float | double |
|nvarchar (max) | tekenreeks |
|datum/tijd | DateTime |
|Record | Woordenlijst\<string, object> |
|Matrix | Object [] |

Hetzelfde geldt wanneer gegevens moeten worden gemarshald van C# naar Azure Stream Analytics, wat gebeurt bij de uitvoer waarde van een UDF. In de volgende tabel ziet u welke typen worden ondersteund:

|**C#-type**  |**Azure Stream Analytics type**  |
|---------|---------|
|long  |  bigint   |
|double  |  float   |
|tekenreeks  |  nvarchar (max)   |
|DateTime  |  dateTime   |
|bouw  |  Record   |
|object  |  Record   |
|Object []  |  Matrix   |
|Woordenlijst\<string, object>  |  Record   |

## <a name="develop-a-udf-in-visual-studio-code"></a>Een UDF ontwikkelen in Visual Studio code

Met [Visual Studio code-hulpprogram ma's voor Azure stream Analytics](quick-create-visual-studio-code.md) kunt u gemakkelijker udf's schrijven, uw taken lokaal testen (zelfs offline) en uw stream Analytics-taak publiceren naar Azure.

Er zijn twee manieren om .NET-standaard Udf's te implementeren in Visual Studio code-hulpprogram ma's.

* UDF van lokale Dll's
* UDF van een lokaal project

### <a name="local-project"></a>Lokaal project

Door de gebruiker gedefinieerde functies kunnen worden geschreven in een assembly die later wordt verwezen in een Azure Stream Analytics-query. Dit is de aanbevolen optie voor complexe functies waarvoor de volledige kracht van een .NET Standard-taal is vereist dan de expressie taal, zoals procedurele logica of recursie. Udf's van een lokaal project kan ook worden gebruikt wanneer u de functie logica tussen verschillende Azure Stream Analytics query's moet delen. Door Udf's toe te voegen aan uw lokale project krijgt u de mogelijkheid om uw functies lokaal op te sporen en te testen.

Verwijzen naar een lokaal project:

1. Maak een nieuwe .NET Standard-klassebibliotheek op uw lokale machine.
2. Schrijf de code in uw klasse. Houd er rekening mee dat de klassen als *openbaar* moeten worden gedefinieerd en dat objecten als *statisch openbaar* moeten worden gedefinieerd.
3. Voeg een nieuw configuratie bestand voor de CSharp-functie toe aan uw Azure Stream Analytics-project en verwijs naar het CSharp-klassen bibliotheek project.
4. Configureer het pad naar de assembly in het configuratie bestand van de taak, `JobConfig.json` **CustomCodeStorage** sectie. Deze stap is niet nodig voor lokale tests.

### <a name="local-dlls"></a>Lokale Dll's

U kunt ook verwijzen naar lokale Dll's die de door de gebruiker gedefinieerde functies bevatten.

### <a name="example"></a>Voorbeeld

In dit voor beeld is **CSharpUDFProject** een C#-klassen bibliotheek project en **ASAUDFDemo** het Azure stream Analytics project, dat verwijst naar **CSharpUDFProject**.

:::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-demo.png" alt-text="Azure Stream Analytics project in Visual Studio code":::

De volgende UDF heeft een functie waarmee een geheel getal zelf wordt vermenigvuldigd om het kwadraat van het gehele getal te produceren. De klassen moeten als *openbaar* worden gedefinieerd en objecten moeten als *statisch openbaar* worden gedefinieerd.

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

In de volgende stappen ziet u hoe u C# UDF-functie toevoegt aan uw Stream Analytics-project.

1. Klik met de rechter muisknop op de map **functions** en kies **item toevoegen**.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function.png" alt-text="Nieuwe functie toevoegen in Azure Stream Analytics project":::

2. Voeg een C#-functie **SquareFunction** toe aan uw Azure stream Analytics-project.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function-2.png" alt-text="Selecteer de functie CSharp van Stream Analytics project in VS code":::

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function-name.png" alt-text="Voer de naam van de CSharp-functie in VS code in":::

3. Selecteer in de C#-functie configuratie de optie **bibliotheek project pad kiezen** om uw C#-project te kiezen in de vervolg keuzelijst en selecteer **project samen stellen** om uw project te bouwen. Kies vervolgens **klasse selecteren** en **methode selecteren** om de bijbehorende klasse en methode naam te selecteren in de vervolg keuzelijst. Om te verwijzen naar de methoden, typen en functies in de Stream Analytics query, moeten de klassen als *openbaar* worden gedefinieerd en moeten de objecten als *statisch openbaar* worden gedefinieerd.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-choose-project.png" alt-text="Stream Analytics C sharp-functie configuratie VS code":::

    Als u de C# UDF van een DLL-bestand wilt gebruiken, selecteert u **bibliotheek dll-pad kiezen** om het dll-bestand te kiezen. Kies vervolgens **klasse selecteren** en **methode selecteren** om de bijbehorende klasse en methode naam te selecteren in de vervolg keuzelijst.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-choose-dll.png" alt-text="Stream Analytics C sharp-functie configuratie":::

4. Roep de UDF aan in uw Azure Stream Analytics-query.

   ```sql
    SELECT price, udf.SquareFunction(price)
    INTO Output
    FROM Input 
   ```

5. Voordat u de taak naar Azure verzendt, moet u het pad naar het pakket configureren in het `JobConfig.json` gedeelte **CustomCodeStorage** van het taak configuratie bestand. Gebruik **selecteren in uw abonnement** in code lens om uw abonnement te kiezen en kies het opslag account en de container naam in de vervolg keuzelijst. **Pad** laten staan als standaard. Deze stap is niet nodig voor lokale tests.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-configure-storage-account.png" alt-text="Bibliotheekpad kiezen":::

## <a name="develop-a-udf-in-visual-studio"></a>Een UDF ontwikkelen in Visual Studio

Er zijn drie manieren om Udf's te implementeren in Visual Studio-hulpprogram ma's.

* CodeBehind-bestanden in een ASA-project
* UDF van een lokaal project
* Een bestaand pakket van een Azure Storage-account

### <a name="codebehind"></a>CodeBehind

U kunt door de gebruiker gedefinieerde functies schrijven in het **script. asql** CodeBehind. Visual Studio Tools compileert het CodeBehind-bestand automatisch in een assembly-bestand. De assembly's worden verpakt als een zip-bestand en geüpload naar uw opslag account wanneer u uw taak naar Azure verzendt. U kunt meer informatie over het schrijven van een C# UDF met behulp van CodeBehind door de zelf studie [C# UDF voor stream Analytics Edge-taken](stream-analytics-edge-csharp-udf.md) te volgen. 

### <a name="local-project"></a>Lokaal project

Verwijzen naar een lokaal project in Visual Studio:

1. Maak een nieuwe .NET Standard-klassebibliotheek in uw oplossing
2. Schrijf de code in uw klasse. Houd er rekening mee dat de klassen als *openbaar* moeten worden gedefinieerd en dat objecten als *statisch openbaar* moeten worden gedefinieerd. 
3. Compileer het nieuwe project. Met de hulpprogram ma's worden alle artefacten in de map bin verpakt naar een zip-bestand en wordt het zip-bestand geüpload naar het opslag account. Voor externe verwijzingen gebruikt u assembly-verwijzing in plaats van het NuGet-pakket.
4. Verwijs naar de nieuwe klasse in uw Azure Stream Analytics-project.
5. Voeg een nieuwe functie toe aan uw Azure Stream Analytics-project.
6. Configureer het pad van de assembly in het taak configuratie bestand, `JobConfig.json` . Stel het pad van de assembly in op **lokale project verwijzing of CodeBehind**.
7. Bouw zowel het functie project als het Azure Stream Analytics project opnieuw op.  

### <a name="example"></a>Voorbeeld

In dit voor beeld is **UDFTest** een C#-klassen bibliotheek project en **ASAUDFDemo** het Azure stream Analytics project, dat verwijst naar **UDFTest**.

:::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png" alt-text="Azure Stream Analytics IoT Edge-project in Visual Studio":::

1. Bouw uw C#-project, waarmee u een verwijzing naar uw C# UDF kunt toevoegen vanuit de Azure Stream Analytics-query.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png" alt-text="Een Azure Stream Analytics IoT Edge-project maken in Visual Studio":::

2. Voeg de verwijzing naar het C#-project in het ASA-project toe. Klik met de rechter muisknop op het knoop punt verwijzingen en kies verwijzing toevoegen.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png" alt-text="Een verwijzing naar een C#-project in Visual Studio toevoegen":::

3. Kies de naam van het C#-project in de lijst.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png" alt-text="Kies de naam van uw C#-project in de lijst met verwijzingen":::

4. U ziet de **UDFTest** vermeld onder **verwijzingen** in **Solution Explorer**.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png" alt-text="De verwijzing naar de door de gebruiker gedefinieerde functie in Solution Explorer weer geven":::

5. Klik met de rechter muisknop op de map **functions** en kies **Nieuw item**.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png" alt-text="Nieuw item toevoegen aan functies in Azure Stream Analytics EDGE-oplossing":::

6. Voeg een C#-functie toe **SquareFunction.js** aan uw Azure stream Analytics-project.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png" alt-text="Selecteer de functie CSharp van Stream Analytics Edge-items in Visual Studio":::

7. Dubbel klik op de functie in **Solution Explorer** om het dialoog venster configuratie te openen.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png" alt-text="C-scherpe functie configuratie in Visual Studio":::

8. In de C#-functie configuratie kiest u **laden van ASA-project verwijzing** en de bijbehorende assembly-, klasse-en methode namen van de vervolg keuzelijst. Om te verwijzen naar de methoden, typen en functies in de Stream Analytics query, moeten de klassen als *openbaar* worden gedefinieerd en moeten de objecten als *statisch openbaar* worden gedefinieerd.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png" alt-text="Stream Analytics C sharp-functie configuratie Visual Studio":::

## <a name="existing-packages"></a>Bestaande pakketten

U kunt in elke wille keurige IDE uw keuze maken over .NET-standaard-UDFs en deze oproepen vanuit uw Azure Stream Analytics-query. Compileer eerst uw code en verpak alle Dll's. De indeling van het pakket heeft het pad `/UserCustomCode/CLR/*` . Upload vervolgens `UserCustomCode.zip` naar de hoofdmap van de container in uw Azure Storage-account.

Zodra de ZIP-pakketten van de assembly naar uw Azure Storage-account zijn geüpload, kunt u de functies in Azure Stream Analytics query's gebruiken. U hoeft alleen de opslag gegevens in de configuratie van de Stream Analytics-taak op te slaan. U kunt de functie niet lokaal met deze optie testen omdat Visual Studio Tools uw pakket niet kan downloaden. Het pad naar het pakket wordt rechtstreeks naar de service geparseerd. 

Het pad van de assembly configureren in het taak configuratie bestand `JobConfig.json` :

Vouw de sectie **Configuratie van de door de gebruiker gedefinieerde code** uit en vul de configuratie in met de volgende voorgestelde waarden:

   |**Instelling**|**Voorgestelde waarde**|
   |-------|---------------|
   |Resource globale opslaginstellingen|Kies gegevensbron van het huidige account|
   |Abonnement voor globale opslaginstellingen| < uw abonnement >|
   |Globale opslaginstellingen opslagaccount| < uw opslagaccount >|
   |Resource aangepaste code opslaginstellingen|Kies gegevensbron van het huidige account|
   |Aangepaste code opslaginstellingen opslagaccount|< uw opslagaccount >|
   |Container aangepaste code opslaginstellingen|< uw opslagcontainer >|
   |Assembly bron aangepaste code|Bestaande assembly pakketten uit de Cloud|
   |Assembly bron aangepaste code|UserCustomCode.zip|

## <a name="user-logging"></a>Gebruikers logboek registratie

Met het mechanisme voor logboekregistratie kunt u aangepaste gegevens vastleggen terwijl een taak wordt uitgevoerd. U kunt logboekgegevens gebruiken om fouten op te sporen of om de juistheid van de aangepaste code in realtime te evalueren.

Met de- `StreamingContext` klasse kunt u Diagnostische gegevens publiceren met behulp van de `StreamingDiagnostics.WriteError` functie. De volgende code toont de interface die wordt weer gegeven door Azure Stream Analytics.

```csharp
public abstract class StreamingContext
{
    public abstract StreamingDiagnostics Diagnostics { get; }
}

public abstract class StreamingDiagnostics
{
    public abstract void WriteError(string briefMessage, string detailedMessage);
}
```

`StreamingContext` wordt door gegeven als invoer parameter voor de UDF-methode en kan worden gebruikt in de UDF voor het publiceren van aangepaste logboek gegevens. In het onderstaande voor beeld `MyUdfMethod` definieert u een **gegevens** invoer, die wordt opgegeven door de query en een **context** invoer als de `StreamingContext` , die wordt opgegeven door de runtime-engine. 

```csharp
public static long MyUdfMethod(long data, StreamingContext context)
{
    // write log
    context.Diagnostics.WriteError("User Log", "This is a log message");
    
    return data;
}
```

De `StreamingContext` waarde hoeft niet door de SQL-query worden door gegeven. Azure Stream Analytics biedt automatisch een context object als er een invoer parameter aanwezig is. Het gebruik van de `MyUdfMethod` wordt niet gewijzigd, zoals wordt weer gegeven in de volgende query:

```sql
SELECT udf.MyUdfMethod(input.value) as udfValue FROM input
```

U kunt logboekberichten openen via de [Diagnostische logboeken](data-errors.md).

## <a name="limitations"></a>Beperkingen

De UDF-preview heeft momenteel de volgende beperkingen:

* .NET Standard Udf's kan alleen worden gemaakt in Visual Studio code of Visual Studio en naar Azure worden gepubliceerd. Alleen-lezen versies van .NET Standard Udf's kunnen worden weer gegeven onder **functies** in de Azure Portal. Het ontwerpen van .NET-standaard functies wordt niet ondersteund in de Azure Portal.

* De Azure Portal query-editor toont een fout bij het gebruik van .NET Standard UDF in de portal. 

* Omdat de aangepaste code aandelen context met Azure Stream Analytics-engine, kan aangepaste code niet verwijzen naar iets met een conflicterende naam ruimte/dll_name met Azure Stream Analytics code. U kunt bijvoorbeeld niet verwijzen naar *Newton Soft JSON*.

* Ondersteunende bestanden die in het project zijn opgenomen, worden gekopieerd naar het door de gebruiker aangepaste code zip-bestand dat wordt gebruikt wanneer u de taak naar de Cloud publiceert. Alle bestanden in submappen worden rechtstreeks gekopieerd naar de hoofdmap van de map met aangepaste gebruikers code in de Cloud wanneer deze is uitgepakt. De zip wordt ' afgevlakt ' bij het decomprimeren.

* Aangepaste code van gebruiker biedt geen ondersteuning voor lege mappen. Voeg geen lege mappen toe aan de ondersteunende bestanden in het project.

## <a name="next-steps"></a>Volgende stappen

* [Zelf studie: een door de gebruiker gedefinieerde C#-functie voor een Azure Stream Analytics-taak schrijven (preview)](stream-analytics-edge-csharp-udf.md)
* [Zelfstudie: Door gebruiker gedefinieerde JavaScript-functies in Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Een Azure Stream Analytics-taak maken in Visual Studio code](quick-create-visual-studio-code.md)