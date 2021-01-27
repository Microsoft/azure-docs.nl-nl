---
title: Azure-Service Fabric-Service Fabric toepassing sleutel kluis verwijzingen gebruiken
description: In dit artikel wordt uitgelegd hoe u de KeyVaultReference-ondersteuning van service Fabric gebruikt voor toepassings geheimen.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: a0e4ef0decae8cc9ab4dc5f8c69dfef854af81f3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898593"
---
# <a name="keyvaultreference-support-for-azure-deployed-service-fabric-applications"></a>KeyVaultReference-ondersteuning voor Azure-geïmplementeerde Service Fabric-toepassingen

Een veelvoorkomende uitdaging bij het bouwen van Cloud toepassingen is het veilig distribueren van geheimen aan uw toepassingen. U kunt bijvoorbeeld een database sleutel implementeren voor uw toepassing zonder de sleutel weer te geven tijdens de pijp lijn of de operator. Met de KeyVaultReference-ondersteuning van Service Fabric kunt u eenvoudig geheimen voor uw toepassingen implementeren door te verwijzen naar de URL van het geheim dat is opgeslagen in Key Vault. Service Fabric gaat het ophalen van dat geheim namens de beheerde identiteit van uw toepassing op en activeert de toepassing met het geheim.

> [!NOTE]
> KeyVaultReference-ondersteuning voor Service Fabric-toepassingen is GA (out-of-preview), te beginnen met Service Fabric versie 7,2 CU5. U kunt het beste een upgrade naar deze versie uitvoeren voordat u deze functie gebruikt.

> [!NOTE]
> KeyVaultReference-ondersteuning voor Service Fabric-toepassingen ondersteunt alleen [versie](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) -geheimen. Geheimen zonder versie worden niet ondersteund. De Key Vault moet zich in hetzelfde abonnement benemen als uw service Fabric-cluster. 

## <a name="prerequisites"></a>Vereisten

- Beheerde identiteit voor Service Fabric toepassingen

    Service Fabric KeyVaultReference-ondersteuning maakt gebruik van de beheerde identiteit van een toepassing om geheimen namens de toepassing op te halen. Daarom moet uw toepassing worden geïmplementeerd via en een beheerde identiteit worden toegewezen. Volg dit [document](concepts-managed-identity.md) om beheerde identiteit in te scha kelen voor uw toepassing.

- Archief met centrale geheimen (CSS).

    Central geheimen Store (CSS) is de versleutelde lokale geheimen cache van Service Fabric. Deze functie maakt gebruik van CSS om geheimen te beveiligen en persistent te maken nadat ze zijn opgehaald uit Key Vault. Het inschakelen van deze optionele systeem service is ook vereist om deze functie te gebruiken. Volg dit [document](service-fabric-application-secret-store.md) om CSS in te scha kelen en te configureren.

- Machtigingen voor beheerde identiteits toegang verlenen aan de sleutel kluis

    Raadpleeg dit [document](how-to-grant-access-other-resources.md) voor meer informatie over het verlenen van beheerde identiteits toegang tot de sleutel kluis. Opmerking Als u een door het systeem toegewezen beheerde identiteit gebruikt, wordt de beheerde identiteit alleen gemaakt na de implementatie van de toepassing. Dit kan race voorwaarden maken waarbij de toepassing toegang probeert te krijgen tot het geheim voordat de identiteit toegang kan krijgen tot de kluis. De naam van de toegewezen identiteit van het systeem is `{cluster name}/{application name}/{service name}` .
    
## <a name="use-keyvaultreferences-in-your-application"></a>KeyVaultReferences gebruiken in uw toepassing
KeyVaultReferences kunnen op verschillende manieren worden gebruikt
- [Als een omgevings variabele](#as-an-environment-variable)
- [Gekoppeld als een bestand in uw container](#mounted-as-a-file-into-your-container)
- [Als referentie voor een wacht woord voor een container opslagplaats](#as-a-reference-to-a-container-repository-password)

### <a name="as-an-environment-variable"></a>Als een omgevings variabele

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
</EnvironmentVariables>
```

```C#
string secret =  Environment.GetEnvironmentVariable("MySecret");
```

### <a name="mounted-as-a-file-into-your-container"></a>Gekoppeld als een bestand in uw container

- Een sectie toevoegen aan settings.xml

    `MySecret`Para meter met het `KeyVaultReference` type en de waarde definiëren`<KeyVaultURL>`

    ```xml
    <Section Name="MySecrets">
        <Parameter Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
    </Section>
    ```

- Raadpleeg de nieuwe sectie in ApplicationManifest.xml in `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="MyServiceMI" ApplicationIdentityRef="MyApplicationMI" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="MySecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- De geheimen van de service code gebruiken

    Elke para meter die onder wordt weer gegeven `<Section  Name=MySecrets>` , is een bestand in de map waarnaar wordt aangegeven door EnvironmentVariable SecretPath. Het onderstaande code fragment van C# laat zien hoe u MySecret van uw toepassing kunt lezen.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "MySecret"))) 
    {
        string secret =  sr.ReadToEnd();
    }
    ```
    > [!NOTE] 
    > Koppel punt regelt de map waar de bestanden met geheime waarden worden gekoppeld.

### <a name="as-a-reference-to-a-container-repository-password"></a>Als referentie voor een wacht woord voor een container opslagplaats

```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="MyACRUser" Type="KeyVaultReference" Password="<KeyVaultURL>"/>
      </ContainerHostPolicies>
```

## <a name="next-steps"></a>Volgende stappen

* [Documentatie voor Azure-sleutel kluis](../key-vault/index.yml)
* [Meer informatie over het centrale geheime archief](service-fabric-application-secret-store.md)
* [Meer informatie over beheerde identiteit voor Service Fabric toepassingen](concepts-managed-identity.md)
