---
title: Toegang verlenen tot blobs met AzCopy & Azure Active Directory | Microsoft Docs
description: U kunt autorisatie referenties opgeven voor AzCopy-bewerkingen met behulp van Azure Active Directory (Azure AD).
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 46a5c941822dd258b420b51c710734dc3152f30f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617405"
---
# <a name="authorize-access-to-blobs-with-azcopy-and-azure-active-directory-azure-ad"></a>Toegang verlenen tot blobs met AzCopy en Azure Active Directory (Azure AD)

U kunt AzCopy met autorisatie referenties opgeven met behulp van Azure AD. Op die manier hoeft u een SAS-token (Shared Access Signature) niet aan elke opdracht toe te voegen. 

Controleer eerst uw roltoewijzingen. Kies vervolgens het type beveiligings- _Principal_ dat u wilt autoriseren. Een [gebruikers-id](../../active-directory/fundamentals/add-users-azure-active-directory.md), een [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md)en een [Service-Principal](../../active-directory/develop/app-objects-and-service-principals.md) zijn elk type beveiligings-principal.

Een gebruikers-id is een gebruiker met een identiteit in azure AD. Het is de eenvoudigste beveiligings-principal die u wilt autoriseren. Beheerde identiteiten en service-principals zijn uitstekende opties als u van plan bent AzCopy te gebruiken in een script dat wordt uitgevoerd zonder tussen komst van de gebruiker. Een beheerde identiteit is beter geschikt voor scripts die worden uitgevoerd vanaf een virtuele Azure-machine (VM) en een Service-Principal is beter geschikt voor scripts die on-premises worden uitgevoerd. 

Ga aan de [slag met AzCopy](storage-use-azcopy-v10.md)voor meer informatie over AzCopy.

## <a name="verify-role-assignments"></a>Roltoewijzingen controleren

Het machtigings niveau dat u nodig hebt, is gebaseerd op de vraag of u bestanden wilt uploaden of gewoon wilt downloaden.

Als u alleen bestanden wilt downloaden, controleert u of de rol van [BLOB-gegevens lezer voor opslag](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) is toegewezen aan uw gebruikers-id, beheerde identiteit of Service-Principal.

Als u bestanden wilt uploaden, controleert u of een van deze rollen is toegewezen aan uw beveiligingsprincipal:

