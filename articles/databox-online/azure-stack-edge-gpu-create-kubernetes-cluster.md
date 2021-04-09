---
title: Een Kubernetes-cluster maken en beheren op Azure Stack Edge Pro GPU-apparaat | Microsoft Docs
description: Hierin wordt beschreven hoe u een Kubernetes-cluster maakt en beheert op Azure Stack Edge Pro GPU-apparaat via de Windows Power shell-interface.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: ddbf3c99845f4c38f511260f4dec274903a9d586
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631950"
---
# <a name="connect-to-and-manage-a-kubernetes-cluster-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Verbinding maken met en beheren van een Kubernetes-cluster via kubectl op uw Azure Stack Edge Pro GPU-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Op uw Azure Stack Edge Pro-apparaat wordt een Kubernetes-cluster gemaakt wanneer u een compute-functie configureert. Nadat het Kubernetes-cluster is gemaakt, kunt u via een systeem eigen hulp programma, zoals *kubectl*, verbinding maken met het cluster en het lokaal beheren vanaf een client computer.

In dit artikel wordt beschreven hoe u verbinding maakt met een Kubernetes-cluster op uw Azure Stack Edge Pro-apparaat en het vervolgens beheert met behulp van *kubectl*. 


## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt toegang tot een Azure Stack Edge Pro-apparaat.

2. U hebt het Azure Stack Edge Pro-apparaat geactiveerd zoals beschreven in [Azure Stack Edge Pro activeren](azure-stack-edge-gpu-deploy-activate.md).

3. U hebt reken functie ingeschakeld op het apparaat. Er is ook een Kubernetes-cluster op het apparaat gemaakt toen u Compute op het apparaat hebt geconfigureerd volgens de instructies in [Configure Compute op uw Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-deploy-configure-compute.md).

4. U hebt toegang tot een Windows-client systeem met Power shell 5,0 of hoger voor toegang tot het apparaat. U kunt ook een andere client met een [ondersteund besturings systeem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) hebben. 

5. U hebt het Kubernetes API-eind punt van de pagina **apparaat** van uw lokale webgebruikersinterface. Zie de instructies in [KUBERNETES API-eind punt ophalen](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints) voor meer informatie


## <a name="connect-to-powershell-interface"></a>Verbinding maken met de Power shell-interface

Nadat het Kubernetes-cluster is gemaakt, kunt u toegang krijgen tot dit cluster om naam ruimten en gebruikers te maken en gebruikers toe te wijzen aan naam ruimten. Hiervoor moet u verbinding maken met de Power shell-interface van het apparaat. Volg deze stappen op de Windows-client met Power shell.

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]


## <a name="configure-cluster-access-via-kubernetes-rbac"></a>Cluster toegang configureren via Kubernetes RBAC

Nadat het Kubernetes-cluster is gemaakt, kunt u het *kubectl* via de opdracht regel gebruiken om toegang te krijgen tot het cluster. 

In deze benadering maakt u een naam ruimte en een gebruiker. Vervolgens koppelt u de gebruiker aan de naam ruimte. U moet ook een *configuratie* bestand ophalen waarmee u een Kubernetes-client kunt gebruiken om rechtstreeks te communiceren met het Kubernetes-cluster dat u hebt gemaakt zonder verbinding te maken met de Power shell-interface van uw Azure stack Edge Pro-apparaat.

1. Een naamruimte maken. Type:

    `New-HcsKubernetesNamespace -Namespace <string>` 

    > [!NOTE]
    > Voor zowel naam ruimte-als gebruikers namen gelden de [naamgevings regels voor DNS-subdomeinen](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) .

    Hier volgt een voorbeeld van uitvoer:

    `[10.100.10.10]: PS> New-HcsKubernetesNamespace -Namespace "myasetest1"`

