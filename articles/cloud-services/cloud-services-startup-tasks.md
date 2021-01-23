---
title: Opstart taken uitvoeren in azure Cloud Services (klassiek) | Microsoft Docs
description: Met opstart taken kunt u uw Cloud service omgeving voorbereiden voor uw app. Zo leert u hoe opstart taken werken en hoe u deze kunt maken
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 25190075bdd13bd4b75dd82c97ee06ee60f4c26c
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743182"
---
# <a name="how-to-configure-and-run-startup-tasks-for-an-azure-cloud-service-classic"></a>Opstart taken voor een Azure-Cloud service configureren en uitvoeren (klassiek)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

U kunt opstart taken gebruiken om bewerkingen uit te voeren voordat een rol wordt gestart. Bewerkingen die u mogelijk wilt uitvoeren, zijn onder andere het installeren van een onderdeel, het registreren van COM-onderdelen, het instellen van register sleutels of het starten van een langlopend proces.

> [!NOTE]
> Opstart taken zijn niet van toepassing op Virtual Machines, alleen voor web-en werk rollen in de Cloud service.
> 
> 

## <a name="how-startup-tasks-work"></a>Hoe opstart taken werken
Opstart taken zijn acties die worden uitgevoerd voordat de rollen beginnen en worden gedefinieerd in het bestand [ServiceDefinition. csdef] met behulp van het element [Task] in het [opstart] element. Veelvoorkomende opstart taken zijn batch-bestanden, maar ze kunnen ook console toepassingen zijn of batch bestanden die Power shell-scripts starten.

Omgevings variabelen geven informatie door aan een opstart taak en lokale opslag kan worden gebruikt om gegevens uit een opstart taak door te geven. Een omgevings variabele kan bijvoorbeeld het pad opgeven naar een programma dat u wilt installeren, en bestanden kunnen worden geschreven naar lokale opslag die vervolgens later door uw rollen kan worden gelezen.

De opstart taak kan informatie en fouten registreren in de map die is opgegeven door de omgevings variabele **temp** . Tijdens de opstart taak wordt de omgevings variabele **temp** omgezet in de *map C: \\ resources \\ temp \\ [GUID]. [ Rolnaam] \\ RoleTemp* map wanneer deze wordt uitgevoerd in de Cloud.

Opstarttaken kunnen ook verschillende keren worden uitgevoerd tussen het opnieuw opstarten. De opstarttaak wordt bijvoorbeeld uitgevoerd telkens wanneer de rol opnieuw wordt gebruikt, en bij het opnieuw gebruiken van rollen wordt niet altijd opnieuw opgestart. Opstart taken moeten zodanig worden geschreven dat ze meerdere keren zonder problemen kunnen worden uitgevoerd.

Opstart taken moeten eindigen met een **Error level** (of afsluit code) van nul zodat het opstart proces kan worden voltooid. Als een opstart taak eindigt met een niet-nul- **Error level**, wordt de rol niet gestart.

## <a name="role-startup-order"></a>Opstart volgorde van rol
Hieronder vindt u een lijst met opstart procedures voor rollen in Azure:

1. Het exemplaar is gemarkeerd als **starten** en ontvangt geen verkeer.
2. Alle opstart taken worden uitgevoerd volgens hun **TaskType** -kenmerk.
   
   * De **eenvoudige** taken worden synchroon uitgevoerd, één per keer.
   * De **achtergrond** -en **voorgrond** taken worden asynchroon gestart, parallel aan de opstart taak.  
     
     > [!WARNING]
     > IIS is mogelijk niet volledig geconfigureerd tijdens het opstart taak stadium in het opstart proces, waardoor er mogelijk geen Role-specifieke gegevens beschikbaar zijn. Opstart taken waarvoor Role-specifieke gegevens moeten worden gebruikt, moeten [micro soft. WindowsAzure. ServiceRuntime. RoleEntryPoint. ONSTART](/previous-versions/azure/reference/ee772851(v=azure.100)).
     > 
     > 
