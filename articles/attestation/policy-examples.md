---
title: Voorbeelden van een Azure Attestation-beleid
description: Voorbeelden van Azure Attestation-beleid.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 6a5460a691658bda1cd60e503be8c98433c9c343
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720151"
---
# <a name="examples-of-an-attestation-policy"></a>Voorbeelden van een attestation-beleid

Attestation-beleid wordt gebruikt om de attestation-bewijzen te verwerken en te bepalen of Azure Attestation een attestation-token zal uitgeven. Het genereren van attestion-tokens kan worden beheerd met aangepast beleid. Hieronder ziet u enkele voorbeelden van een attestation-beleid. 

## <a name="sample-custom-policy-for-an-sgx-enclave"></a>Voorbeeld van standaardbeleid voor een SGX-enclave 

```
version= 1.0;
authorizationrules
{
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==<product-id> ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="<mrsigner>"]
    => permit();
};
issuancerules {
c:[type=="x-ms-sgx-mrsigner"] => issue(type="<custom-name>", value=c.value);
};

```
Zie [claim sets](/azure/attestation/claim-sets)voor meer informatie over de binnenkomende claims die door Azure Attestation worden gegenereerd. Binnenkomende claims kunnen door auteurs van beleid worden gebruikt om autorisatie regels in een aangepast beleid te definiëren. 

De sectie uitgifte regels is niet verplicht. Deze sectie kan door de gebruikers worden gebruikt voor het genereren van extra uitgaande claims in het Attestation-token met aangepaste namen. Zie [claim sets](/azure/attestation/claim-sets)voor meer informatie over de uitgaande claims die door de service in de Attestation-token worden gegenereerd.

## <a name="default-policy-for-an-sgx-enclave"></a>Standaardbeleid voor een SGX-enclave

```
version= 1.0;
authorizationrules
{
    c:[type=="$is-debuggable"] => permit();
};

issuancerules
{
    c:[type=="$is-debuggable"] => issue(type="is-debuggable", value=c.value);
    c:[type=="$sgx-mrsigner"] => issue(type="sgx-mrsigner", value=c.value);
    c:[type=="$sgx-mrenclave"] => issue(type="sgx-mrenclave", value=c.value);
    c:[type=="$product-id"] => issue(type="product-id", value=c.value);
    c:[type=="$svn"] => issue(type="svn", value=c.value);
    c:[type=="$tee"] => issue(type="tee", value=c.value);
};
```

Claims die worden gebruikt in het standaard beleid, worden beschouwd als afgeschaft, maar worden wel volledig ondersteund en blijven in de toekomst opgenomen. Het is raadzaam om de niet-afgeschafte claim namen te gebruiken. Zie [claim sets](/azure/attestation/claim-sets)voor meer informatie over de aanbevolen claim namen. 

## <a name="sample-custom-policy-to-support-multiple-sgx-enclaves"></a>Voor beeld van een aangepast beleid ter ondersteuning van meerdere SGX-enclaves

```
version= 1.0;
authorizationrules 
{
    [ type=="x-ms-sgx-is-debuggable", value==true ]&&
    [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
    [ type=="x-ms-sgx-is-debuggable", value==true ]&& 
    [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
};
```

## <a name="unsigned-policy-for-an-sgx-enclave-with-policyformatjwt"></a>Niet-ondertekend beleid voor een SGX-enclave met PolicyFormat=JWT

