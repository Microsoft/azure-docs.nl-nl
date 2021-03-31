---
title: Azure Service Fabric-toepassings geheimen beheren
description: Meer informatie over het beveiligen van geheime waarden in een Service Fabric-toepassing (platform-neutraal).
ms.topic: conceptual
ms.date: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: a11869c3b606ed9e74ce4f598109139fa1bb4164
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89012820"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Versleutelde geheimen in Service Fabric-toepassingen beheren
In deze hand leiding worden de stappen beschreven voor het beheren van geheimen in een Service Fabric-toepassing. Geheimen kunnen gevoelige informatie zijn, zoals verbindings reeksen voor opslag, wacht woorden of andere waarden die niet in tekst zonder opmaak moeten worden verwerkt.

Het gebruik van versleutelde geheimen in een Service Fabric toepassing bestaat uit drie stappen:
* Stel een versleutelings certificaat in en versleutel geheimen.
* Versleutelde geheimen in een toepassing opgeven.
* Versleutelde geheimen van service code ontsleutelen.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Een versleutelings certificaat instellen en geheimen versleutelen
Het instellen van een versleutelings certificaat voor het versleutelen van geheimen is afhankelijk van Windows en Linux.
* [Stel een versleutelings certificaat in en versleutel geheimen op Windows-clusters.][secret-management-windows-specific-link]
* [Een versleutelings certificaat instellen en geheimen op Linux-clusters versleutelen.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Versleutelde geheimen in een toepassing opgeven
In de vorige stap wordt beschreven hoe u een geheim versleutelt met een certificaat en een met base 64 gecodeerde teken reeks produceert voor gebruik in een toepassing. Deze met base 64 gecodeerde teken reeks kan worden opgegeven als een versleutelde [para meter][parameters-link] in de Settings.xml van een service of als een versleutelde [omgevings variabele][environment-variables-link] in de ServiceManifest.xml van een service.

Geef een versleutelde [para meter][parameters-link] in het Settings.xml configuratie bestand van uw service op waarvan het `IsEncrypted` kenmerk is ingesteld op `true` :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Geef een versleutelde [omgevings variabele][environment-variables-link] in het ServiceManifest.xml-bestand van uw service op waarvan het `Type` kenmerk is ingesteld op `Encrypted` :
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

De geheimen moeten ook worden opgenomen in uw Service Fabric-toepassing door een certificaat op te geven in het manifest van de toepassing. Voeg een **SecretsCertificate** -element toe aan **ApplicationManifest.xml** en voeg de vinger afdruk van het gewenste certificaat toe.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
> [!NOTE]
> Wanneer een toepassing wordt geactiveerd waarmee een SecretsCertificate wordt opgegeven, wordt in Service Fabric het overeenkomende certificaat gevonden en wordt de identiteit verleend die de toepassing uitvoert onder volledige machtigingen voor de persoonlijke sleutel van het certificaat. Service Fabric wordt ook het certificaat gecontroleerd op wijzigingen en de machtigingen dienovereenkomstig opnieuw toe te passen. Als u wijzigingen wilt detecteren voor certificaten die zijn gedeclareerd met een algemene naam, wordt door Service Fabric een periodieke taak uitgevoerd waarmee alle overeenkomende certificaten worden gevonden en vergeleken met een lijst met vinger afdrukken in de cache. Wanneer een nieuwe vinger afdruk wordt gedetecteerd, betekent dit dat een certificaat van dat onderwerp is verlengd. De taak wordt één keer per minuut uitgevoerd op elk knoop punt van het cluster.
>
> Terwijl de SecretsCertificate declaraties op basis van een onderwerp toestaat, moet u er rekening mee houden dat de versleutelde instellingen zijn gekoppeld aan het sleutel paar dat is gebruikt voor het versleutelen van de instelling op de client. U moet ervoor zorgen dat het oorspronkelijke versleutelings certificaat (of een equivalent) overeenkomt met de op onderwerp gebaseerde declaratie en dat deze is geïnstalleerd, inclusief de bijbehorende persoonlijke sleutel, op elk knoop punt van het cluster waarop de toepassing kan worden gehost. Alle tijdgebonden certificaten die overeenkomen met de op onderwerp gebaseerde declaratie en die zijn gebaseerd op hetzelfde sleutel paar als het oorspronkelijke versleutelings certificaat, worden beschouwd als gelijkwaardig.
>

### <a name="inject-application-secrets-into-application-instances"></a>Toepassings geheimen in toepassings exemplaren invoeren
In het ideale geval moet de implementatie naar verschillende omgevingen zo automatisch mogelijk worden uitgevoerd. Dit kan worden bereikt door geheime versleuteling uit te voeren in een bouw omgeving en de versleutelde geheimen op te geven als para meters bij het maken van toepassings exemplaren.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Overschrijf bare-para meters gebruiken in Settings.xml
Met het Settings.xml configuratie bestand kunt u Overschrijf bare-para meters opgeven die tijdens het maken van de toepassing kunnen worden opgegeven. Gebruik het `MustOverride` kenmerk in plaats van een waarde voor een para meter op te geven:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Als u de waarden in Settings.xml wilt overschrijven, declareert u een onderdrukkings parameter voor de service in ApplicationManifest.xml:

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

De waarde kan nu worden opgegeven als een *toepassings parameter* bij het maken van een exemplaar van de toepassing. Het maken van een toepassings exemplaar kan worden gescripteerd met Power shell of is geschreven in C#, voor een eenvoudige integratie in een bouw proces.

Met behulp van Power shell wordt de para meter aan de `New-ServiceFabricApplication` opdracht opgegeven als een [hash-tabel](/previous-versions/windows/it-pro/windows-powershell-1.0/ee692803(v=technet.10)):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Met C# worden toepassings parameters opgegeven in een `ApplicationDescription` as a `NameValueCollection` :

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Versleutelde geheimen ontsleutelen van service code
Met de Api's voor toegang tot [para meters][parameters-link] en [omgevings variabelen][environment-variables-link] kunt u versleutelde waarden eenvoudig ontsleutelen. Omdat de versleutelde teken reeks informatie bevat over het certificaat dat voor versleuteling wordt gebruikt, hoeft u het certificaat niet hand matig op te geven. Het certificaat hoeft alleen te worden geïnstalleerd op het knoop punt waarop de service wordt uitgevoerd.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>Volgende stappen
* Archief met Service Fabric [geheimen](service-fabric-application-secret-store.md) 
* Meer informatie over de [beveiliging van toepassingen en services](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[service fabric secrets store]: service-fabric-application-secret-store.md
