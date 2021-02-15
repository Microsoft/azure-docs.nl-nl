---
title: Een SMB-volume maken voor Azure NetApp Files | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een SMB3-volume maakt in Azure NetApp Files. Meer informatie over de vereisten voor Active Directory verbindingen en Domain Services.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/01/2020
ms.author: b-juche
ms.openlocfilehash: 7c5f18d33efa1838fccab3935054ea19227bc06c
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102849"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Een SMB-volume maken voor Azure NetApp Files

Azure NetApp Files biedt ondersteuning voor het maken van volumes met behulp van NFS (NFSv3 en NFSv 4.1), SMB3 of het dubbele Protocol (NFSv3 en SMB). Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool. In dit artikel wordt beschreven hoe u een SMB3-volume maakt.

## <a name="before-you-begin"></a>Voordat u begint 
U dient al een capaciteitspool te hebben ingesteld.   
[Een capaciteits groep instellen](azure-netapp-files-set-up-capacity-pool.md)   
Er moet een subnet zijn gedelegeerd aan Azure NetApp Files.  
[Een subnet delegeren aan Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Vereisten voor Active Directory-verbindingen

 U moet Active Directory verbindingen maken voordat u een SMB-volume maakt. De vereisten voor Active Directory verbindingen zijn als volgt: 

* Het beheerders account dat u gebruikt, moet de mogelijkheid hebben om computer accounts te maken in het pad naar de organisatie-eenheid (OE) dat u opgeeft.  

* De juiste poorten moeten geopend zijn op de betreffende Windows Active Directory (AD)-server.  
    De vereiste poorten zijn als volgt: 

    |     Service           |     Poort     |     Protocol     |
    |-----------------------|--------------|------------------|
    |    AD-webservices    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N.v.t.       |    ECHO antwoord    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS-naam       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    W32Time            |    123       |    UDP           |

* De site topologie voor de doel-Active Directory Domain Services moet voldoen aan de richt lijnen, met name de Azure VNet waar Azure NetApp Files wordt geïmplementeerd.  

    De adres ruimte voor het virtuele netwerk waar Azure NetApp Files wordt geïmplementeerd, moet worden toegevoegd aan een nieuwe of bestaande Active Directory site (waarbij een domein controller bereikbaar is voor Azure NetApp Files). 

* De opgegeven DNS-servers moeten bereikbaar zijn vanaf het [overgedragen subnet](./azure-netapp-files-delegate-subnet.md) van Azure NetApp files.  

    Zie de [richt lijnen voor het Azure NetApp files-netwerk planning](./azure-netapp-files-network-topologies.md) voor ondersteunde netwerk topologieën.

    De netwerk beveiligings groepen (Nsg's) en firewalls moeten de juiste geconfigureerde regels hebben om Active Directory-en DNS-verkeers aanvragen toe te staan. 

* Het Azure NetApp Files overgedragen subnet moet alle Active Directory Domain Services (toevoegen) domein controllers in het domein, inclusief alle lokale en externe domein controllers, kunnen bereiken. Anders kan er sprake zijn van een onderbreking van de service.  

    Als u domein controllers hebt die niet bereikbaar zijn voor het Azure NetApp Files overgedragen subnet, kunt u een Active Directory site opgeven tijdens het maken van de Active Directory verbinding.  Azure NetApp Files moet alleen communiceren met domein controllers in de site waar de Azure NetApp Files adres ruimte van het subnet is overgedragen.

    Zie [de site topologie ontwerpen](/windows-server/identity/ad-ds/plan/designing-the-site-topology) over AD-sites en-services. 
    
* U kunt AES-versleuteling voor AD-verificatie inschakelen door het selectie vakje **AES-versleuteling** in het venster [lid worden van Active Directory](#create-an-active-directory-connection) in te scha kelen. Azure NetApp Files ondersteunt DES-, Kerberos AES 128-en Kerberos AES 256-versleutelings typen (van het minst veilig tot het veiligst). Als u AES-versleuteling inschakelt, moeten de gebruikers referenties die worden gebruikt om lid te worden van Active Directory de hoogste overeenkomende account optie ingeschakeld hebben die overeenkomt met de mogelijkheden die zijn ingeschakeld voor uw Active Directory.    

    Als uw Active Directory bijvoorbeeld alleen de functie AES-128 heeft, moet u de optie AES-128-account inschakelen voor de gebruikers referenties. Als uw Active Directory de functie AES-256 heeft, moet u de optie AES-256-account inschakelen (deze ondersteunt ook AES-128). Als uw Active Directory geen Kerberos-coderings functie heeft, gebruikt Azure NetApp Files standaard DES.  

    U kunt de account opties inschakelen in de eigenschappen van de Active Directory gebruikers en computers micro soft Management Console (MMC):   

    ![MMC-Active Directory gebruikers en computers](../media/azure-netapp-files/ad-users-computers-mmc.png)

* Azure NetApp Files ondersteunt [LDAP-ondertekening](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server), waarmee het LDAP-verkeer tussen de Azure NetApp files-service en de doel [Active Directory domein controllers](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)veilig kan worden verzonden. Als u de richt lijnen van micro soft Advisor [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) voor LDAP-ondertekening volgt, moet u de functie voor LDAP-ondertekening inschakelen in azure NetApp files door het selectie vakje **LDAP** in het venster [lid worden van Active Directory](#create-an-active-directory-connection) in te scha kelen. 

    Configuratie van [LDAP-kanaal binding](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) heeft alleen invloed op de Azure NetApp files-service. Als u echter zowel LDAP-kanaal binding als beveiligde LDAP gebruikt (bijvoorbeeld LDAPS of `start_tls` ), mislukt het maken van het SMB-volume.

Zie Azure NetApp Files [SMB Veelgestelde vragen](./azure-netapp-files-faqs.md#smb-faqs) over extra AD-informatie. 

## <a name="decide-which-domain-services-to-use"></a>Beslissen welke domein Services u wilt gebruiken 

Azure NetApp Files ondersteunt zowel [Active Directory Domain Services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (toevoegen) als Azure Active Directory Domain Services (AADDS) voor AD-verbindingen.  Voordat u een AD-verbinding maakt, moet u beslissen of u toevoegt of AADDS wilt gebruiken.  

Zie voor meer informatie [zelf beheerde Active Directory Domain Services, Azure Active Directory en beheerde Azure Active Directory Domain Services vergelijken](../active-directory-domain-services/compare-identity-solutions.md). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

U kunt uw favoriete [Active Directory-sites en-services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) -bereik gebruiken voor Azure NetApp files. Met deze optie schakelt u lees-en schrijf bewerkingen naar Active Directory Domain Services (toevoegen) domein controllers die [toegankelijk zijn via Azure NetApp files](azure-netapp-files-network-topologies.md). Ook wordt voor komen dat de service communiceert met domein controllers die zich niet in de opgegeven Active Directory sites en Services-site. 

Als u wilt zoeken naar de naam van uw site wanneer u toevoegt, kunt u contact opnemen met de beheer groep in uw organisatie die verantwoordelijk is voor Active Directory Domain Services. In het onderstaande voor beeld ziet u de Active Directory-invoeg toepassing sites en services waar de site naam wordt weer gegeven: 

![Sites en services van Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Wanneer u een AD-verbinding voor Azure NetApp Files configureert, geeft u de site naam in bereik op voor het veld **ad-site naam** .

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Zie [Azure AD Domain Services documentatie](../active-directory-domain-services/index.yml)voor Azure Active Directory Domain Services (AADDS) configuratie en richt lijnen.

Er zijn aanvullende AADDS-overwegingen van toepassing op Azure NetApp Files: 

* Zorg ervoor dat het VNet of subnet waar AADDS wordt geïmplementeerd, zich in dezelfde Azure-regio bevindt als de Azure NetApp Files-implementatie.
* Als u een ander VNet gebruikt in de regio waar Azure NetApp Files is geïmplementeerd, moet u een peering tussen de twee VNets maken.
* Azure NetApp Files ondersteunt `user` en `resource forest` typen.
* Voor het synchronisatie type kunt u `All` of selecteren `Scoped` .   
    Als u selecteert `Scoped` , moet u ervoor zorgen dat de juiste Azure AD-groep is geselecteerd voor toegang tot SMB-shares.  Als u niet zeker weet, kunt u het `All` synchronisatie type gebruiken.
* Het gebruik van de Enter prise-of Premium-SKU is vereist. De standaard-SKU wordt niet ondersteund.

Wanneer u een Active Directory verbinding maakt, moet u rekening houden met de volgende specifieke voor AADDS:

* In het menu AADDS vindt u informatie over de **primaire** DNS-, **secundaire DNS**-en **AD DNS-domein naam** .  
Voor DNS-servers worden er twee IP-adressen gebruikt voor het configureren van de Active Directory-verbinding. 
* Het **pad voor de organisatie-eenheid** is `OU=AADDC Computers` .  
Deze instelling wordt geconfigureerd in de **Active Directory verbindingen** onder **NetApp-account**:

  ![Pad naar de organisatie-eenheid](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **Gebruikers naam** referenties kunnen alle gebruikers zijn die lid zijn van de Azure AD-groep **Azure AD DC-Administrators**.


## <a name="create-an-active-directory-connection"></a>Een Active Directory verbinding maken

1. Klik vanuit uw NetApp-account op **Active Directory verbindingen** en klik vervolgens op **lid worden**.  

    ![Active Directory verbindingen](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Geef in het venster lid worden Active Directory de volgende informatie op op basis van de domein services die u wilt gebruiken:  

    Zie [beslissen welke domein Services u moet gebruiken](#decide-which-domain-services-to-use)voor informatie die specifiek is voor de domein services die u gebruikt. 

    * **Primaire DNS**  
        Dit is de DNS-server die is vereist voor de Active Directory domein deelname en SMB-verificatie bewerkingen. 
    * **Secundaire DNS**   
        Dit is de secundaire DNS-server voor het controleren van redundante naam Services. 
    * **AD DNS-domein naam**  
        Dit is de domein naam van de Active Directory Domain Services die u wilt toevoegen.
    * **AD-site naam**  
        Dit is de naam van de site die de detectie van de domein controller beperkt is. Dit moet overeenkomen met de naam van de site in Active Directory-sites en-services.
    * **Voor voegsel van SMB-server (computer-account)**  
        Dit is het naam voorvoegsel voor het machine account in Active Directory dat Azure NetApp Files gebruikt voor het maken van nieuwe accounts.

        Als de naamgevings standaard die uw organisatie gebruikt voor bestands servers NAS-01, NAS-02..., NAS-045 is, voert u "NAS" in voor het voor voegsel. 

        Met de service worden zo nodig extra machine accounts in Active Directory gemaakt.

        > [!IMPORTANT] 
        > Als u de naam van het SMB-server voorvoegsel wijzigt nadat u de Active Directory verbinding hebt gemaakt, verstoort het. U moet bestaande SMB-shares opnieuw koppelen na het wijzigen van de naam van het voor voegsel van de SMB-server.

    * **Pad naar de organisatie-eenheid**  
        Dit is het LDAP-pad voor de organisatie-eenheid (OE) waar de computer accounts van de SMB-server worden gemaakt. Dat wil zeggen OU = 2e niveau, OE = eerste niveau. 

        Als u Azure NetApp Files gebruikt met Azure Active Directory Domain Services, is het pad voor de organisatie-eenheid `OU=AADDC Computers` Wanneer u Active Directory configureert voor uw NetApp-account.

    ![Active Directory toevoegen](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **AES-versleuteling**   
        Schakel dit selectie vakje in om AES-versleuteling in te scha kelen voor een SMB-volume. Bekijk de [vereisten voor Active Directory verbindingen](#requirements-for-active-directory-connections) voor vereisten. 

        ![AES-versleuteling Active Directory](../media/azure-netapp-files/active-directory-aes-encryption.png)

        De **AES-versleutelings** functie is momenteel beschikbaar als preview-versie. Als dit de eerste keer is dat u deze functie gebruikt, moet u de functie registreren voordat u deze gebruikt: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        Controleer de status van de functie registratie: 

        > [!NOTE]
        > Het **RegistrationState** kan `Registering` tot 60 minuten duren voordat de status wordt gewijzigd in `Registered` . Wacht totdat de status is **geregistreerd** voordat u doorgaat.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        U kunt ook [Azure cli-opdrachten](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) gebruiken `az feature register` `az feature show` om de functie te registreren en de registratie status weer te geven. 

    * **LDAP-ondertekening**   
        Schakel dit selectie vakje in om LDAP-ondertekening in te scha kelen. Deze functionaliteit maakt beveiligde LDAP-zoek acties mogelijk tussen de Azure NetApp Files-service en de door de gebruiker opgegeven [Active Directory Domain Services domein controllers](/windows/win32/ad/active-directory-domain-services). Zie ADV190023 voor meer informatie. [| Micro soft-richt lijnen voor het inschakelen van LDAP-kanaal binding en LDAP-ondertekening](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).  

        ![LDAP-ondertekening Active Directory](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        De **LDAP-handtekening** functie is momenteel beschikbaar als preview-versie. Als dit de eerste keer is dat u deze functie gebruikt, moet u de functie registreren voordat u deze gebruikt: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        Controleer de status van de functie registratie: 

        > [!NOTE]
        > Het **RegistrationState** kan `Registering` tot 60 minuten duren voordat de status wordt gewijzigd in `Registered` . Wacht totdat de status is **geregistreerd** voordat u doorgaat.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        U kunt ook [Azure cli-opdrachten](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) gebruiken `az feature register` `az feature show` om de functie te registreren en de registratie status weer te geven. 

     * **Back-upbeleid gebruikers**  
        U kunt aanvullende accounts toevoegen waarvoor verhoogde bevoegdheden zijn vereist voor het computer account dat is gemaakt voor gebruik met Azure NetApp Files. Met de opgegeven accounts kunnen de NTFS-machtigingen op bestands- of mapniveau worden gewijzigd. U kunt bijvoorbeeld een niet-gemachtigd serviceaccount opgeven dat wordt gebruikt voor het migreren van gegevens naar een SMB-bestandsshare in Azure NetApp Files.  

        ![Active Directory gebruikers van het back-upbeleid](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        De functie **gebruikers van back-upbeleid** is momenteel beschikbaar als preview-versie. Als dit de eerste keer is dat u deze functie gebruikt, moet u de functie registreren voordat u deze gebruikt: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        Controleer de status van de functie registratie: 

        > [!NOTE]
        > Het **RegistrationState** kan `Registering` tot 60 minuten duren voordat de status wordt gewijzigd in `Registered` . Wacht totdat de status is **geregistreerd** voordat u doorgaat.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        U kunt ook [Azure cli-opdrachten](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) gebruiken `az feature register` `az feature show` om de functie te registreren en de registratie status weer te geven. 

    * Referenties, inclusief uw **gebruikers naam** en **wacht woord**

        ![Active Directory referenties](../media/azure-netapp-files/active-directory-credentials.png)

3. Klik op **Deelnemen**.  

    De Active Directory verbinding die u hebt gemaakt, wordt weer gegeven.

    ![Active Directory verbindingen gemaakt](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>Een SMB-volume toevoegen

1. Klik op de Blade **volumes** op de Blade capaciteits groepen. 

    ![Navigeren naar volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Klik op **+ Volume toevoegen** om een volume te maken.  
    Het venster een volume maken wordt weer gegeven.

3. Klik in het venster een volume maken op **maken** en geef informatie op voor de volgende velden op het tabblad basis beginselen:   
    * **Volume naam**      
        Geef de naam op voor het volume dat u wilt maken.   

        Een volume naam moet uniek zijn binnen elke capaciteits groep. De naam moet minstens drie tekens bevatten. U kunt alle alfanumerieke tekens gebruiken.   

        U kunt niet gebruiken `default` of `bin` als de naam van het volume.

    * **Capaciteits pool**  
        Geef de capaciteits pool op waar u het volume wilt maken.

    * **Quota**  
        Geef de hoeveelheid logische opslag op die u wilt toewijzen aan het volume.  

        Het veld **Beschikbare quotum** toont hoeveel ongebruikte ruimte er is in de gekozen capaciteitspool, die u kunt gebruiken om een nieuw volume te maken. De grootte van het nieuwe volume mag niet groter zijn dan het beschikbare quotum.  

    * **Door Voer (MiB/S)**   
        Als het volume is gemaakt in een hand matige QoS-capaciteits groep, geeft u de door Voer op voor het volume.   

        Als het volume wordt gemaakt in een automatische QoS-capaciteits groep, is de waarde die wordt weer gegeven in dit veld (quotum x-door Voer van service niveau).   

    * **Virtueel netwerk**  
        Geef het virtuele Azure-netwerk (VNet) op van waaruit u toegang wilt krijgen tot het volume.  

        Het VNet dat u opgeeft, moet een subnet hebben gedelegeerd aan Azure NetApp Files. De Azure NetApp Files-service kan alleen worden geopend vanuit hetzelfde VNet of vanuit een VNet dat zich in dezelfde regio bevindt als het volume via VNet-peering. U kunt het volume ook openen vanuit uw on-premises netwerk via een snelle route.   

    * **Subnet**  
        Geef het subnet op dat u wilt gebruiken voor het volume.  
        Het opgegeven subnet moet zijn gedelegeerd aan Azure NetApp Files. 
        
        Als u nog geen subnet hebt gedelegeerd, kunt u op de pagina een volume maken klikken op **Nieuw maken** . Geef vervolgens op de pagina Subnet maken de subnetgegevens op, en selecteer **Microsoft.NetApp/volumes** om het subnet te delegeren aan Azure NetApp Files. In elk VNet kan slechts één subnet worden gedelegeerd aan Azure NetApp Files.   
 
        ![ Een volume maken](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Subnet maken](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Als u een bestaand momentopname beleid wilt Toep assen op het volume, klikt u op de **sectie Geavanceerd weer geven** om deze uit te vouwen, geeft u op of u het pad naar de moment opname wilt verbergen en selecteert u een momentopname beleid in de vervolg keuzelijst. 

        Zie voor meer informatie over het maken van een momentopname beleid [beleid voor moment opnamen beheren](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Geavanceerde selectie weer geven](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Klik op **protocol** en voer de volgende informatie uit:  
    * Selecteer **SMB** als het protocol type voor het volume. 
    * Selecteer uw **Active Directory** verbinding in de vervolg keuzelijst.
    * Geef de naam op van het gedeelde volume in  **share naam**.

    ![SMB-protocol opgeven](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Klik op **beoordeling + maken** om de details van het volume te controleren.  Klik vervolgens op **maken** om het SMB-volume te maken.

    Het volume dat u hebt gemaakt, wordt weer gegeven op de pagina volumes. 
 
    Een volume neemt het abonnement, de resourcegroep en de locatiekenmerken over van de bijbehorende capaciteitspool. U kunt de implementatiestatus van het volume controleren vanuit het tabblad Meldingen.

## <a name="control-access-to-an-smb-volume"></a>Toegang tot een SMB-volume beheren  

Toegang tot een SMB-volume wordt beheerd via machtigingen.  

### <a name="share-permissions"></a>Share machtigingen  

Een nieuw volume heeft standaard de machtigingen delen **iedereen/volledig beheer** . Leden van de groep domein Administrators kunnen de share machtigingen wijzigen door gebruik te maken van computer beheer op het computer account dat wordt gebruikt voor het Azure NetApp Files volume.

![SMB-koppel pad ](../media/azure-netapp-files/smb-mount-path.png) 
 ![ machtigingen voor delen instellen](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>NTFS-bestands-en mapmachtigingen  

U kunt machtigingen voor een bestand of map instellen met behulp van het tabblad **beveiliging** van de eigenschappen van het object in de Windows SMB-client.
 
![Machtigingen voor bestanden en mappen instellen](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Volgende stappen  

* [Een volume voor Windows- of Linux-VM's koppelen of ontkoppelen](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Veelgestelde vragen over SMB](./azure-netapp-files-faqs.md#smb-faqs)
* [Problemen met SMB-of Dual-protocol volumes oplossen](troubleshoot-dual-protocol-volumes.md)
* Meer informatie over [Integratie van virtuele netwerken voor Azure-services](../virtual-network/virtual-network-for-azure-services.md)
* [Een nieuw Active Directory-forest installeren met behulp van Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
