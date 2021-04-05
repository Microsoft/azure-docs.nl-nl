---
title: Cloud Services van Azure (uitgebreide ondersteuning) def. webrole-schema | Microsoft Docs
description: Informatie met betrekking tot de webrol voor Cloud Services (uitgebreide ondersteuning)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: fef3e3cc63fb9e1ca6aa64cf799a620f187db76f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98744502"
---
# <a name="azure-cloud-services-extended-support-definition-webrole-schema"></a>Webrole-schema voor de definitie van Azure Cloud Services (uitgebreide ondersteuning)

De Azure-webfunctie is een rol die is aangepast voor programmering van webtoepassingen zoals ondersteund door IIS 7, zoals ASP.NET, PHP, Windows Communication Foundation en FastCGI.

De standaard extensie voor het service definitie bestand is csdef.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Basis-service definitie schema voor een webrole  
De basis indeling van een service definitie bestand met een webrole is als volgt.

```xml
<ServiceDefinition …>  
  <WebRole name="<web-role-name>" vmsize="<web-role-size>" enableNativeCodeExecution="[true|false]">  
    <Certificates>  
      <Certificate name="<certificate-name>" storeLocation="<certificate-store>" storeName="<store-name>" />  
    </Certificates>      
    <ConfigurationSettings>  
      <Setting name="<setting-name>" />  
    </ConfigurationSettings>  
    <Imports>  
      <Import moduleName="<import-module>"/>  
    </Imports>  
    <Endpoints>  
      <InputEndpoint certificate="<certificate-name>" ignoreRoleInstanceStatus="[true|false]" name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<port-number>" port="<port-number>" loadBalancerProbe="<load-balancer-probe-name>" />  
      <InternalEndpoint name="<internal-endpoint-name>" protocol="[http|tcp|udp|any]" port="<port-number>">  
         <FixedPort port="<port-number>"/>  
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
      </InternalEndpoint>  
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">  
         <AllocatePublicPortFrom>  
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
         </AllocatePublicPortFrom>  
      </InstanceInputEndpoint>  
    </Endpoints>  
    <LocalResources>  
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    </LocalResources>  
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    <Runtime executionContext="[limited|elevated]">  
      <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
      </Environment>  
      <EntryPoint>  
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>  
      </EntryPoint>  
    </Runtime>  
    <Sites>  
      <Site name="<web-site-name>">  
        <VirtualApplication name="<application-name>" physicalDirectory="<directory-path>"/>  
        <VirtualDirectory name="<directory-path>" physicalDirectory="<directory-path>"/>  
        <Bindings>  
          <Binding name="<binding-name>" endpointName="<endpoint-name-bound-to>" hostHeader="<url-of-the-site>"/>  
        </Bindings>  
      </Site>  
    </Sites>  
    <Startup priority="<for-internal-use-only>">  
      <Task commandLine="<command-to=execute>" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">  
        <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
        </Environment>  
      </Task>  
    </Startup>  
    <Contents>  
      <Content destination="<destination-folder-name>" >  
        <SourceDirectory path="<local-source-directory>" />  
      </Content>  
    </Contents>  
  </WebRole>  
</ServiceDefinition>  
```  

## <a name="schema-elements"></a>Schema-elementen  
Het service definitie bestand bevat deze elementen, zoals beschreven in de volgende secties in dit onderwerp:  

