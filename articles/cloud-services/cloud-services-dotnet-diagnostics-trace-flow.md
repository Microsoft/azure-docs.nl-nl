---
title: De stroom in Cloud Services (klassieke) toepassing traceren met Azure Diagnostics
description: Voeg tracerings berichten toe aan een Azure-toepassing voor het opsporen van fouten, het meten van prestaties, bewaking, verkeers analyse en meer.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: b00bb28128cfe9a2e701647ad174ea2c9dd458e4
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742122"
---
# <a name="trace-the-flow-of-a-cloud-services-classic-application-with-azure-diagnostics"></a>De stroom van een Cloud Services (klassieke) toepassing traceren met Azure Diagnostics

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Tracering is een methode waarmee u de uitvoering van uw toepassing kunt bewaken terwijl deze wordt uitgevoerd. U kunt de klassen [System. Diagnostics. trace](/dotnet/api/system.diagnostics.trace), [System. Diagnostics. debug](/dotnet/api/system.diagnostics.debug)en [System. Diagnostics. TraceSource](/dotnet/api/system.diagnostics.tracesource) gebruiken om informatie vast te leggen over fouten en de uitvoering van toepassingen in Logboeken, tekst bestanden of andere apparaten voor latere analyse. Zie [toepassingen traceren en instrumenteren](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications)voor meer informatie over tracering.

## <a name="use-trace-statements-and-trace-switches"></a>Trace-instructies en tracerings switches gebruiken
Implementeer tracering in uw Cloud Services-toepassing door de [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) toe te voegen aan de toepassings configuratie en aanroepen naar System. Diagnostics. trace of System. Diagnostics. debug te maken in de toepassings code. Gebruik het configuratie bestand *app.config* voor werk rollen en de *web.config* voor webrollen. Wanneer u een nieuwe gehoste service maakt met behulp van een Visual Studio-sjabloon, wordt Azure Diagnostics automatisch toegevoegd aan het project en wordt de DiagnosticMonitorTraceListener toegevoegd aan het juiste configuratie bestand voor de functies die u toevoegt.

Zie [How to: trace-instructies toevoegen aan toepassings code](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code)voor meer informatie over het plaatsen van trace-instructies.

Door [tracerings switches](/dotnet/framework/debug-trace-profile/trace-switches) in uw code te plaatsen, kunt u bepalen of tracering plaatsvindt en hoe groot het is. Hiermee kunt u de status van uw toepassing in een productie omgeving bewaken. Dit is vooral belang rijk in een bedrijfs toepassing die gebruikmaakt van meerdere onderdelen die op meerdere computers worden uitgevoerd. Zie [How to: Configure Trace switches](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches)(Engelstalig) voor meer informatie.

## <a name="configure-the-trace-listener-in-an-azure-application"></a>De traceer-listener configureren in een Azure-toepassing
Voor tracering, fouten opsporen en TraceSource moet u ' listeners ' instellen om de verzonden berichten te verzamelen en vast te leggen. Listeners verzamelen, opslaan en routeren van tracerings berichten. Ze sturen de tracerings uitvoer naar een geschikt doel, zoals een logboek, venster of tekst bestand. Azure Diagnostics maakt gebruik van de [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) -klasse.

Voordat u de volgende procedure voltooit, moet u de diagnostische Azure-monitor initialiseren. Zie voor het [inschakelen van diagnostische gegevens in Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Houd er rekening mee dat als u de sjablonen gebruikt die worden verschaft door Visual Studio, de configuratie van de listener automatisch voor u wordt toegevoegd.

### <a name="add-a-trace-listener"></a>Een traceer-listener toevoegen
1. Open het web.config-of app.config-bestand voor uw rol.
2. Voeg de volgende code toe aan het bestand. Wijzig het versie kenmerk om het versie nummer te gebruiken van de assembly waarnaar u verwijst. De assembly-versie hoeft niet per se te worden gewijzigd met de Azure SDK-release, tenzij er updates voor zijn.
   
    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                    <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
   > [!IMPORTANT]
   > Zorg ervoor dat u een project verwijzing hebt naar de assembly micro soft. WindowsAzure. Diagnostics. Werk het versie nummer in bovenstaand XML-bestand bij om overeen te komen met de versie van de micro soft. WindowsAzure. Diagnostics-assembly.
   > 
   > 
3. Sla het configuratiebestand op.

Zie [Trace listeners](/dotnet/framework/debug-trace-profile/trace-listeners)voor meer informatie over listeners.

Nadat u de stappen hebt voltooid om de listener toe te voegen, kunt u tracerings instructies aan uw code toevoegen.

### <a name="to-add-trace-statement-to-your-code"></a>Een tracerings instructie toevoegen aan uw code
1. Open een bron bestand voor uw toepassing. Bijvoorbeeld het \<RoleName> . CS-bestand voor de worker-rol of-webrol.
2. Voeg de volgende using-instructie toe als deze nog niet is toegevoegd:
    ```
        using System.Diagnostics;
    ```
3. Voeg tracerings instructies toe waarin u informatie wilt vastleggen over de status van uw toepassing. U kunt verschillende methoden gebruiken om de uitvoer van de instructie trace op te maken. Zie [How to: trace-instructies toevoegen aan de toepassings code](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code)voor meer informatie.
4. Sla het bron bestand op.