2. Maak een gebruiker en haal een configuratie bestand op. Type:

    `New-HcsKubernetesUser -UserName <string>`

    > [!NOTE]
    > U kunt *aseuser* niet gebruiken als de gebruikers naam omdat deze is gereserveerd voor een standaard gebruiker die is gekoppeld aan IOT-naam ruimte voor Azure stack Edge Pro.

    Hier volgt een voor beeld van de uitvoer van het configuratie bestand:
   
    ```powershell
    [10.100.10.10]: PS> New-HcsKubernetesUser -UserName "aseuser1"
        apiVersion: v1
        clusters:
        - cluster:
            certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01ERXlPVEUyTlRFeE4xb1hEVE13TURFeU5qRTJOVEV4TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTXNpCkdZUHB0U1VWcDhKZEdVcHE1MVBURWhsZm8wWkk3YXFBdUlrOHZWdUFCZHJQK1FBSzFxcEN1di93NjIwbUtpZ0QKak1aT3Q4QkREREppWHF6UDZRZm5Oc0U2VXBHMnh0YnYrcTZHV2R5K0t6WkxMbXlwWGY3VjlzZEJnejVKVDNvYQpIdzFja2NTUklHSlV3UWxTbklNaHJUS3JUNDZFUUp3d282TmlNUzZMZDZieVk3WkUrTGg3OS9aNEhLanhTRmhMClc5ZG8veThZR3FXUDZmZTFmMmVmSkhUeGtwR05HZE1UVjNuOFlCZ0pSRzdrNjh0N2MrZ1NhbUlVWVJpTUNSNFAKYlFxcFpscWYvV2REZEJHOFh6aDJ0M1l4SkVIMm00T0Z1cSsvUitMYm95aHdKbmNMdVJ5OEpNZWlwTEQ3UlN0QwpZTDNNR0EzN2JieTRyYm4zVzg4Q0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNbzFwWlBtQzV1cmRPZUJhSWQ4eEQzRkxCMG8KTlErbXBXMWpDd0ZtY3h6dUtlWmRsNXc2N0tuS2JTcDR0TXo1cXg3bUtSc0UxcnBoWkh2VHlKUXg1ZFk2ZE1Kdgp5d2FQZjBpT05TNlU2cC9INE12U1dJaEtJZ1FuTnE1dDh4TjJCNnZpQW41RmZoRkx6WEQrUlZGSm42cnovWkZnCmV6MHpxTkNKYmcvelFucFROcmQ2cnFFRHpoSVFZOVdYVWQycFh3ZXRqUXJpMkpZamh4NmtEcTVoRkZTM0FLUnIKOWlQTVQxaWNkR1NUMFVvM1hIZ1k2ck45WGp3MHFrY2I0Sy83UlVVWlRvS3dKamROR3lNTnpad000L2puR0p5SwpQTE9ycU5Ddlkvb0lkVEM5eVZVY3VRbXVlR0VqT20xUnN1RDFHYVE0RTZwakppVWJpMVdrajJ1bFhOWT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
            server: https://10.128.47.90:6443
            name: kubernetes
        contexts:
        - context:
            cluster: kubernetes
            user: aseuser1
            name: aseuser1@kubernetes
        current-context: aseuser1@kubernetes
        kind: Config
        preferences: {}
        users:
        - name: aseuser1
            user:
            client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwVENDQWJtZ0F3SUJBZ0lJWlFXcjY2cGFWSm93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBeE1qa3hOalV4TVRkYUZ3MHlNVEF4TWpneU1qVTJNVGRhTUJNeApFVEFQQmdOVkJBTVRDR0Z6WlhWelpYSXhNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDCkFRRUF4R3RDaXJ2cVhGYm5wVmtaYlVPRWQ3cXg2UzNVZ092MlhHRHNKM2VYWXN0bUxQVjMrQnlBcWwyN211L1AKaWdmaWt6MG9QSW1iYmtvcVJkamlYckxFZnk0N3dHcEhzdUhUOHNLY2tHTnJleFE2bXhaZ29xaU1nL2FuMUpMdwpiOFEvVnlQaWdVdUt6eVBseEhUZmlmSVM5MzR1VnZVZUc0dzlMRjAyZ2s2Nitpc0ZtanhsVmhseWRMNlc2UmZTCjl0OGpNMEFkdEpJL0xNbE13RHJJRVdFKzM4WDVNelJhQkJYNnlzNDFWSkZxekcwdW14dHdxN2pGOXp1UTE4ekIKalRZaDl3OWVKcDJwS2Fvak5tNE9SSDh4SzVSaUhocjJ2anFJWXkxRDd2WDh0b0U1K05HNmxHZjh5L1NvQnNRbQpmOG9vL1k3SEZmQXVGdlN6WUc1RUlQTFM4UUlEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEClZSMGxCQXd3Q2dZSUt3WUJCUVVIQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNR1BxY0YzS1BCbHZ0K24KN1NOcGE3anhWYkhZVGxyNTgwVWxzek93WEwwVnVPUUlCYmN2djIzZk9HNkhDZlQ0bWxBU0JRWVNZcmpLMjJTTwpTWld4cjNQUDlhVzNHajkxc0ttSnc1ZUF1WFhQbUJpK1RWQzBvY0ZLaEQvZ0o1aC93YnBaVndpVjVyRWE5Kzc2CnhNcFAzRld6dG5tT1hPaEl6UFNlR3B4YWpwQXd3ZXd4QU0yb0xGRFZFcy9XTFFMODJZM3NFcE93NVNaSVJJNXMKUHhMUTVnV1ZPM2x2SXcwZ3IrdkJlanZSOUZKaWVuTWFRdGdjSVgyRmpDaDBRMHVYRkdsTVNXWEljbjRLRTR0TApQSFFMalRSVUwyVnRXcW1YZ1RBM3RzN01DcGNRTFdPZFJUYkpSejZCbkc1aXVwcDdOSlFvYW9YcWpNVk5DVDZCCllYMEd0Skk9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
            client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBeEd0Q2lydnFYRmJucFZrWmJVT0VkN3F4NlMzVWdPdjJYR0RzSjNlWFlzdG1MUFYzCitCeUFxbDI3bXUvUGlnZmlrejBvUEltYmJrb3FSZGppWHJMRWZ5NDd3R3BIc3VIVDhzS2NrR05yZXhRNm14WmcKb3FpTWcvYW4xSkx3YjhRL1Z5UGlnVXVLenlQbHhIVGZpZklTOTM0dVZ2VWVHNHc5TEYwMmdrNjYraXNGbWp4bApWaGx5ZEw2VzZSZlM5dDhqTTBBZHRKSS9MTWxNd0RySUVXRSszOFg1TXpSYUJCWDZ5czQxVkpGcXpHMHVteHR3CnE3akY5enVRMTh6QmpUWWg5dzllSnAycEthb2pObTRPUkg4eEs1UmlIaHIydmpxSVl5MUQ3dlg4dG9FNStORzYKbEdmOHkvU29Cc1FtZjhvby9ZN0hGZkF1RnZTellHNUVJUExTOFFJREFRQUJBb0lCQUVVSUVXM2kxMTQycU5raQo5RjNEWWZZV1pscTJZYjRoc0FjTmhWSGxwUTN5d0dsQ3FEUktDQ3BZSVF3MkJqSFR6WnpEM0xWU0E0K0NmMUxuCkE4QVdnaHJVcStsWE1QVzhpcG9DTGJaTlNzUUord0x3bld2dFl0MHFQaGZtd0p2M1UrK1RUQkwyOHNVVUw3ZVkKLzh0aWlhbno3ZU5mNklIMENyZmgxcnQ3WWhsemtRd1hBVHNScVJja0dMaTgrdGN5WnVzdGFhbENUSzBGRTdCaQpBUGE5a2w1SG56eCs4TTcvNWladHkwTUIxYWpWMnlGblBkUmlKSFVCb1AxVVV0QUthYjVZU0RvNllkZ2pIUTRHCjNWN1l1YWZobnVFMXA0VVIvUkloVVdjRlVVaTFBOFpZMFdnd1BDTmhnMWpQZU5vb2Y1UHpRbEY1OTRBREVwUUYKOFR2bG92RUNnWUVBOWZZbUxyY0tlQ0JiMTFoQVhoTi91Z1RTbU5xNnpFL1pPSWl6M0xwckdjRDhvWDdCVW9GcgplelkxbktSS2tkczE5OERnVjlQZUhuNzllQTRoMjM5RkIwNFFhMUJBdUVMRzRsdHJ3VlNxaFBENUR6YkcrSEhSCnJtYThVMEpUSmVVS0tJVjRUUGxlTzFtK2tjbkRJVXY1ckpwZDVXU3RvcUhXdk9RZkEvRUF0VlVDZ1lFQXpHOTcKTitCZVVvbFNiREttVUNGdTdPZGhYSXJYR3RnSEorZ2JOMDlnSHRURG5PY0IxZ1NzNkpZa1FPQU9qbWFxK05lRAp5SUF1NytheWlFRmpyT2tzTGhkSTREUXNkWFZveFFGVko1V1JwWlk3UTVRaFZpYUR2enR4NDlzSDlKSkplM2U0Cnl3NWdpNGkxKy90MnY2eWRKcWdNQ0xxOHlEdFRrcE9PSitkbkp5MENnWUJwZ3lpcURaZU9KTU9CUTdpSkl2QSsKQ21lVmJ1K0hTaEd6TU9HSHBPamc2V3IybEh1Mk94S3lqblM5TjdWTmtLNDhGQitwVFpnUm1RUi9CZ0Q4T2tLUQplYXFOZnFYazViQ1AxZ3dKcVpwazRVTFdoZmNoQ1NLY0lESlZ2VFFTSTRrU0RQK29kYWs0Nkt6WnVhWGRtTXdJCmdVZ2FhZkFhdmpaeVhhSDRmT0NDNlFLQmdHVXJCaDh3dVh5KzJEc1RGWnF4OE9McjNoS2Q0clUyRXRSODJIc1cKbk1xbEgraVZxU0x3VFdFTWJBUnUzTVU3cVlCYnBxdWlRNWdVNG1UcmR4Z3FpK0tEUTEwd2RJL3IrbDBEdTlCTApCRGlkajlaeGg4M0tZWWhSTXBzLzJULys1TDVsRU4zcnozczl2RkZtcisxS3pycENqeklDdDBtZmtrd0hHV0pGCjhaWkJBb0dBVXB3aUIrcWlHbkpxU1FtZHNSZFVabGFBaTRpbGhaa01RYTRHem95ZFQ3OTVHTm44ZThBRjd3WHMKTGpyYjdEV1FwakdCMnZpUlkySUZBVmIyKzZsdDlwOVJRMTZnSmxpNU5ZRXVvQWRoWXBsVWdBZGFHWHNGNHdabwo3SHFHTHBGdmUxVU5Gb0dQdkxpWUNrUFVYdGduQ3dNb0R2SEpKNzVYMXl6ckh6cmxUS1k9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==  
        
    [10.100.10.10]: PS>
    ```
    

