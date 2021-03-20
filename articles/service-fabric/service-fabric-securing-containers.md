---
title: Certificaten importeren in een container
description: Meer informatie over het importeren van certificaat bestanden in een Service Fabric container service.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 219882a3f7f6db665f1ec311098ef53464773b71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92313685"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Een certificaat bestand importeren in een container die wordt uitgevoerd op Service Fabric

> [!NOTE]
> Voor Service Fabric-clusters die worden uitgevoerd op Azure, wordt aangeraden om de [beheerde identiteit](./concepts-managed-identity.md) van de toepassing te service Fabric gebruiken om toepassings certificaten vanuit een container in te richten. Beheerde identiteit biedt isolatie van geheimen en certificaten op het niveau van de service, en maakt het mogelijk dat het inrichten van toepassings certificaten deel uitmaakt van de werk stroom van de toepassing, in plaats van de werk stroom van de infra structuur. Het CertificateRef-mechanisme zal in een toekomstige release worden afgeschaft.

U kunt uw container Services beveiligen door een certificaat op te geven. Service Fabric biedt een mechanisme voor services binnen een container om toegang te krijgen tot een certificaat dat op de knoop punten in een Windows-of Linux-cluster (versie 5,7 of hoger) is geïnstalleerd. Het certificaat moet worden geïnstalleerd in een certificaat archief onder LocalMachine op alle knoop punten van het cluster. De persoonlijke sleutel die overeenkomt met het certificaat moet beschikbaar, toegankelijk en in Windows-exporteerbaar zijn. De certificaat informatie wordt verstrekt in het manifest van de toepassing onder de- `ContainerHostPolicies` tag als in het volgende code fragment wordt weer gegeven:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Bij het starten van de toepassing exporteert de runtime elk certificaat en de bijbehorende persoonlijke sleutel naar een PFX-bestand, dat is beveiligd met een wille keurig gegenereerd wacht woord, voor Windows-clusters. De PFX-en wachtwoord bestanden zijn respectievelijk toegankelijk in de container met behulp van de volgende omgevings variabelen: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Voor Linux-clusters worden de certificaten (PEM) gekopieerd van het archief dat is opgegeven door X509StoreName naar de container. De bijbehorende omgevings variabelen voor Linux zijn:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Houd er rekening mee dat zowel het `PEM` als- `PrivateKey` bestand het certificaat en de niet-versleutelde persoonlijke sleutel bevatten.

Als u de certificaten al in het vereiste formulier hebt en u deze wilt openen in de container, kunt u een gegevens pakket in uw app-pakket maken en het volgende in het manifest van de toepassing opgeven:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

De container service of het proces is verantwoordelijk voor het importeren van de certificaat bestanden in de container. Als u het certificaat wilt importeren, kunt u `setupentrypoint.sh` scripts gebruiken of aangepaste code uitvoeren in het container proces. Hier volgt een voorbeeld code in C# voor het importeren van het PFX-bestand:

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
Dit PFX-certificaat kan worden gebruikt voor het verifiëren van de toepassing of service of het beveiligen van communicatie met andere services. Standaard worden de bestanden alleen ACLed op systeem. U kunt deze naar andere accounts toegangs beheer lijst als vereist door de service.

Lees de volgende artikelen als volgende stap:

* [Een Windows-container implementeren voor het Service Fabric op Windows Server 2016](service-fabric-get-started-containers.md)
* [Een docker-container implementeren voor Service Fabric op Linux](service-fabric-get-started-containers-linux.md)