3. Het hostproces van de rol wordt gestart en de site wordt gemaakt in IIS.
4. De methode [micro soft. WindowsAzure. ServiceRuntime. RoleEntryPoint. ONSTART](/previous-versions/azure/reference/ee772851(v=azure.100)) wordt aangeroepen.
5. Het exemplaar is gemarkeerd als **gereed** en het verkeer wordt doorgestuurd naar het exemplaar.
6. De methode [micro soft. WindowsAzure. ServiceRuntime. RoleEntryPoint. run](/previous-versions/azure/reference/ee772746(v=azure.100)) wordt aangeroepen.

## <a name="example-of-a-startup-task"></a>Voor beeld van een opstart taak
Opstart taken worden gedefinieerd in het bestand [ServiceDefinition. csdef] in het element **Task** . Het kenmerk **commandline** geeft de naam en de para meters van de opdracht Startup batch bestand of console aan, het kenmerk **executionContext** geeft het Bevoegdheids niveau van de opstart taak aan en het kenmerk **TaskType** geeft aan hoe de taak wordt uitgevoerd.

In dit voor beeld wordt een omgevings variabele, **MyVersionNumber**, gemaakt voor de opstart taak en ingesteld op de waarde "**1.0.0.0**".

**ServiceDefinition. csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

In het volgende voor beeld schrijft het batch bestand **Startup. cmd** de regel ' de huidige versie is 1.0.0.0 ' naar het StartupLog.txt-bestand in de map die is opgegeven door de omgevings variabele TEMP. De `EXIT /B 0` regel zorgt ervoor dat de opstart taak eindigt met een **Error level** van nul.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> In Visual Studio moet de eigenschap **kopiëren naar uitvoermap** voor het batch bestand voor opstarten altijd worden ingesteld op **kopiëren** om ervoor te zorgen dat uw opstart batch bestand correct is geïmplementeerd in uw project op Azure (**AppRoot \\ bin** voor webrollen en **AppRoot** voor werk rollen).
> 
> 

## <a name="description-of-task-attributes"></a>Beschrijving van de taakkenmerken
Hieronder worden de kenmerken van het element **Task** in het bestand [ServiceDefinition. csdef] beschreven:

**commandline** -Hiermee geeft u de opdracht regel voor de opstart taak op:

* De opdracht, met optionele opdracht regel parameters, waarmee de opstart taak wordt gestart.
* Vaak is dit de bestands naam van een. cmd-of. bat batch-bestand.
* De taak is relatief ten opzichte van de AppRoot \\ bin-map voor de implementatie. Omgevings variabelen worden niet uitgebreid bij het bepalen van het pad en het bestand van de taak. Als omgevings uitbreiding vereist is, kunt u een klein. cmd-script maken dat de opstart taak aanroept.
* Dit kan een console toepassing zijn of een batch bestand waarmee een [Power shell-script](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)wordt gestart.

**executionContext** : Hiermee geeft u het bevoegdheden niveau voor de opstart taak op. Het bevoegdheden niveau kan worden beperkt of verhoogd:

* **aantal**  
  De opstart taak wordt uitgevoerd met dezelfde bevoegdheden als de rol. Wanneer het kenmerk **executionContext** voor het [runtime] -element ook is **beperkt**, worden er gebruikers bevoegdheden gebruikt.
* **verhoogde**  
  De opstart taak wordt uitgevoerd met beheerders bevoegdheden. Op die manier kunt u opstart taken uitvoeren om Program ma's te installeren, wijzigingen in de IIS-configuratie aan te brengen, register wijzigingen uit te voeren en andere beheer niveau taken, zonder het Bevoegdheids niveau van de rol zelf te verhogen.  

> [!NOTE]
> Het bevoegdheden niveau van een opstart taak hoeft niet hetzelfde te zijn als de rol zelf.
> 
> 

**TaskType** : Hiermee geeft u op hoe een opstart taak wordt uitgevoerd.