3. Een configuratie bestand wordt weer gegeven als tekst zonder opmaak. Kopieer dit bestand en sla het op als een *configuratie* bestand. 

    > [!IMPORTANT]
    > Sla het configuratie bestand niet op als *. txt* -bestand, sla het bestand zonder bestands extensie op.

4. Het configuratie bestand moet zich in de `.kube` map van uw gebruikers profiel op de lokale computer bevinden. Kopieer het bestand naar de map in uw gebruikers profiel.

    ![Locatie van het configuratie bestand op de client](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)

5. Koppel de naam ruimte aan de gebruiker die u hebt gemaakt. Type:

    `Grant-HcsKubernetesNamespaceAccess -Namespace <string> -UserName <string>`

    Hier volgt een voorbeeld van uitvoer:

    `[10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace "myasetest1" -UserName "aseuser1"`

    Zodra u het configuratie bestand hebt, hebt u geen fysieke toegang tot het cluster nodig. Als uw client het IP-adres van het Azure Stack Edge Pro-apparaat kan pingen, moet u het cluster met behulp van *kubectl* -opdrachten kunnen door sturen.

6. Start een nieuwe Power shell-sessie op de client. U hoeft geen verbinding te maken met de interface van het apparaat. U kunt nu `kubectl` op uw client installeren met de volgende opdracht:

    ```powershell
    PS C:\windows\system32> curl https://storage.googleapis.com/kubernetes-release/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    
    PS C:\windows\system32>
    ```
    Als bijvoorbeeld het hoofd knooppunt Kubernetes werd uitgevoerd op v 1.15.2, installeert u v 1.15.2 op de client.

    > [!IMPORTANT]
    > Download een client met een versie die maximaal één secundaire versie is verwijderd van de hoofdversie. De client versie, maar kan het hoofd naar een secundaire versie leiden. Een v 1.3-Master zou bijvoorbeeld moeten werken met de knoop punten v 1.1, v 1.2 en v 1.3, en moet werken met clients met de v 1.2, v 1.3 en v 1.4. Zie [ondersteunings beleid voor Kubernetes-versie en-versie](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)voor meer informatie over de Kubernetes-client versie. Ga naar Kubernetes-Server versie ophalen voor meer informatie over de Kubernetes-Server versie op Azure Stack Edge Pro.<!-- insert link-->
    > Soms `kubectl` wordt vooraf geïnstalleerd op uw systeem als u docker voor Windows of andere hulpprogram ma's uitvoert. Het is belang rijk dat u de specifieke versie van `kubectl` zoals beschreven in deze sectie gebruikt om met dit kubernetes-cluster te werken. 

    De installatie duurt enkele minuten.