[Webrol](#WebRole)

[ConfigurationSettings](#ConfigurationSettings)

[Instelling](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Eindpunten](#Endpoints)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certificaten](#Certificates)

[Certificaat](#Certificate)

[Rusland](#Imports)

[Importeren](#Import)

[Runtime](#Runtime)

[Omgeving](#Environment)

[Variabele](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[Sites](#Sites)

[Site](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Bindingen](#Bindings)

[Binding](#Binding)

[Opstarten](#Startup)

[Taak](#Task)

[Inhoud](#Contents)

[Inhoud](#Content)

[Source Directory](#SourceDirectory)

##  <a name="webrole"></a><a name="WebRole"></a> Webrol  
Het `WebRole` element beschrijft een rol die is aangepast voor het Program meren van webtoepassingen, zoals ondersteund door IIS 7 en ASP.net. Een service kan nul of meer webrollen bevatten.

In de volgende tabel worden de kenmerken van het `WebRole` element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|naam|tekenreeks|Vereist. De naam voor de webrole. De naam van de rol moet uniek zijn.|  
|enableNativeCodeExecution|booleaans|Optioneel. De standaard waarde is `true` ; systeem eigen code-uitvoering en volledig vertrouwen zijn standaard ingeschakeld. Stel dit kenmerk in op `false` het uitschakelen van systeem eigen code voor de webfunctie en gebruik in plaats daarvan Azure gedeeltelijk vertrouwen.|  
|vmsize|tekenreeks|Optioneel. Stel deze waarde in om de grootte van de virtuele machine te wijzigen die aan de rol is toegewezen. De standaardwaarde is `Small`. Zie [grootten van virtuele machines voor Cloud Services](available-sizes.md)voor meer informatie.|  

##  <a name="configurationsettings"></a><a name="ConfigurationSettings"></a> ConfigurationSettings  
Het `ConfigurationSettings` element beschrijft het verzamelen van configuratie-instellingen voor een webrole. Dit element is de bovenliggende elementen van het `Setting` element.

##  <a name="setting"></a><a name="Setting"></a> Stelt  
Het `Setting` element beschrijft een naam en een waardepaar waarmee een configuratie-instelling voor een exemplaar van een rol wordt opgegeven.

In de volgende tabel worden de kenmerken van het `Setting` element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|naam|tekenreeks|Vereist. Een unieke naam voor de configuratie-instelling.|  

De configuratie-instellingen voor een rol zijn naam-en waardeparen die in het service definitie bestand zijn gedeclareerd en in het service configuratie bestand zijn ingesteld.

##  <a name="localresources"></a><a name="LocalResources"></a> LocalResources  
Het `LocalResources` element beschrijft de verzameling van lokale opslag resources voor een webrole. Dit element is de bovenliggende elementen van het `LocalStorage` element.

##  <a name="localstorage"></a><a name="LocalStorage"></a> LocalStorage  
Het `LocalStorage` element identificeert een lokale opslag resource die bestandssysteem ruimte biedt voor de service tijdens runtime. Een rol kan nul of meer lokale opslag resources definiëren.

> [!NOTE]
>  Het `LocalStorage` element kan worden weer gegeven als een onderliggend item van het `WebRole` element ter ondersteuning van compatibiliteit met eerdere versies van de Azure SDK.

In de volgende tabel worden de kenmerken van het `LocalStorage` element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|naam|tekenreeks|Vereist. Een unieke naam voor het lokale archief.|  
|cleanOnRoleRecycle|booleaans|Optioneel. Hiermee wordt aangegeven of het lokale archief moet worden gereinigd wanneer de rol opnieuw wordt gestart. De standaardwaarde is `true`.|  
|Sizeinmb kleiner|int|Optioneel. De gewenste hoeveelheid opslag ruimte die voor het lokale archief moet worden toegewezen, in MB. Als dat niet is opgegeven, is de standaard toegewezen opslag ruimte 100 MB. De minimale hoeveelheid opslag ruimte die kan worden toegewezen, is 1 MB.<br /><br /> De maximale grootte van de lokale resources is afhankelijk van de grootte van de virtuele machine. Zie [grootten van virtuele machines voor Cloud Services](available-sizes.md)voor meer informatie.|  
  
De naam van de map die is toegewezen aan de lokale opslag Resource komt overeen met de waarde die is gegeven voor het naam kenmerk.

##  <a name="endpoints"></a><a name="Endpoints"></a> Eind punten  
Het `Endpoints` element beschrijft de verzameling invoer-eind punten (extern), intern en exemplaar invoer eindpunten voor een rol. Dit element is het bovenliggende item van `InputEndpoint` de `InternalEndpoint` elementen, en `InstanceInputEndpoint` .

De invoer en interne eind punten worden afzonderlijk toegewezen. Een service kan in totaal 25 invoer-, interne en exemplaar invoer-eind punten bevatten die kunnen worden toegewezen in de 25 rollen die in een service zijn toegestaan. Als u bijvoorbeeld vijf rollen hebt, kunt u vijf invoer eindpunten per rol toewijzen, of u kunt 25 invoer eindpunten toewijzen aan één rol, maar u kunt ook één invoer eindpunt toewijzen aan 25 rollen.

> [!NOTE]
>  Voor elke geïmplementeerde rol is één exemplaar per rol vereist. De standaard inrichting voor een abonnement is beperkt tot 20 kernen en is daarom beperkt tot 20 exemplaren van een rol. Als uw toepassing meer exemplaren vereist dan wordt geleverd door de standaard inrichting, Zie [facturering, abonnements beheer en quotum ondersteuning](https://azure.microsoft.com/support/options/) voor meer informatie over het verhogen van uw quotum.

##  <a name="inputendpoint"></a><a name="InputEndpoint"></a> InputEndpoint  
Het `InputEndpoint` element beschrijft een extern eind punt voor een webrole.

U kunt meerdere eind punten definiëren die een combi natie zijn van HTTP-, HTTPS-, UDP-en TCP-eind punten. U kunt elk poort nummer opgeven dat u voor een invoer eindpunt kiest, maar de opgegeven poort nummers voor elke rol in de service moeten uniek zijn. Als u bijvoorbeeld opgeeft dat een webrole gebruikmaakt van poort 80 voor HTTP en poort 443 voor HTTPS, kunt u opgeven dat een tweede Web Role poort 8080 gebruikt voor HTTP en poort 8043 voor HTTPS.

In de volgende tabel worden de kenmerken van het `InputEndpoint` element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|naam|tekenreeks|Vereist. Een unieke naam voor het externe eind punt.|  
|protocol|tekenreeks|Vereist. Het transport protocol voor het externe eind punt. Mogelijke waarden voor een webrole zijn `HTTP` ,, `HTTPS` , `UDP` of `TCP` .|  
|poort|int|Vereist. De poort voor het externe eind punt. U kunt een wille keurig poort nummer opgeven, maar de poort nummers die voor elke rol in de service zijn opgegeven, moeten uniek zijn.<br /><br /> Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1,7 of hoger).|  
|certificaat|tekenreeks|Vereist voor een HTTPS-eind punt. De naam van een certificaat dat door een `Certificate` element is gedefinieerd.|  
|localPort|int|Optioneel. Hiermee geeft u een poort op die wordt gebruikt voor interne verbindingen op het eind punt. Het `localPort` kenmerk wijst de externe poort op het eind punt toe aan een interne poort van een rol. Dit is handig in scenario's waarbij een rol moet communiceren met een intern onderdeel op een poort die afwijkt van de functie die extern wordt weer gegeven.<br /><br /> Als dat niet is opgegeven, is de waarde van `localPort` gelijk aan het `port` kenmerk. Stel de waarde van `localPort` in op ' * ' om automatisch een niet-toegewezen poort toe te wijzen die kan worden gedetecteerd met behulp van de runtime-API.<br /><br /> Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1,7 of hoger).<br /><br /> Het `localPort` kenmerk is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.|  
|ignoreRoleInstanceStatus|booleaans|Optioneel. Wanneer de waarde van dit kenmerk is ingesteld op `true` , wordt de status van een service genegeerd en wordt het eind punt niet verwijderd door de Load Balancer. Stel deze waarde in op `true` nuttig voor het opsporen van fouten in de bezette instanties van een service. De standaardwaarde is `false`. **Opmerking:**  Een eind punt kan nog steeds verkeer ontvangen, zelfs wanneer de rol niet de status gereed heeft.|  
|loadBalancerProbe|tekenreeks|Optioneel. De naam van de load balancer-test die is gekoppeld aan het invoer eindpunt. Zie [LoadBalancerProbe-schema](schema-csdef-loadbalancerprobe.md)voor meer informatie.|  

##  <a name="internalendpoint"></a><a name="InternalEndpoint"></a> InternalEndpoint  
Het `InternalEndpoint` element beschrijft een intern eind punt voor een webrole. Een intern eind punt is alleen beschikbaar voor andere rolinstanties die in de service worden uitgevoerd. het is niet beschikbaar voor clients buiten de service. Webrollen die het element niet bevatten `Sites` , kunnen slechts één http-, UDP-of TCP-intern eind punt hebben.

In de volgende tabel worden de kenmerken van het `InternalEndpoint` element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|naam|tekenreeks|Vereist. Een unieke naam voor het interne eind punt.|  
|protocol|tekenreeks|Vereist. Het transport protocol voor het interne eind punt. Mogelijke waarden zijn `HTTP` , `TCP` , `UDP` , of `ANY` .<br /><br /> Een waarde van `ANY` geeft aan dat een protocol, elke poort, is toegestaan.|  
|poort|int|Optioneel. De poort die wordt gebruikt voor verbindingen met interne taak verdeling op het eind punt. Een eind punt met taak verdeling gebruikt twee poorten. De poort die wordt gebruikt voor het open bare IP-adres en de poort die wordt gebruikt op het privé-IP-adres. Deze zijn meestal ingesteld op hetzelfde, maar u kunt ervoor kiezen om andere poorten te gebruiken.<br /><br /> Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1,7 of hoger).<br /><br /> Het `Port` kenmerk is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.|  

##  <a name="instanceinputendpoint"></a><a name="InstanceInputEndpoint"></a> InstanceInputEndpoint  
Het `InstanceInputEndpoint` element beschrijft een invoer eindpunt van een exemplaar naar een webrole. Een invoer eindpunt van een instantie wordt gekoppeld aan een specifieke rolinstantie door het gebruik van poort door te sturen in de load balancer. Elk invoer eindpunt van de instantie wordt toegewezen aan een specifieke poort uit een bereik van mogelijke poorten. Dit element is de bovenliggende elementen van het `AllocatePublicPortFrom` element.

Het `InstanceInputEndpoint` element is alleen beschikbaar via de Azure SDK-versie 1,7 of hoger.

In de volgende tabel worden de kenmerken van het `InstanceInputEndpoint` element beschreven.
  
| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|naam|tekenreeks|Vereist. Een unieke naam voor het eind punt.|  
|localPort|int|Vereist. Hiermee geeft u de interne poort op waarnaar alle rolinstanties worden geluisterd om binnenkomend verkeer te ontvangen dat vanuit het load balancer wordt doorgestuurd. Mogelijke waarden variëren tussen 1 en 65535, inclusief.|  
|protocol|tekenreeks|Vereist. Het transport protocol voor het interne eind punt. Mogelijke waarden zijn `udp` en `tcp`. Gebruiken `tcp` voor HTTP/HTTPS-verkeer.|  
  
##  <a name="allocatepublicportfrom"></a><a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom  
Het `AllocatePublicPortFrom` element beschrijft het open bare poort bereik dat door externe klanten kan worden gebruikt voor toegang tot elk invoer eindpunt van het exemplaar. Het open bare (VIP) poort nummer wordt toegewezen vanuit dit bereik en toegewezen aan elk eind punt van de afzonderlijke Role-instantie tijdens de Tenant implementatie en-update. Dit element is de bovenliggende elementen van het `FixedPortRange` element.

Het `AllocatePublicPortFrom` element is alleen beschikbaar via de Azure SDK-versie 1,7 of hoger.

##  <a name="fixedport"></a><a name="FixedPort"></a> FixedPort  
`FixedPort`Met het element wordt de poort voor het interne eind punt opgegeven, waarmee verbindingen met gelijke taak verdeling op het eind punt worden ingeschakeld.

Het `FixedPort` element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

In de volgende tabel worden de kenmerken van het `FixedPort` element beschreven.

| Kenmerk | Type | Description |  
| --------- | ---- | ----------- |  
|poort|int|Vereist. De poort voor het interne eind punt. Dit heeft hetzelfde effect als het instellen van de `FixedPortRange` minimum-en maximum waarde op dezelfde poort.<br /><br /> Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1,7 of hoger).|  

##  <a name="fixedportrange"></a><a name="FixedPortRange"></a> FixedPortRange  
Het `FixedPortRange` element geeft het bereik aan van poorten die zijn toegewezen aan het interne eind punt of het invoer eindpunt van het exemplaar en stelt de poort in die wordt gebruikt voor verbindingen met gelijke taak verdeling op het eind punt.

> [!NOTE]
>  Het `FixedPortRange` element werkt anders, afhankelijk van het element waarin het zich bevindt. Wanneer het `FixedPortRange` element zich in het element bevindt `InternalEndpoint` , worden alle poorten op de Load Balancer geopend binnen het bereik van de minimum-en maximum kenmerken voor alle virtuele machines waarop de functie wordt uitgevoerd. Wanneer het `FixedPortRange` element zich in het element bevindt `InstanceInputEndpoint` , wordt er slechts één poort geopend binnen het bereik van de minimum-en maximum kenmerken van elke virtuele machine waarop de functie wordt uitgevoerd.

Het `FixedPortRange` element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

In de volgende tabel worden de kenmerken van het `FixedPortRange` element beschreven.

| Kenmerk | Type | Description |  
| --------- | ---- | ----------- |  
|min.|int|Vereist. De minimale poort in het bereik. Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1,7 of hoger).|  
|max|tekenreeks|Vereist. De maximum poort in het bereik. Mogelijke waarden variëren tussen 1 en 65535, inclusief (Azure SDK-versie 1,7 of hoger).|  

##  <a name="certificates"></a><a name="Certificates"></a> Bewijzen  
Het `Certificates` element beschrijft de verzameling certificaten voor een webrole. Dit element is de bovenliggende elementen van het `Certificate` element. Een rol kan bestaan uit een wille keurig aantal gekoppelde certificaten. Zie [het service definitie bestand met een certificaat wijzigen](../cloud-services/cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files)voor meer informatie over het gebruik van het onderdeel certificaten.

##  <a name="certificate"></a><a name="Certificate"></a> Certificaat  
Het `Certificate` element beschrijft een certificaat dat is gekoppeld aan een webrole.

In de volgende tabel worden de kenmerken van het `Certificate` element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|naam|tekenreeks|Vereist. Een naam voor dit certificaat, dat wordt gebruikt om ernaar te verwijzen wanneer het is gekoppeld aan een HTTPS- `InputEndpoint` element.|  
|storeLocation|tekenreeks|Vereist. De locatie van het certificaat archief waar dit certificaat op de lokale computer kan worden gevonden. Mogelijke waarden zijn `CurrentUser` en `LocalMachine` .|  
|storeName|tekenreeks|Vereist. De naam van het certificaat archief waar dit certificaat zich bevindt op de lokale computer. Mogelijke waarden zijn de ingebouwde winkel namen,,,,,,,, `My` `Root` `CA` `Trust` `Disallowed` `TrustedPeople` `TrustedPublisher` `AuthRoot` `AddressBook` , of een aangepaste archief naam. Als een aangepaste archief naam is opgegeven, wordt het archief automatisch gemaakt.|  
|permissionLevel|tekenreeks|Optioneel. Hiermee geeft u de toegangs machtigingen op die aan de rollen processen worden gegeven. Als u wilt dat alleen verhoogde processen toegang hebben tot de persoonlijke sleutel, geeft u vervolgens een `elevated` machtiging op. `limitedOrElevated` met machtiging kunnen alle rollen processen toegang krijgen tot de persoonlijke sleutel. Mogelijke waarden zijn `limitedOrElevated` en `elevated`. De standaardwaarde is `limitedOrElevated`.|  

##  <a name="imports"></a><a name="Imports"></a> Rusland  
Het `Imports` element beschrijft een verzameling import modules voor een webrole waarmee onderdelen aan het gast besturingssysteem worden toegevoegd. Dit element is de bovenliggende elementen van het `Import` element. Dit element is optioneel en een rol kan slechts één import blok hebben. 

Het `Imports` element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

##  <a name="import"></a><a name="Import"></a> Wederinvoer  
Het `Import` element geeft een module op die aan het gast besturingssysteem moet worden toegevoegd.

Het `Import` element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

In de volgende tabel worden de kenmerken van het `Import` element beschreven.

| Kenmerk | Type | Description |  
| --------- | ---- | ----------- |  
|moduleName|tekenreeks|Vereist. De naam van de module die u wilt importeren. Geldige import modules zijn:<br /><br /> -RemoteAccess<br />- RemoteForwarder<br />-Diagnostische gegevens<br /><br /> Met de modules RemoteAccess en RemoteForwarder kunt u uw rolinstantie voor extern bureau blad-verbindingen configureren. Zie [extensies](extensions.md)voor meer informatie.<br /><br /> Met de module diagnostiek kunt u Diagnostische gegevens verzamelen voor een rolinstantie.|  

##  <a name="runtime"></a><a name="Runtime"></a> Gezamenlijke  
Het `Runtime` element beschrijft een verzameling instellingen voor een omgevings variabele voor een webrole waarmee de runtime omgeving van het Azure hostproces wordt beheerd. Dit element is de bovenliggende elementen van het `Environment` element. Dit element is optioneel en een rol kan slechts één runtime blok hebben.

Het `Runtime` element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

In de volgende tabel worden de kenmerken van het `Runtime` element beschreven:  

| Kenmerk | Type | Description |  
| --------- | ---- | ----------- |  
|executionContext|tekenreeks|Optioneel. Hiermee geeft u de context waarin het Role proces wordt gestart. De standaard context is `limited` .<br /><br /> -   `limited` : Het proces wordt gestart zonder beheerders bevoegdheden.<br />-   `elevated` : Het proces wordt gestart met Administrator bevoegdheden.|  

##  <a name="environment"></a><a name="Environment"></a> Variabelen  
Het `Environment` element beschrijft een verzameling instellingen voor een omgevings variabele voor een webrole. Dit element is de bovenliggende elementen van het `Variable` element. Er is mogelijk een aantal omgevings variabelen ingesteld voor een rol.

##  <a name="variable"></a><a name="Variable"></a> Variabeletype  
Het `Variable` element geeft een omgevings variabele op die in het gast besturingssysteem moet worden ingesteld.

Het `Variable` element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

In de volgende tabel worden de kenmerken van het `Variable` element beschreven:  

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|naam|tekenreeks|Vereist. De naam van de omgevings variabele die moet worden ingesteld.|  
|waarde|tekenreeks|Optioneel. De waarde die moet worden ingesteld voor de omgevings variabele. U moet een kenmerk value of een- `RoleInstanceValue` element bevatten.|  

##  <a name="roleinstancevalue"></a><a name="RoleInstanceValue"></a> RoleInstanceValue  
Het `RoleInstanceValue` element geeft het xPath op waaruit de waarde van de variabele moet worden opgehaald.

In de volgende tabel worden de kenmerken van het `RoleInstanceValue` element beschreven.

| Kenmerk | Type | Description |  
| --------- | ---- | ----------- |  
|XPath|tekenreeks|Optioneel. Locatiepad van de implementatie-instellingen voor het exemplaar. Zie [Configuration Varia bles with XPath](../cloud-services/cloud-services-role-config-xpath.md)(Engelstalig) voor meer informatie.<br /><br /> U moet een kenmerk value of een- `RoleInstanceValue` element bevatten.|  

##  <a name="entrypoint"></a><a name="EntryPoint"></a> Toegangs  
Het `EntryPoint` element geeft u het toegangs punt voor een rol op. Dit element is de bovenliggende elementen van de `NetFxEntryPoint` onderdelen. Met deze elementen kunt u een andere toepassing dan de standaard WaWorkerHost.exe opgeven om te fungeren als het ingangs punt van de rol.

Het `EntryPoint` element is alleen beschikbaar via de Azure SDK-versie 1,5 of hoger.

##  <a name="netfxentrypoint"></a><a name="NetFxEntryPoint"></a> NetFxEntryPoint  
Het `NetFxEntryPoint` element geeft het programma op dat moet worden uitgevoerd voor een rol.

> [!NOTE]
>  Het `NetFxEntryPoint` element is alleen beschikbaar via de Azure SDK-versie 1,5 of hoger.

In de volgende tabel worden de kenmerken van het `NetFxEntryPoint` element beschreven.

| Kenmerk | Type | Description |  
| --------- | ---- | ----------- |  
|assemblyName|tekenreeks|Vereist. Het pad en de bestands naam van de assembly met het toegangs punt. Het pad is relatief ten opzichte van de map **\\ %ROLEROOT%\Approot** (in **\\** `commandLine` wordt aangenomen dat er geen%ROLEROOT%\Approot is opgegeven). **% ROLEROOT%** is een omgevings variabele die wordt onderhouden door Azure en vertegenwoordigt de locatie van de hoofdmap voor uw rol. De map **\\ %ROLEROOT%\Approot** vertegenwoordigt de toepassingsmap voor uw rol.<br /><br /> Voor HWC-rollen is het pad altijd relatief ten opzichte van de map **\\ %ROLEROOT%\Approot\bin** .<br /><br /> Als de assembly niet kan worden gevonden ten opzichte van de map **\\ %ROLEROOT%\Approot** , wordt de **\\ %ROLEROOT%\APPROOT\BIN** doorzocht voor volledige IIS-en IIS Express webrollen.<br /><br /> Het gedrag van deze terugvallen voor volledige IIS is niet een aanbevolen best practice en kan in toekomstige versies worden verwijderd.|  
|targetFrameworkVersion|tekenreeks|Vereist. De versie van .NET Framework waarop de assembly is gebouwd. Bijvoorbeeld `targetFrameworkVersion="v4.0"`.|  

##  <a name="sites"></a><a name="Sites"></a> Sites  
Het `Sites` element beschrijft een verzameling websites en webtoepassingen die worden gehost in een webrole. Dit element is de bovenliggende elementen van het `Site` element. Als u geen `Sites` element opgeeft, wordt uw webrol gehost als verouderde webrol. u kunt slechts één website hebben gehost in uw webrole. Dit element is optioneel en een rol kan slechts één site blok hebben.

Het `Sites` element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

##  <a name="site"></a><a name="Site"></a> Site  
Met het `Site` element wordt een website of webtoepassing opgegeven die deel uitmaakt van de webrole.

Het `Site` element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

In de volgende tabel worden de kenmerken van het `Site` element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|naam|tekenreeks|Vereist. De naam van de website of toepassing.|  
|physicalDirectory|tekenreeks|De locatie van de inhoudsmap voor de hoofdmap van de site. De locatie kan worden opgegeven als een absoluut pad of relatief ten opzichte van de csdef-locatie.|  

##  <a name="virtualapplication"></a><a name="VirtualApplication"></a> VirtualApplication  
Het `VirtualApplication` element definieert een toepassing in Internet Information Services (IIS) 7 is een groep bestanden die inhoud levert of services biedt via protocollen, zoals http. Wanneer u een toepassing maakt in IIS 7, wordt het pad van de toepassing onderdeel van de URL van de site.

Het `VirtualApplication` element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

In de volgende tabel worden de kenmerken van het `VirtualApplication` element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|naam|tekenreeks|Vereist. Hiermee geeft u een naam op voor het identificeren van de virtuele toepassing.|  
|physicalDirectory|tekenreeks|Vereist. Hiermee geeft u het pad op de ontwikkel computer die de virtuele toepassing bevat. In de compute-emulator is IIS geconfigureerd om inhoud op deze locatie op te halen. Wanneer u implementeert naar Azure, wordt de inhoud van de fysieke map verpakt samen met de rest van de service. Wanneer het service pakket wordt geïmplementeerd in azure, wordt IIS geconfigureerd met de locatie van de uitgepakte inhoud.|  

##  <a name="virtualdirectory"></a><a name="VirtualDirectory"></a> VirtualDirectory  
Het `VirtualDirectory` element geeft een directory naam (ook wel pad genoemd) op die u opgeeft in IIS en wijst deze toe aan een fysieke map op een lokale of externe server.

Het `VirtualDirectory` element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

In de volgende tabel worden de kenmerken van het `VirtualDirectory` element beschreven.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|naam|tekenreeks|Vereist. Hiermee geeft u een naam op voor de virtuele map.|  
|waarde|physicalDirectory|Vereist. Hiermee geeft u het pad op de ontwikkelings machine met de website of de inhoud van de virtuele map. In de compute-emulator is IIS geconfigureerd om inhoud op deze locatie op te halen. Wanneer u implementeert naar Azure, wordt de inhoud van de fysieke map verpakt samen met de rest van de service. Wanneer het service pakket wordt geïmplementeerd in azure, wordt IIS geconfigureerd met de locatie van de uitgepakte inhoud.|  

##  <a name="bindings"></a><a name="Bindings"></a> Bindingen  
Het `Bindings` element beschrijft een verzameling bindingen voor een website. Het is het bovenliggende element van het `Binding` element. Het element is vereist voor elk `Site` element. Zie [communicatie inschakelen voor rolinstanties](../cloud-services/cloud-services-enable-communication-role-instances.md)voor meer informatie over het configureren van eind punten.

Het `Bindings` element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

##  <a name="binding"></a><a name="Binding"></a> Dwingen  
Het `Binding` element specificeert configuratie-informatie die is vereist voor aanvragen om te communiceren met een website of webtoepassing.

Het `Binding` element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

| Kenmerk | Type | Beschrijving |  
| --------- | ---- | ----------- |  
|naam|tekenreeks|Vereist. Hiermee geeft u een naam op om de binding aan te duiden.|  
|endpointName|tekenreeks|Vereist. Hiermee geeft u de naam van het eind punt waaraan moet worden gebonden.|  
|hostHeader|tekenreeks|Optioneel. Hiermee geeft u een hostnaam op waarmee u meerdere sites kunt hosten, met verschillende hostnamen, op één combi natie van IP-adres/poort nummer.|  

##  <a name="startup"></a><a name="Startup"></a> Opstarten  
Het `Startup` element beschrijft een verzameling taken die worden uitgevoerd wanneer de rol wordt gestart. Dit element kan de bovenliggende elementen van het `Variable` element zijn. Zie [opstart taken configureren](../cloud-services/cloud-services-startup-tasks.md)voor meer informatie over het gebruik van de functie opstart taken. Dit element is optioneel en een rol kan slechts één opstart blok hebben.

In de volgende tabel wordt het kenmerk van het `Startup` element beschreven.

| Kenmerk | Type | Description |  
| --------- | ---- | ----------- |  
|priority|int|Alleen voor intern gebruik.|  

##  <a name="task"></a><a name="Task"></a> Takenreeksuitvoeringsengine  
Het `Task` element geeft de opstart taak op die wordt uitgevoerd wanneer de rol wordt gestart. Opstart taken kunnen worden gebruikt om taken uit te voeren die de rol voorbereiden voor het uitvoeren van dergelijke installatie software componenten of het uitvoeren van andere toepassingen. Taken worden uitgevoerd in de volg orde waarin ze worden weer gegeven in het `Startup` element blok.

Het `Task` element is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

In de volgende tabel worden de kenmerken van het `Task` element beschreven.

| Kenmerk | Type | Description |  
| --------- | ---- | ----------- |  
|commandLine|tekenreeks|Vereist. Een script, zoals een CMD-bestand, dat de opdrachten bevat die moeten worden uitgevoerd. Opstart opdrachten en batch bestanden moeten worden opgeslagen in ANSI-indeling. Bestands indelingen die een byte volgorde markering instellen aan het begin van het bestand, worden niet goed verwerkt.|  
|executionContext|tekenreeks|Hiermee geeft u de context waarin het script wordt uitgevoerd.<br /><br /> -   `limited` [Standaard]: Voer uit met dezelfde bevoegdheden als voor de rol die het proces host.<br />-   `elevated` – Uitvoeren met beheerders bevoegdheden.|  
|taskType|tekenreeks|Hiermee geeft u het uitvoerings gedrag van de opdracht op.<br /><br /> -   `simple` [Standaard]: het systeem wacht tot de taak wordt afgesloten voordat andere taken worden gestart.<br />-   `background` – Het systeem wacht niet tot de taak is afgesloten.<br />-   `foreground` – Vergelijkbaar met achtergrond, behalve als de rol niet opnieuw wordt gestart totdat alle voorgrond taken worden afgesloten.|  

##  <a name="contents"></a><a name="Contents"></a> Gehele  
Het `Contents` element beschrijft het verzamelen van inhoud voor een webrole. Dit element is de bovenliggende elementen van het `Content` element.

Het `Contents` element is alleen beschikbaar via de Azure SDK-versie 1,5 of hoger.

##  <a name="content"></a><a name="Content"></a> Inhoudbeheer  
Het `Content` element definieert de bron locatie van de inhoud die moet worden gekopieerd naar de virtuele Azure-machine en het doelpad waarnaar deze wordt gekopieerd.

Het `Content` element is alleen beschikbaar via de Azure SDK-versie 1,5 of hoger.

In de volgende tabel worden de kenmerken van het `Content` element beschreven.

| Kenmerk | Type | Description |  
| --------- | ---- | ----------- |  
|doel|tekenreeks|Vereist. De locatie op de virtuele Azure-machine waarop de inhoud is geplaatst. Deze locatie is relatief ten opzichte van de map **%ROLEROOT%\Approot**.|  

Dit element is het bovenliggende element van het `SourceDirectory` element.

##  <a name="sourcedirectory"></a><a name="SourceDirectory"></a> Source Directory  
Het `SourceDirectory` element definieert de lokale map waaruit inhoud wordt gekopieerd. Gebruik dit element om de lokale inhoud op te geven die moet worden gekopieerd naar de virtuele machine van Azure.

Het `SourceDirectory` element is alleen beschikbaar via de Azure SDK-versie 1,5 of hoger.

In de volgende tabel worden de kenmerken van het `SourceDirectory` element beschreven.

| Kenmerk | Type | Description |  
| --------- | ---- | ----------- |  
|leertraject|tekenreeks|Vereist. Relatief of absoluut pad van een lokale map waarvan de inhoud wordt gekopieerd naar de virtuele machine van Azure. De uitbrei ding van omgevings variabelen in het mappad wordt ondersteund.|  
  
## <a name="see-also"></a>Zie ook
[Definitie schema van Cloud service (uitgebreide ondersteuning)](schema-csdef-file.md).




