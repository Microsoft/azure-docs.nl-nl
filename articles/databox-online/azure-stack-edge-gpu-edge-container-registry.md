---
title: Een Edge-container register inschakelen op Azure Stack Edge Pro GPU-apparaat
description: Hierin wordt beschreven hoe u een lokaal Edge-container register op Azure Stack Edge Pro GPU-apparaat inschakelt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 56b691b2755b5e248b16e338f8fd82864f5bf218
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560332"
---
# <a name="enable-edge-container-registry-on-your-azure-stack-edge-pro-gpu-device"></a>Edge container Registry op uw Azure Stack Edge Pro GPU-apparaat inschakelen

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u het Edge-container register kunt inschakelen en het kunt gebruiken vanuit het Kubernetes-cluster op uw Azure Stack Edge Pro-apparaat. In het voor beeld in het artikel wordt beschreven hoe u een installatie kopie kunt pushen vanuit een bron register, in dit geval micro soft container Registry, naar het REGI ster op het Azure Stack edge-apparaat, het Edge-container register.

### <a name="about-edge-container-registry"></a>Over Edge container Registry

Gecontainerde Compute-toepassingen worden uitgevoerd op container installatie kopieën en deze installatie kopieën worden opgeslagen in registers. Registers kunnen openbaar zijn, zoals een docker hub, een persoonlijke of een Cloud provider die wordt beheerd zoals Azure Container Registry. Zie voor meer informatie [over registers, opslag plaatsen en installatie kopieën](../container-registry/container-registry-concepts.md).

Een Edge-container register biedt een opslag plaats aan de rand van uw Azure Stack Edge Pro-apparaat. U kunt dit REGI ster gebruiken om uw persoonlijke container installatie kopieën op te slaan en te beheren.

In een omgeving met meerdere knoop punten kunnen container installatie kopieën eenmaal worden gedownload en naar het Edge-container register worden gepusht. Alle Edge-toepassingen kunnen het Edge-container register voor volgende implementaties gebruiken.


## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt toegang tot een Azure Stack Edge Pro-apparaat.

1. U hebt het Azure Stack Edge Pro-apparaat geactiveerd zoals beschreven in [Azure Stack Edge Pro activeren](azure-stack-edge-gpu-deploy-activate.md).

1. U hebt reken functie ingeschakeld op het apparaat. Er is ook een Kubernetes-cluster op het apparaat gemaakt toen u Compute op het apparaat hebt geconfigureerd volgens de instructies in [Configure Compute op uw Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-deploy-configure-compute.md).

1. U hebt het Kubernetes API-eind punt van de pagina **apparaat** van uw lokale webgebruikersinterface. Zie de instructies in [Get KUBERNETES API endpoint](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)(Engelstalig) voor meer informatie.