7. Controleer of de geïnstalleerde versie is die u hebt gedownload. Geef het absolute pad op naar de locatie waar de `kubectl.exe` is geïnstalleerd op uw systeem.
    
    ```powershell
    PS C:\Users\myuser> C:\windows\system32\kubectl.exe version
    Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
    Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
    PS C:\Users\myuser>
    ``` 
    
    Ga voor meer informatie over `kubectl` de opdrachten die worden gebruikt voor het beheren van het Kubernetes-cluster naar [overzicht van kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

8. Voeg een DNS-vermelding toe aan het hosts-bestand op uw systeem. 

    1. Voer Klad blok als Administrator uit en open het `hosts` bestand dat zich bevindt in `C:\windows\system32\drivers\etc\hosts` . 
    2. Gebruik de informatie die u hebt opgeslagen op de pagina **apparaat** in de lokale gebruikers interface in de vorige stap om de vermelding in het hosts-bestand te maken. 

        Kopieer bijvoorbeeld dit eind punt `https://compute.asedevice.microsoftdatabox.com/[10.100.10.10]` om de volgende vermelding te maken met het IP-adres van het apparaat en het DNS-domein: 

        `10.100.10.10 compute.asedevice.microsoftdatabox.com`

9. Als u wilt controleren of u verbinding kunt maken met de Kubernetes, typt u:
    
    ```powershell
    PS C:\Users\myuser> kubectl get pods -n "myasetest1"
    No resources found.
    PS C:\Users\myuser>
    ```
U kunt nu uw toepassingen implementeren in de naam ruimte en vervolgens de toepassingen en de bijbehorende logboeken weer geven.

> [!IMPORTANT]   
> Er zijn veel opdrachten die u niet kunt uitvoeren, bijvoorbeeld de opdrachten waarvoor u beheerders toegang nodig hebt. U kunt alleen bewerkingen uitvoeren die zijn toegestaan in de naamruimte.


## <a name="remove-kubernetes-cluster"></a>Kubernetes-cluster verwijderen

Als u het Kubernetes-cluster wilt verwijderen, moet u de IoT Edge configuratie verwijderen.

Ga voor gedetailleerde instructies naar [beheer IOT Edge configuratie](azure-stack-edge-gpu-manage-compute.md#manage-iot-edge-configuration).
   

## <a name="next-steps"></a>Volgende stappen

- [Implementeer een staatloze toepassing op uw Azure stack Edge Pro](azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).