- [Inzender voor Storage Blob-gegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- [Eigenaar van opslagblobgegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

Deze rollen kunnen worden toegewezen aan uw beveiligingsprincipal in een van deze bereiken:

- Container (bestands systeem)
- Storage-account
- Resourcegroep
- Abonnement

Zie [de Azure Portal gebruiken om een Azure-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](./storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)voor meer informatie over het controleren en toewijzen van rollen.

> [!NOTE]
> Houd er rekening mee dat de toewijzingen van Azure-rollen Maxi maal vijf minuten kunnen worden door gegeven.

U hoeft niet een van deze rollen te hebben toegewezen aan uw beveiligingsprincipal als uw beveiligingsprincipal wordt toegevoegd aan de toegangs beheer lijst (ACL) van de doel container of directory. In de ACL heeft uw beveiligings-principal schrijf machtigingen nodig voor de doel directory, en moet de machtiging voor het uitvoeren van de container en elke bovenliggende map worden uitgevoerd.

Zie voor meer informatie [Access Control model in azure data Lake Storage Gen2](../blobs/data-lake-storage-access-control-model.md).

## <a name="authorize-a-user-identity"></a>Een gebruikers identiteit autoriseren

Nadat u hebt gecontroleerd of uw gebruikers-id het benodigde autorisatie niveau heeft gekregen, opent u een opdracht prompt, typt u de volgende opdracht en drukt u vervolgens op ENTER.

```azcopy
azcopy login
```

Als u een fout bericht ontvangt, kunt u de Tenant-ID van de organisatie waartoe het opslag account behoort, opnemen.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Vervang de `<tenant-id>` tijdelijke aanduiding door de Tenant-id van de organisatie waartoe het opslag account behoort. Als u de Tenant-ID wilt vinden, selecteert u **Azure Active Directory > eigenschappen > Directory-id** in het Azure Portal.

Met deze opdracht worden een verificatiecode en de URL van een website geretourneerd. Open de website, geef de code op en kies vervolgens de knop **Volgende**.

![Een container maken](media/storage-use-azcopy-v10/azcopy-login.png)

Er wordt een aanmeldingsvenster weergegeven. Meld u in dat venster aan bij uw Azure-account met behulp van de referenties van uw Azure-account. Nadat u bent aangemeld, kunt u het browservenster sluiten en AzCopy gebruiken.

<a id="managed-identity"></a>

## <a name="authorize-a-managed-identity"></a>Een beheerde identiteit autoriseren

Dit is een uitstekende optie als u van plan bent AzCopy te gebruiken in een script dat wordt uitgevoerd zonder tussen komst van de gebruiker en het script wordt uitgevoerd vanaf een virtuele machine van Azure (VM). Wanneer u deze optie gebruikt, hoeft u geen referenties op te slaan op de VM.

U kunt zich aanmelden bij uw account via een door het systeem beheerde identiteit die u op uw virtuele machine hebt ingeschakeld, of door de client-ID, object-ID of Resource-ID te gebruiken van een door de gebruiker toegewezen beheerde identiteit die u aan uw VM hebt toegewezen.

Zie [beheerde identiteiten voor Azure-resources configureren op een virtuele machine met behulp van de Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)voor meer informatie over het inschakelen van een door het systeem beheerde identiteit of het maken van een door de gebruiker toegewezen beheerde identiteit.

#### <a name="authorize-by-using-a-system-wide-managed-identity"></a>Autoriseren met behulp van een beheerde identiteit voor het hele systeem

Zorg er eerst voor dat u een op het hele systeem beheerde identiteit hebt ingeschakeld op uw VM. Zie door [het systeem toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

Typ vervolgens in de opdracht console de volgende opdracht en druk vervolgens op ENTER.

```azcopy
azcopy login --identity
```

#### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>Autoriseren met een door de gebruiker toegewezen beheerde identiteit

Zorg er eerst voor dat u een door de gebruiker toegewezen beheerde identiteit hebt ingeschakeld op de virtuele machine. Zie door de [gebruiker toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity).

Typ in de opdracht console een van de volgende opdrachten en druk vervolgens op ENTER.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Vervang de `<client-id>` tijdelijke aanduiding door de client-id van de door de gebruiker toegewezen beheerde identiteit.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Vervang de `<object-id>` tijdelijke aanduiding door de object-id van de door de gebruiker toegewezen beheerde identiteit.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Vervang de `<resource-id>` tijdelijke aanduiding door de resource-id van de door de gebruiker toegewezen beheerde identiteit.

<a id="service-principal"></a>

## <a name="authorize-a-service-principal"></a>Een Service-Principal autoriseren

Dit is een uitstekende optie als u van plan bent AzCopy te gebruiken in een script dat wordt uitgevoerd zonder tussen komst van de gebruiker, met name wanneer u on-premises uitvoert. Als u van plan bent AzCopy uit te voeren op Vm's die worden uitgevoerd in azure, is een beheerde service-identiteit eenvoudiger te beheren. Zie de sectie [een beheerde identiteit autoriseren](#authorize-a-managed-identity) in dit artikel voor meer informatie.

Voordat u een script uitvoert, moet u zich ten minste één keer interactief aanmelden zodat u AzCopy kunt opgeven met de referenties van de Service-Principal.  Deze referenties worden opgeslagen in een beveiligd en versleuteld bestand, zodat uw script geen gevoelige informatie hoeft op te geven.

U kunt zich aanmelden bij uw account door gebruik te maken van een client geheim of door het wacht woord te gebruiken van een certificaat dat is gekoppeld aan de app-registratie van uw service-principal.

Zie [How to: de portal gebruiken om een Azure AD-toepassing en Service-Principal te maken die toegang hebben tot resources](../../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie over het maken van een service-principal.

Zie [toepassings-en Service-Principal-objecten in azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) voor meer informatie over service-principals in het algemeen.

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>Een Service-Principal autoriseren met behulp van een client geheim

Begin door de `AZCOPY_SPA_CLIENT_SECRET` omgevings variabele in te stellen op het client geheim van de app-registratie van de Service-Principal.

> [!NOTE]
> Zorg ervoor dat u deze waarde instelt vanaf de opdracht prompt en niet in de omgevings variabele-instellingen van uw besturings systeem. Op die manier is de waarde alleen beschikbaar voor de huidige sessie.

In dit voor beeld ziet u hoe u dit kunt doen in Power shell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> U kunt een prompt gebruiken zoals in dit voor beeld wordt weer gegeven. Op die manier wordt uw wacht woord niet weer gegeven in de opdracht geschiedenis van de console.  

Typ vervolgens de volgende opdracht en druk vervolgens op ENTER.

```azcopy
azcopy login --service-principal  --application-id application-id --tenant-id=tenant-id
```

Vervang de `<application-id>` tijdelijke aanduiding door de toepassings-id van de app-registratie van uw service-principal. Vervang de `<tenant-id>` tijdelijke aanduiding door de Tenant-id van de organisatie waartoe het opslag account behoort. Als u de Tenant-ID wilt vinden, selecteert u **Azure Active Directory > eigenschappen > Directory-id** in het Azure Portal. 

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>Een Service-Principal autoriseren met behulp van een certificaat

Als u uw eigen referenties voor autorisatie wilt gebruiken, kunt u een certificaat uploaden naar de registratie van uw app en dat certificaat vervolgens gebruiken om u aan te melden.

Naast het uploaden van uw certificaat naar de registratie van uw app, moet u ook een kopie van het certificaat hebben opgeslagen op de machine of VM waarop AzCopy wordt uitgevoerd. Deze kopie van het certificaat moet zich in bestaan. PFX of. PEM-indeling en moet de persoonlijke sleutel bevatten. De persoonlijke sleutel moet met een wacht woord zijn beveiligd. Als u Windows gebruikt en uw certificaat alleen bestaat in een certificaat archief, moet u het certificaat exporteren naar een PFX-bestand (met inbegrip van de persoonlijke sleutel). Zie [export-pfx](/powershell/module/pkiclient/export-pfxcertificate) voor meer informatie.

Vervolgens stelt u de `AZCOPY_SPA_CERT_PASSWORD` omgevings variabele in op het certificaat wachtwoord.

> [!NOTE]
> Zorg ervoor dat u deze waarde instelt vanaf de opdracht prompt en niet in de omgevings variabele-instellingen van uw besturings systeem. Op die manier is de waarde alleen beschikbaar voor de huidige sessie.

In dit voor beeld ziet u hoe u deze taak kunt uitvoeren in Power shell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Typ vervolgens de volgende opdracht en druk vervolgens op ENTER.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Vervang de `<path-to-certificate-file>` tijdelijke aanduiding door het relatieve of volledig gekwalificeerde pad naar het certificaat bestand. AzCopy slaat het pad naar dit certificaat op, maar er wordt geen kopie van het certificaat opgeslagen. Zorg er dus voor dat u dat certificaat blijft bewaren. Vervang de `<tenant-id>` tijdelijke aanduiding door de Tenant-id van de organisatie waartoe het opslag account behoort. Als u de Tenant-ID wilt vinden, selecteert u **Azure Active Directory > eigenschappen > Directory-id** in het Azure Portal.

> [!NOTE]
> U kunt een prompt gebruiken zoals in dit voor beeld wordt weer gegeven. Op die manier wordt uw wacht woord niet weer gegeven in de opdracht geschiedenis van de console. 

## <a name="authorize-without-a-secret-store"></a>Autoriseren zonder geheime opslag

Als uw besturings systeem geen geheim archief heeft, zoals een Linux- *sleutel hanger*, `azcopy login` werkt de opdracht niet. In plaats daarvan kunt u omgevings variabelen in het geheugen instellen voordat u elke bewerking uitvoert. 

### <a name="authorize-a-user-identity"></a>Een gebruikers identiteit autoriseren

Nadat u hebt gecontroleerd of uw gebruikers-id het benodigde autorisatie niveau heeft gekregen, typt u de volgende opdracht en drukt u vervolgens op ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=DEVICE
```

Voer vervolgens een wille keurige azcopy-opdracht uit (bijvoorbeeld: `azcopy list https://contoso.blob.core.windows.net` ).

Met deze opdracht worden een verificatiecode en de URL van een website geretourneerd. Open de website, geef de code op en kies vervolgens de knop **Volgende**.

![Een container maken](media/storage-use-azcopy-v10/azcopy-login.png)

Er wordt een aanmeldingsvenster weergegeven. Meld u in dat venster aan bij uw Azure-account met behulp van de referenties van uw Azure-account. Nadat u zich hebt aangemeld, kan de bewerking worden voltooid.

### <a name="authorize-by-using-a-system-wide-managed-identity"></a>Autoriseren met behulp van een beheerde identiteit voor het hele systeem

Zorg er eerst voor dat u een op het hele systeem beheerde identiteit hebt ingeschakeld op uw VM. Zie door [het systeem toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

Typ de volgende opdracht en druk vervolgens op ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

Voer vervolgens een wille keurige azcopy-opdracht uit (bijvoorbeeld: `azcopy list https://contoso.blob.core.windows.net` ).

### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>Autoriseren met een door de gebruiker toegewezen beheerde identiteit

Zorg er eerst voor dat u een door de gebruiker toegewezen beheerde identiteit hebt ingeschakeld op de virtuele machine. Zie door de [gebruiker toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity).

Typ de volgende opdracht en druk vervolgens op ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

Typ vervolgens een van de volgende opdrachten en druk vervolgens op ENTER.

```bash
export AZCOPY_MSI_CLIENT_ID=<client-id>
```

Vervang de `<client-id>` tijdelijke aanduiding door de client-id van de door de gebruiker toegewezen beheerde identiteit.

```bash
export AZCOPY_MSI_OBJECT_ID=<object-id>
```

Vervang de `<object-id>` tijdelijke aanduiding door de object-id van de door de gebruiker toegewezen beheerde identiteit.

```bash
export AZCOPY_MSI_RESOURCE_STRING=<resource-id>
```

Vervang de `<resource-id>` tijdelijke aanduiding door de resource-id van de door de gebruiker toegewezen beheerde identiteit.

Nadat u deze variabelen hebt ingesteld, kunt u elke wille keurige azcopy-opdracht uitvoeren (bijvoorbeeld: `azcopy list https://contoso.blob.core.windows.net` ).

### <a name="authorize-a-service-principal"></a>Een Service-Principal autoriseren

Voordat u een script uitvoert, moet u zich ten minste één keer interactief aanmelden zodat u AzCopy kunt opgeven met de referenties van de Service-Principal.  Deze referenties worden opgeslagen in een beveiligd en versleuteld bestand, zodat uw script geen gevoelige informatie hoeft op te geven.

U kunt zich aanmelden bij uw account door gebruik te maken van een client geheim of door het wacht woord te gebruiken van een certificaat dat is gekoppeld aan de app-registratie van uw service-principal.

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>Een Service-Principal autoriseren met behulp van een client geheim

Typ de volgende opdracht en druk vervolgens op ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_APPLICATION_ID=<application-id>
export AZCOPY_SPA_CLIENT_SECRET=<client-secret>
```

Vervang de `<application-id>` tijdelijke aanduiding door de toepassings-id van de app-registratie van uw service-principal. Vervang de `<client-secret>` tijdelijke aanduiding door het client geheim.

> [!NOTE]
> Overweeg het gebruik van een prompt om het wacht woord van de gebruiker te verzamelen. Op die manier wordt uw wacht woord niet weer gegeven in de opdracht geschiedenis. 

Voer vervolgens een wille keurige azcopy-opdracht uit (bijvoorbeeld: `azcopy list https://contoso.blob.core.windows.net` ).

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>Een Service-Principal autoriseren met behulp van een certificaat

Als u uw eigen referenties voor autorisatie wilt gebruiken, kunt u een certificaat uploaden naar de registratie van uw app en dat certificaat vervolgens gebruiken om u aan te melden.

Naast het uploaden van uw certificaat naar de registratie van uw app, moet u ook een kopie van het certificaat hebben opgeslagen op de machine of VM waarop AzCopy wordt uitgevoerd. Deze kopie van het certificaat moet zich in bestaan. PFX of. PEM-indeling en moet de persoonlijke sleutel bevatten. De persoonlijke sleutel moet met een wacht woord zijn beveiligd. 

Typ de volgende opdracht en druk vervolgens op ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_CERT_PATH=<path-to-certificate-file>
export AZCOPY_SPA_CERT_PASSWORD=<certificate-password>
```

Vervang de `<path-to-certificate-file>` tijdelijke aanduiding door het relatieve of volledig gekwalificeerde pad naar het certificaat bestand. AzCopy slaat het pad naar dit certificaat op, maar er wordt geen kopie van het certificaat opgeslagen. Zorg er dus voor dat u dat certificaat blijft bewaren. Vervang de `<certificate-password>` tijdelijke aanduiding door het wacht woord van het certificaat.

> [!NOTE]
> Overweeg het gebruik van een prompt om het wacht woord van de gebruiker te verzamelen. Op die manier wordt uw wacht woord niet weer gegeven in de opdracht geschiedenis. 

Voer vervolgens een wille keurige azcopy-opdracht uit (bijvoorbeeld: `azcopy list https://contoso.blob.core.windows.net` ).

## <a name="next-steps"></a>Volgende stappen

- Ga aan de [slag met AzCopy](storage-use-azcopy-v10.md) voor meer informatie over AzCopy.

- Als u vragen, problemen of algemene feedback hebt, verzendt u deze [op](https://github.com/Azure/azure-storage-azcopy) de pagina github.