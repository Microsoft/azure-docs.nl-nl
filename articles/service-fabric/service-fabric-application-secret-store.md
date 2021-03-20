---
title: Archief met Azure Service Fabric Central-geheimen
description: In dit artikel wordt beschreven hoe u het archief centrale geheimen kunt gebruiken in azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: e9fd435803ad5354b0eb2d4f5de50009a8cbbfe2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88869752"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Archief met centrale geheimen in azure Service Fabric 
In dit artikel wordt beschreven hoe u centraal geheimen (CSS) in azure Service Fabric kunt gebruiken om geheimen te maken in Service Fabric toepassingen. CSS is een lokale archief cache voor het opslaan van gevoelige gegevens, zoals een wacht woord, tokens en sleutels, versleuteld in het geheugen.

  > [!NOTE] 
  > Wanneer CSS voor de eerste keer wordt geactiveerd vóór SF-versie 7,1. CU3, de activering kan mislukken en de status van CSS wordt permanent beschadigd als: CSS wordt geactiveerd op een Windows-geauthenticeerd cluster. CSS is op een cluster geactiveerd, maar `EncryptionCertificateThumbprint` is onjuist gedeclareerd of het bijbehorende certificaat is niet geïnstalleerd/acl's-Ed op knoop punten. Voor Windows-verificatie cluster gaat u naar 7,1. CU3 voordat u doorgaat. Voor andere clusters controleert u deze invarianties of gaat u naar 7,1. CU3.
  
## <a name="enable-central-secrets-store"></a>Archief met centrale geheimen inschakelen
Voeg het volgende script toe aan de cluster configuratie onder `fabricSettings` om CSS in te scha kelen. Het is raadzaam om een ander certificaat dan een cluster certificaat voor CSS te gebruiken. Zorg ervoor dat het versleutelings certificaat is geïnstalleerd op alle knoop punten en dat `NetworkService` Lees machtigingen heeft voor de persoonlijke sleutel van het certificaat.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "1"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>Een geheime resource declareren
U kunt een geheime bron maken met behulp van de REST API.
  > [!NOTE] 
  > Als het cluster gebruikmaakt van Windows-verificatie, wordt de REST-aanvraag verzonden via een onbeveiligd HTTP-kanaal. De aanbeveling is een x509-cluster te gebruiken met beveiligde eind punten.

Als u een `supersecret` geheime resource wilt maken met behulp van de rest API, maakt u een put-aanvraag naar `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview` . U hebt het cluster certificaat of het client certificaat van de beheerder nodig voor het maken van een geheime resource.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>De geheime waarde instellen

Gebruik het volgende script om de REST API te gebruiken om de geheime waarde in te stellen.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>Controleer de geheime waarde
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Het geheim gebruiken in uw toepassing

Volg deze stappen om het geheim in uw Service Fabric-toepassing te gebruiken.

1. Voeg een sectie toe aan het **settings.xml** -bestand met het volgende code fragment. Hier ziet u dat de waarde de indeling { `secretname:version` } heeft.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importeer de sectie in **ApplicationManifest.xml**.
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   De omgevings variabele `SecretPath` verwijst naar de map waarin alle geheimen zijn opgeslagen. Elke para meter die in de `testsecrets` sectie wordt vermeld, wordt opgeslagen in een afzonderlijk bestand. De toepassing kan het geheim nu als volgt gebruiken:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. De geheimen koppelen aan een container. De enige wijziging die is vereist om de geheimen beschikbaar te maken in de container, is op `specify` een koppel punt in `<ConfigPackage>` .
Het volgende code fragment is de gewijzigde **ApplicationManifest.xml**.  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   Geheimen zijn beschikbaar onder het koppel punt in uw container.

1. U kunt een geheim binden aan een proces omgevings variabele door op te geven `Type='SecretsStoreRef` . Het volgende code fragment is een voor beeld van het koppelen van de `supersecret` versie `ver1` aan de omgevings variabele `MySuperSecret` in **ServiceManifest.xml**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [beveiliging van toepassingen en services](service-fabric-application-and-service-security.md).