1. U hebt toegang tot een client systeem met een [ondersteund besturings systeem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). Als u een Windows-client gebruikt, moet het systeem Power shell 5,0 of hoger uitvoeren om toegang te krijgen tot het apparaat.

    1. Als u uw eigen container installatie kopieën wilt verzamelen en pushen, moet u ervoor zorgen dat docker-client op het systeem is geïnstalleerd. Als u een Windows-client gebruikt, [installeert u docker desktop in Windows](https://docs.docker.com/docker-for-windows/install/).  


## <a name="enable-container-registry-as-add-on"></a>Container Registry inschakelen als invoeg toepassing

De eerste stap is het inschakelen van het Edge-container register als een invoeg toepassing.

1. [Maak verbinding met de Power shell-interface van het apparaat](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

1. Als u het container register wilt inschakelen als een invoeg toepassing, typt u: 

    `Set-HcsKubernetesContainerRegistry`
    
    Deze bewerking kan enkele minuten duren.

    Hier volgt een voor beeld van de uitvoer van deze opdracht:  
            
    ```powershell
    [10.128.44.40]: PS>Set-HcsKubernetesContainerRegistry
    Operation completed successfully. Use Get-HcsKubernetesContainerRegistryInfo for credentials    
    ```
            
1. Als u de container register details wilt ophalen, typt u:

    `Get-HcsKubernetesContainerRegistryInfo`

    Hier volgt een voor beeld van deze opdracht:  
    
    ```powershell
    [10.128.44.40]: PS> Get-HcsKubernetesContainerRegistryInfo
                
    Endpoint                                   IPAddress    Username     Password
    --------                                   ---------    --------     --------
    ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 10.128.44.41 ase-ecr-user i3eTsU4zGYyIgxV
    ``` 

1. Noteer de gebruikers naam en het wacht woord uit de uitvoer van `Get-HcsKubernetesContainerRegistryInfo` . Deze referenties worden gebruikt om u aan te melden bij het Edge-container register tijdens het pushen van installatie kopieën.         


## <a name="manage-container-registry-images"></a>Container register installatie kopieën beheren

U kunt toegang krijgen tot het container register van buiten uw Azure Stack edge-apparaat. Het is ook mogelijk dat u installatie kopieën wilt pushen of pullen in het REGI ster.

Volg deze stappen om toegang te krijgen tot het Edge-container register:

1. De eindpunt Details ophalen voor het Edge-container register.
    1. Ga in de lokale gebruikers interface van het apparaat naar het **apparaat**.
    1. Zoek het **eind punt van het Edge-container register**.
        ![Rand container register eindpunt op de pagina apparaat](media/azure-stack-edge-gpu-edge-container-registry/get-edge-container-registry-endpoint-1.png) 
    1. Kopieer dit eind punt en maak een bijbehorende DNS-vermelding in het `C:\Windows\System32\Drivers\etc\hosts` bestand van uw client om verbinding te maken met het eind punt van het Edge-container register. 

        <IP address of the Kubernetes main node>    <Edge container registry endpoint> 
        
        ![DNS-vermelding voor het Edge-container register eindpunt toevoegen](media/azure-stack-edge-gpu-edge-container-registry/add-domain-name-service-entry-hosts-1.png)    

1. Down load het Edge-container register certificaat van de lokale gebruikers interface. 
    1. Ga in de lokale gebruikers interface van het apparaat naar **certificaten**.
    1. Zoek de vermelding voor het **REGI ster van het Edge-container certificaat**. Rechts van deze vermelding selecteert u de **down load** om het rand container register certificaat te downloaden op uw client systeem dat u gaat gebruiken voor toegang tot uw apparaat. 

        ![Edge-eindpunt certificaat van container register downloaden](media/azure-stack-edge-gpu-edge-container-registry/download-edge-container-registry-endpoint-certificate-1.png)  

1. Installeer het gedownloade certificaat op de client. Als u een Windows-client gebruikt, voert u de volgende stappen uit: 
    1. Selecteer het certificaat en selecteer in de **wizard Certificaat importeren** de optie archief locatie als **lokale machine**. 

        ![Certificaat installeren 1](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. Installeer het certificaat op uw lokale computer in het vertrouwde basis archief. 

        ![Certificaat installeren 2](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 

1. Nadat het certificaat is geïnstalleerd, start u de docker-client op uw systeem opnieuw op.

1. Meld u aan bij het Edge-container register. Type:

    `docker login <Edge container registry endpoint> -u <username> -p <password>`

    Geef het eind punt van het Edge-container register op via de pagina **apparaten** en de gebruikers naam en het wacht woord dat u hebt verkregen uit de uitvoer van `Get-HcsKubernetesContainerRegistryInfo` . 

1. Gebruik docker push of pull-opdrachten om container installatie kopieën te pushen of uit te halen uit het container register.
 
    1. Haal een installatie kopie op uit de micro soft Container Registry-installatie kopie. Type:
        
        `docker pull <Full path to the container image in the Microsoft Container Registry>`
       
    1. Maak een alias van de installatie kopie die u met het volledig gekwalificeerde pad naar uw REGI ster hebt opgehaald.

        `docker tag <Path to the image in the Microsoft container registry> <Path to the image in the Edge container registry/Image name with tag>`

    1. Push de installatie kopie naar het REGI ster.
    
        `docker push <Path to the image in the Edge container registry/Image name with tag>`

    1. Voer de installatie kopie uit die u in het REGI ster hebt gepusht.
    
        `docker run -it --rm -p 8080:80 <Path to the image in the Edge container registry/Image name with tag>`

        Hier volgt een voor beeld van de uitvoer van de opdrachten pull en push:

        ```powershell
        PS C:\WINDOWS\system32> docker login ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 -u ase-ecr-user -p 3bbo2sOtDe8FouD
        WARNING! Using --password via the CLI is insecure. Use --password-stdin.
        Login Succeeded
        PS C:\WINDOWS\system32> docker pull mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        1.17.5-alpine: Pulling from oss/nginx/nginx
        Digest: sha256:5466bbc0a989bd1cd283c0ba86d9c2fc133491ccfaea63160089f47b32ae973b
        Status: Image is up to date for mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        PS C:\WINDOWS\system32> docker tag mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        PS C:\WINDOWS\system32> docker push ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        The push refers to repository [ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx]
        bba7d2385bc1: Pushed
        77cae8ab23bf: Pushed
        2.0: digest: sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff size: 739
        PS C:\WINDOWS\system32> docker run -it --rm -p 8080:80 ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        2020/11/10 00:00:49 [error] 6#6: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:8080", referrer: "http://localhost:8080/"
        172.17.0.1 - - [10/Nov/2020:00:00:49 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8080/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36" "-"
        ^C
        PS C:\WINDOWS\system32>    
        ```
    
1. Blader naar `http://localhost:8080` om de actieve container weer te geven. In dit geval ziet u de nginx-webserver waarop wordt uitgevoerd.

    ![De container die wordt uitgevoerd weer geven](media/azure-stack-edge-gpu-edge-container-registry/view-running-container-1.png)

    Als u de container wilt stoppen en verwijderen, drukt u op `Control+C` .

 

## <a name="use-edge-container-registry-images-via-kubernetes-pods"></a>Rand container register installatie kopieën gebruiken via Kubernetese peul

U kunt nu de installatie kopie implementeren die u in het container register van de Edge hebt gepusht vanuit de Kubernetes.

1. Als u de installatie kopie wilt implementeren, moet u cluster toegang configureren via *kubectl*. Maak een naam ruimte, een gebruiker, verleen gebruikers toegang tot de naam ruimte en haal een *configuratie* bestand op. Zorg ervoor dat u verbinding kunt maken met het Kubernetes. 
    
    Volg alle stappen in [verbinding maken met en beheer een Kubernetes-cluster via kubectl op uw Azure stack Edge Pro GPU-apparaat](azure-stack-edge-gpu-create-kubernetes-cluster.md). 

    Hier volgt een voor beeld van de uitvoer van een naam ruimte op uw apparaat van waar de gebruiker toegang tot het Kubernetes-cluster kan krijgen.

    ```powershell
    [10.128.44.40]: PS>New-HcsKubernetesNamespace -Namespace myecr
    [10.128.44.40]: PS>New-HcsKubernetesUser -UserName ecruser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01URXdOVEF6TkRJek1Gb1hEVE13TVRFd016QXpOREl6TUZvd0ZURVRNQkVnNjOVRLWndCQ042cm1XQms2eXFwcXI1MUx6bApTaXMyTy91UEJ2YXNSSUUzdzgrbmEwdG1aTERZZ2F6MkQwMm42Q29mUmtyUTR2d1lLTnR1MlpzR3pUdz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.dbe-hw6h1t2.microsoftdatabox.com:6443
        name: kubernetes
        ===================CUT=========================================CUT==============
        client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJYmVWRGJSTzZ3ell3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURFeE1EVXdNelF5TXpCYUZ3MHlNVEV4TURreU16UTRNal
        ===================CUT=========================================CUT==============
        DMVUvN3lFOG5UU3k3b2VPWitUeHdzCjF1UDByMjhDZ1lCdHdRY0ZpcFh1blN5ak16dTNIYjhveFI2V3VWWmZldFFKNElKWEFXOStVWGhKTFhyQ2x4bUcKWHRtbCt4UU5UTzFjQVNKRVZWVDd6Tjg2ay9kSU43S3JIVkdUdUxlUDd4eGVjV2VRcWJrZEVScUsxN0liTXpiVApmbnNxc0dobEdmLzdmM21kTGtyOENrcWs5TU5aM3MvUVIwRlFCdk94ZVpuUlpTeDVGbUR5S1E9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=

    [10.128.44.40]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myecr -UserName ecruser
    [10.128.44.40]: PS>kubectl get pods -n "myecr"
    No resources found.
    PS C:\WINDOWS\system32>
    ```  

2. De pull-geheimen van de installatie kopie zijn al ingesteld in alle Kubernetes-naam ruimten op het apparaat. U kunt geheimen ophalen met behulp van de `get secrets` opdracht. Hier volgt een voorbeeld van uitvoer:

    ```powershell
    PS C:\WINDOWS\system32> .\kubectl.exe get secrets -n myecr
    NAME                  TYPE                                  DATA   AGE
    ase-ecr-credentials   kubernetes.io/dockerconfigjson        1      99m
    default-token-c7kww   kubernetes.io/service-account-token   3      107m
    sec-smbcredentials    microsoft.com/smb                     2      99m
    PS C:\WINDOWS\system32>   
    ```    

3. Implementeer een pod in uw naam ruimte met behulp van kubectl. Gebruik het volgende `yaml` . 

    Vervang de afbeelding: `<image-name>` door de afbeelding die naar het container register is gepusht. Raadpleeg de geheimen in uw naam ruimten met behulp van imagePullSecrets met een naam: `ase-ecr-credentials` .
    
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        imagePullPolicy: Always
      imagePullSecrets:
      - name: ase-ecr-credentials
    ```

4. Pas de implementatie toe in de naam ruimte die u hebt gemaakt met behulp van de opdracht apply. Controleer of de container wordt uitgevoerd. Hier volgt een voorbeeld van uitvoer:
   
    ```yml
    PS C:\Windows\System32> .\kubectl.exe apply -f .\deployment.yml -n myecr
    pod/nginx configured
    PS C:\Windows\System32> .\kubectl.exe get pods -n myecr
    NAME    READY   STATUS    RESTARTS   AGE
    nginx   1/1     Running   0          27m
    PS C:\Windows\System32>
    ```

## <a name="delete-container-registry-images"></a>Container register installatie kopieën verwijderen

Edge Container Registry opslag wordt gehost op een lokale share binnen uw Azure Stack Edge Pro-apparaat die wordt beperkt door de beschik bare opslag op het apparaat. Het is uw verantwoordelijkheid om ongebruikte docker-installatie kopieën uit het container register te verwijderen met behulp van docker HTTP v2 API ( https://docs.docker.com/registry/spec/api/) .  

Als u een of meer container installatie kopieën wilt verwijderen, voert u de volgende stappen uit:

1. Stel de naam van de installatie kopie in op de afbeelding die u wilt verwijderen.

    ```powershell
    PS C:\WINDOWS\system32> $imageName="nginx"    
    ```

1. Stel de gebruikers naam en het wacht woord van het container register in als een PS-referentie

    ```powershell
    PS C:\WINDOWS\system32> $username="ase-ecr-user"
    PS C:\WINDOWS\system32> $password="3bbo2sOtDe8FouD"
    PS C:\WINDOWS\system32> $securePassword = ConvertTo-SecureString $password -AsPlainText -Force
    PS C:\WINDOWS\system32> $credential = New-Object System.Management.Automation.PSCredential ($username, $securePassword)    
    ```

1. De tags weer geven die zijn gekoppeld aan de installatie kopie

    ```powershell
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/nginx/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32>    
    ```
  
1. Vermeld de samen vatting die is gekoppeld aan het label dat u wilt verwijderen. Dit maakt gebruik van $tags van de bovenstaande opdracht. Als u meerdere labels hebt, selecteert u een van de tags en gebruikt u de volgende opdracht.

    ```powershell
    PS C:\WINDOWS\system32> $response = Invoke-WebRequest -Method Head -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$tags" -Headers @{ 'Accept' = 'application/vnd.docker.distribution.manifest.v2+json' }
    PS C:\WINDOWS\system32> $digest = $response.Headers['Docker-Content-Digest']
    PS C:\WINDOWS\system32> $digest
    sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff
    PS C:\WINDOWS\system32>    
    ```
1. Verwijder de installatie kopie met de samen vatting van de afbeelding: tag

    ```powershell
    PS C:\WINDOWS\system32> Invoke-WebRequest -Method Delete -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$digest" | Select-Object -ExpandProperty StatusDescription    
    ```

Nadat u de ongebruikte installatie kopieën hebt verwijderd, wordt de ruimte die is gekoppeld aan de niet-gerefereerde installatie kopieën automatisch vrijgemaakt door een proces dat in de nacht wordt uitgevoerd. 

## <a name="next-steps"></a>Volgende stappen

- [Implementeer een staatloze toepassing op uw Azure stack Edge Pro](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).