```
eyJhbGciOiJub25lIn0.eyJBdHRlc3RhdGlvblBvbGljeSI6ICJkbVZ5YzJsdmJqMGdNUzR3TzJGMWRHaHZjbWw2WVhScGIyNXlkV3hsYzN0ak9sdDBlWEJsUFQwaUpHbHpMV1JsWW5WbloyRmliR1VpWFNBOVBpQndaWEp0YVhRb0tUdDlPMmx6YzNWaGJtTmxjblZzWlhON1l6cGJkSGx3WlQwOUlpUnBjeTFrWldKMVoyZGhZbXhsSWwwZ1BUNGdhWE56ZFdVb2RIbHdaVDBpYVhNdFpHVmlkV2RuWVdKc1pTSXNJSFpoYkhWbFBXTXVkbUZzZFdVcE8yTTZXM1I1Y0dVOVBTSWtjMmQ0TFcxeWMybG5ibVZ5SWwwZ1BUNGdhWE56ZFdVb2RIbHdaVDBpYzJkNExXMXljMmxuYm1WeUlpd2dkbUZzZFdVOVl5NTJZV3gxWlNrN1l6cGJkSGx3WlQwOUlpUnpaM2d0YlhKbGJtTnNZWFpsSWwwZ1BUNGdhWE56ZFdVb2RIbHdaVDBpYzJkNExXMXlaVzVqYkdGMlpTSXNJSFpoYkhWbFBXTXVkbUZzZFdVcE8yTTZXM1I1Y0dVOVBTSWtjSEp2WkhWamRDMXBaQ0pkSUQwLUlHbHpjM1ZsS0hSNWNHVTlJbkJ5YjJSMVkzUXRhV1FpTENCMllXeDFaVDFqTG5aaGJIVmxLVHRqT2x0MGVYQmxQVDBpSkhOMmJpSmRJRDAtSUdsemMzVmxLSFI1Y0dVOUluTjJiaUlzSUhaaGJIVmxQV011ZG1Gc2RXVXBPMk02VzNSNWNHVTlQU0lrZEdWbElsMGdQVDRnYVhOemRXVW9kSGx3WlQwaWRHVmxJaXdnZG1Gc2RXVTlZeTUyWVd4MVpTazdmVHMifQ.
```

## <a name="signed-policy-for-an-sgx-enclave-with-policyformatjwt"></a>Ondertekend beleid voor een SGX-enclave met PolicyFormat=JWT