* **Simple**  
  Taken worden synchroon uitgevoerd, een voor een in de volg orde die is opgegeven in het bestand [ServiceDefinition. csdef] . Wanneer één **eenvoudige** opstart taak eindigt met een **Error level** van nul, wordt de volgende **eenvoudige** opstart taak uitgevoerd. Als er geen **eenvoudige** opstart taken kunnen worden uitgevoerd, wordt de rol zelf gestart.   
  
  > [!NOTE]
  > Als de **eenvoudige** taak eindigt met een niet-nul- **Error level**, wordt het exemplaar geblokkeerd. Volgende **eenvoudige** opstart taken en de rol zelf, worden niet gestart.
  > 
  > 
  
    Om ervoor te zorgen dat uw batch bestand eindigt met een **Error level** van nul, voert u de opdracht `EXIT /B 0` uit aan het einde van het batch-bestand proces.
* **background**  
  Taken worden asynchroon uitgevoerd, parallel met het opstarten van de rol.
* **vormen**  
  Taken worden asynchroon uitgevoerd, parallel met het opstarten van de rol. Het belangrijkste verschil tussen een voor **grond** en een **achtergrond** taak is dat een **voorgrond** taak voor komt dat de rol wordt gerecycled of afgesloten totdat de taak is beëindigd. Voor de **achtergrond** taken gelden deze beperkingen niet.

## <a name="environment-variables"></a>Omgevingsvariabelen
Omgevings variabelen zijn een manier om informatie door te geven aan een opstart taak. U kunt bijvoorbeeld het pad naar een BLOB plaatsen die een programma bevat dat moet worden geïnstalleerd, of poort nummers die door uw rol worden gebruikt, of instellingen voor het beheren van de functies van de opstart taak.

Er zijn twee soorten omgevings variabelen voor opstart taken. statische omgevings variabelen en omgevings variabelen op basis van leden van de klasse [RoleEnvironment] . Beide bevinden zich in het gedeelte [omgeving] van het bestand [ServiceDefinition. csdef] en gebruiken beide het kenmerk [Variable] en **name** .

Statische omgevings variabelen maakt gebruik van het kenmerk **Value** van het element [Variable] . In het bovenstaande voor beeld wordt de omgevings variabele **MyVersionNumber** met de statische waarde "**1.0.0.0**" gemaakt. Een ander voor beeld is het maken van een **StagingOrProduction** -omgevings variabele die u hand matig kunt instellen op de waarden "**staging**" of "**productie**" om verschillende opstart acties uit te voeren op basis van de waarde van de omgevings variabele **StagingOrProduction** .

Omgevings variabelen op basis van de leden van de klasse RoleEnvironment gebruiken niet het kenmerk **Value** van het element [Variable] . In plaats daarvan wordt het onderliggende [RoleInstanceValue] -element, met de juiste **XPath** -kenmerk waarde, gebruikt om een omgevings variabele te maken op basis van een specifiek lid van de klasse [RoleEnvironment] . De waarden voor het **XPath** -kenmerk voor toegang tot verschillende [RoleEnvironment] -waarden kunt u [hier](cloud-services-role-config-xpath.md)vinden.

Als u bijvoorbeeld een omgevings variabele wilt maken die '**True**' is wanneer het exemplaar wordt uitgevoerd in de compute-emulator en '**False**' wordt uitgevoerd in de Cloud, gebruikt u de volgende [variabele] en [RoleInstanceValue] elementen:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het uitvoeren van enkele [veelvoorkomende opstart taken](cloud-services-startup-tasks-common.md) met uw Cloud service.

[Verpakken](cloud-services-model-and-package.md) uw Cloud service.  

[ServiceDefinition. csdef]: cloud-services-model-and-package.md#csdef
[Taak]: /previous-versions/azure/reference/gg557552(v=azure.100)#Task
[Opstarten]: /previous-versions/azure/reference/gg557552(v=azure.100)#Startup
[Runtime]: /previous-versions/azure/reference/gg557552(v=azure.100)#Runtime
[Omgeving]: /previous-versions/azure/reference/gg557552(v=azure.100)#Environment
[Variabele]: /previous-versions/azure/reference/gg557552(v=azure.100)#Variable
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue
[RoleEnvironment]: /previous-versions/azure/reference/ee773173(v=azure.100)