---
title: Řešení potíží s Azure Stack Edge pomocí GPU | Microsoft Docs
description: Popisuje řešení potíží s chybami certifikátů pomocí Azure Stack zařízení GPU na hraničních zařízeních.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 05338fb70bfa390ff3bd9e14e938edeb40938aeb
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269433"
---
# <a name="troubleshooting-certificate-errors"></a>Řešení chyb certifikátů

Tento článek poskytuje řešení běžných chyb certifikátů při instalaci certifikátů do zařízení Azure Stack Edge.

## <a name="common-certificate-errors"></a>Běžné chyby certifikátů

V následující tabulce jsou uvedeny běžné chyby certifikátů a podrobné informace o těchto chybách a možných řešeních:

> [!NOTE]
> Výskyty &#8220;{0} , {1} ,..., {n} &#8221; označují poziční parametry. Poziční parametry převezmou hodnoty v závislosti na certifikátech, které používáte.

| Kód chyby | Description |
|---|---|
| CertificateManagement_UntrustedCertificate | Certifikát s názvem subjektu {0} má Porušený řetěz certifikátů. Před nahráním tohoto certifikátu Nahrajte certifikát podpisového řetězce.|
| CertificateManagement_DeviceNotRegistered| Vaše zařízení není aktivované. Certifikát podpory můžete nahrát až po aktivaci.|
| CertificateManagement_EmptySAN | Certifikát s názvem subjektu neobsahuje {0} alternativní název subjektu. Podívejte se na vlastnosti svého certifikátu a přepněte nový certifikát.|
| CertificateManagement_ExpiredCertificate | Platnost certifikátu s typem {0} vypršela nebo brzy vyprší. Ověřte vypršení platnosti certifikátu a v případě potřeby ho přeneste do nového certifikátu.|
| CertificateManagement_FormatMismatch | Formát certifikátu není podporován. Ověřte formát certifikátu a v případě potřeby ho přeneste do nového certifikátu.  Očekávalo {0} se, našlo se {1} . |
| CertificateManagement_GenericError | Nelze provést operaci správy certifikátů. Zkuste tuto operaci zopakovat za pár minut. Pokud potíže potrvají, obraťte se na podpora Microsoftu. |
| CertificateManagement_HttpsBindingFailure | Certifikátu s názvem subjektu {0} se nepodařilo vytvořit zabezpečený kanál protokolu HTTPS. Ověřte certifikát, který jste nahráli, a v případě potřeby ho přeneste do nového certifikátu. K této chybě dochází s certifikátem uzlu zařízení.|
| CertificateManagement_IncorrectKeyCertSignKeyUsage | Certifikát s názvem subjektu {0} by neměl mít podepisování certifikátu použití klíče. Ověřte použití klíče certifikátu a v případě potřeby ho přeneste do nového certifikátu. K této chybě dochází s certifikátem podpisového řetězce. |
| CertificateManagement_IncorrectKeyNumber | Certifikát s názvem subjektu {0} má nesprávné číslo klíče {1} . Podívejte se na číslo klíče certifikátu a v případě potřeby ho přeneste do nového certifikátu.|
| CertificateManagement_InvalidP7b | Nahraný soubor certifikátu není platný.  Ověřte formát certifikátu a v případě potřeby ho přeneste do nového certifikátu.|
| CertificateManagement_KeyAlgorithmNotRSA | Tento certifikát nepoužívá algoritmus RSA Key. Podporují se jenom certifikáty RSA. |
| CertificateManagement_KeySizeNotSufficient | Certifikát s názvem subjektu {0} má nedostatečnou velikost klíče {1} . Minimální velikost klíče je 4096.|
| CertificateManagement_MissingClientOid | Certifikát s názvem subjektu nemá {0} identifikátor OID ověřování klienta. Ověřte vlastnosti certifikátu a v případě potřeby přepněte do nového certifikátu.|
| CertificateManagement_MissingDigitalSignatureKeyUsage | Certifikát s názvem subjektu nemá {0} v použití klíče digitální podpis. Ověřte vlastnosti certifikátu a v případě potřeby přepněte do nového certifikátu. |
| CertificateManagement_MissingEntryInSAN | Certifikát s názvem subjektu neobsahuje {0} položku názvu subjektu v alternativním názvu subjektu. Podívejte se na vlastnosti svého certifikátu a přepněte nový certifikát. |
| CertificateManagement_MissingKeyCertSignKeyUsage | Certifikát s názvem subjektu nemá {0} při použití klíče podepisování certifikátu. Ověřte vlastnosti certifikátu a v případě potřeby přepněte do nového certifikátu.|
| CertificateManagement_MissingKeyEnciphermentKeyUsage | Certifikát s názvem subjektu nemá {0} zašifrování klíče v použití klíče. Ověřte vlastnosti certifikátu a v případě potřeby přepněte do nového certifikátu. |
| CertificateManagement_MissingServerOid | Certifikát s názvem subjektu neobsahuje {0} identifikátor OID ověřování serveru. Ověřte vlastnosti certifikátu a v případě potřeby přepněte do nového certifikátu.|
| CertificateManagement_NameMismatch | Neshoda typu certifikátu. Byl nalezen očekávaný rozsah: {0} , nalezeno {1} . Nahrajte příslušný certifikát.|
| CertificateManagement_NoPrivateKeyPresent | V certifikátu s názvem subjektu není {0} přítomen žádný privátní klíč. Nahrajte certifikát. pfx s privátním klíčem.|
| CertificateManagement_NotSelfSignedCertificate | Certifikát s názvem subjektu není {0} podepsaný svým vlastníkem. Kořenové certifikáty by měly být podepsané svým vlastníkem |
| CertificateManagement_NotSupportedOnVirtualAppliance | Tato operace není ve virtuálním zařízení podporovaná. Tato chyba znamená, že podepisování proběhne pouze u Data Box Gateway spuštěného v cloudovém zařízení taktické. K této chybě dochází při správě zařízení prostřednictvím prostředí Windows PowerShell.|
| CertificateManagement_SelfSignedCertificate | Certifikát s názvem subjektu {0} je podepsaný svým jménem. Nahrajte certifikát, který je správně podepsaný.|
| CertificateManagement_SignatureAlgorithmSha1 | Certifikát s názvem subjektu {0} má nepodporovaný algoritmus podpisu. SHA1 – šifrování RSA není podporováno. |
| CertificateManagement_SubjectNamesInvalid | Certifikát s názvem subjektu nemá {0} správný název předmětu nebo alternativní názvy subjektu pro {1} certifikát. Ověřte certifikát, který jste nahráli, a v případě potřeby ho přeneste do nového certifikátu. Měli byste také ověřit, že název DNS bude odpovídat názvům sítě SAN.|
| CertificateManagement_UnreadableCertificate | Certifikát s typem {0} nelze přečíst. K této chybě dochází, pokud je certifikát nečitelný nebo poškozený. Přepněte do nového certifikátu.|
| CertificateSubjectNotFound | Certifikát s názvem subjektu se {0} nepovedlo najít. Přepněte do nového certifikátu.|

## <a name="next-steps"></a>Další kroky

[Požadavky na certifikáty](azure-stack-edge-j-series-certificate-requirements.md)