```
eyJhbGciOiJSU0EyNTYiLCJ4NWMiOlsiTUlJQzFqQ0NBYjZnQXdJQkFnSUlTUUdEOUVGakJcdTAwMkJZd0RRWUpLb1pJaHZjTkFRRUxCUUF3SWpFZ01CNEdBMVVFQXhNWFFYUjBaWE4wWVhScGIyNURaWEowYVdacFkyRjBaVEF3SGhjTk1qQXhNVEl6TVRneU1EVXpXaGNOTWpFeE1USXpNVGd5TURVeldqQWlNU0F3SGdZRFZRUURFeGRCZEhSbGMzUmhkR2x2YmtObGNuUnBabWxqWVhSbE1EQ0NBU0l3RFFZSktvWklodmNOQVFFQkJRQURnZ0VQQURDQ0FRb0NnZ0VCQUpyRWVNSlo3UE01VUJFbThoaUNLRGh6YVA2Y2xYdkhmd0RIUXJ5L3V0L3lHMUFuMGJ3MVU2blNvUEVtY2FyMEc1WmYxTUR4alZOdEF5QjZJWThKLzhaQUd4eFFnVVZsd1dHVmtFelpGWEJVQTdpN1B0NURWQTRWNlx1MDAyQkJnanhTZTBCWVpGYmhOcU5zdHhraUNybjYwVTYwQUU1WFx1MDAyQkE1M1JvZjFUUkNyTXNLbDRQVDRQeXAzUUtNVVlDaW9GU3d6TkFQaU8vTy9cdTAwMkJIcWJIMXprU0taUXh6bm5WUGVyYUFyMXNNWkptRHlyUU8vUFlMTHByMXFxSUY2SmJsbjZEenIzcG5uMXk0Wi9OTzJpdFBxMk5Nalx1MDAyQnE2N1FDblNXOC9xYlpuV3ZTNXh2S1F6QVR5VXFaOG1PSnNtSThUU05rLzBMMlBpeS9NQnlpeDdmMTYxQ2tjRm1LU3kwQ0F3RUFBYU1RTUE0d0RBWURWUjBUQkFVd0F3RUIvekFOQmdrcWhraUc5dzBCQVFzRkFBT0NBUUVBZ1ZKVWRCaXRud3ZNdDdvci9UMlo4dEtCUUZsejFVcVVSRlRUTTBBcjY2YWx2Y2l4VWJZR3gxVHlTSk5pbm9XSUJROU9QamdMa1dQMkVRRUtvUnhxN1NidGxqNWE1RUQ2VjRyOHRsejRISjY0N3MyM2V0blJFa2o5dE9Gb3ZNZjhOdFNVeDNGTnBhRUdabDJMUlZHd3dcdTAwMkJsVThQd0gzL2IzUmVCZHRhQTdrZmFWNVx1MDAyQml4ZWRjZFN5S1F1VkFUbXZNSTcxM1A4VlBsNk1XbXNNSnRrVjNYVi9ZTUVzUVx1MDAyQkdZcU1yN2tLWGwxM3lldUVmVTJWVkVRc1ovMXRnb29iZVZLaVFcdTAwMkJUcWIwdTJOZHNcdTAwMkJLamRIdmFNYngyUjh6TDNZdTdpR0pRZnd1aU1tdUxSQlJwSUFxTWxRRktLNmRYOXF6Nk9iT01zUjlpczZ6UDZDdmxGcEV6bzVGUT09Il19.eyJBdHRlc3RhdGlvblBvbGljeSI6ImRtVnljMmx2YmoweExqQTdZWFYwYUc5eWFYcGhkR2x2Ym5KMWJHVnpJSHRqT2x0MGVYQmxQVDBpSkdsekxXUmxZblZuWjJGaWJHVWlYU0FtSmlCYmRtRnNkV1U5UFhSeWRXVmRJRDAtSUdSbGJua29LVHM5UGlCd1pYSnRhWFFvS1R0OU8ybHpjM1ZoYm1ObGNuVnNaWE1nZXlBZ0lDQmpPbHQwZVhCbFBUMGlKR2x6TFdSbFluVm5aMkZpYkdVaVhTQTlQaUJwYzNOMVpTaDBlWEJsUFNKT2IzUkVaV0oxWjJkaFlteGxJaXdnZG1Gc2RXVTlZeTUyWVd4MVpTazdJQ0FnSUdNNlczUjVjR1U5UFNJa2FYTXRaR1ZpZFdkbllXSnNaU0pkSUQwLUlHbHpjM1ZsS0hSNWNHVTlJbWx6TFdSbFluVm5aMkZpYkdVaUxDQjJZV3gxWlQxakxuWmhiSFZsS1RzZ0lDQWdZenBiZEhsd1pUMDlJaVJ6WjNndGJYSnphV2R1WlhJaVhTQTlQaUJwYzNOMVpTaDBlWEJsUFNKelozZ3RiWEp6YVdkdVpYSWlMQ0IyWVd4MVpUMWpMblpoYkhWbEtUc2dJQ0FnWXpwYmRIbHdaVDA5SWlSelozZ3RiWEpsYm1Oc1lYWmxJbDBnUFQ0Z2FYTnpkV1VvZEhsd1pUMGljMmQ0TFcxeVpXNWpiR0YyWlNJc0lIWmhiSFZsUFdNdWRtRnNkV1VwT3lBZ0lDQmpPbHQwZVhCbFBUMGlKSEJ5YjJSMVkzUXRhV1FpWFNBOVBpQnBjM04xWlNoMGVYQmxQU0p3Y205a2RXTjBMV2xrSWl3Z2RtRnNkV1U5WXk1MllXeDFaU2s3SUNBZ0lHTTZXM1I1Y0dVOVBTSWtjM1p1SWwwZ1BUNGdhWE56ZFdVb2RIbHdaVDBpYzNadUlpd2dkbUZzZFdVOVl5NTJZV3gxWlNrN0lDQWdJR002VzNSNWNHVTlQU0lrZEdWbElsMGdQVDRnYVhOemRXVW9kSGx3WlQwaWRHVmxJaXdnZG1Gc2RXVTlZeTUyWVd4MVpTazdmVHMifQ.c0l-xqGDFQ8_kCiQ0_vvmDQYG_u544CYmoiucPNxd9MU8ZXT69UD59UgSuya2yl241NoVXA_0LaMEB2re0JnTbPD_dliJn96HnIOqnxXxRh7rKbu65ECUOMWPXbyKQMZ0I3Wjhgt_XyyhfEiQGfJfGzA95-wm6yWqrmW7dMI7JkczG9ideztnr0bsw5NRsIWBXOjVy7Bg66qooTnODS_OqeQ4iaNsN-xjMElHABUxXhpBt2htbhemDU1X41o8clQgG84aEHCgkE07pR-7IL_Fn2gWuPVC66yxAp00W1ib2L-96q78D9J52HPdeDCSFio2RL7r5lOtz8YkQnjacb6xA       
```

</br>

## <a name="next-steps"></a>Volgende stappen

- [Een Attestation-beleid ontwerpen en ondertekenen](author-sign-policy.md)
- [Azure Attestation instellen met behulp van PowerShell](quickstart-powershell.md)
