---
title: Azure Data Box řešení potíží s použitím rozhraní REST | Microsoft Docs
description: V této části najdete popis postupu při řešení potíží, které se zobrazují v Azure Data Box při kopírování dat prostřednictvím rozhraní REST.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: ccb1a31761298d9dc4376684380702cf7cbd9a1f
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310066"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Řešení potíží souvisejících s Azure Data Box BLOB Storage

Tento článek podrobně popisuje, jak řešit problémy, které se můžou zobrazit při použití Data Box BLOB Storage přes rozhraní REST na Data Box ke kopírování dat. Tyto problémy se zobrazí, když používáte Data Box BLOB Storage s jinými aplikacemi nebo klientskými knihovnami, jako je Průzkumník služby Azure Storage, AzCopy nebo knihovna Azure Storage pro Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Chyby zjištěné v Průzkumník služby Azure Storage

Tato část podrobně popisuje některé problémy při použití Průzkumník služby Azure Storage s úložištěm objektů BLOB v Data Box.

|Chybová zpráva  |Doporučená akce |
|---------|---------|
|Nelze načíst podřízené prostředky. Hodnota pro jednu z hlaviček protokolu HTTP není ve správném formátu.|V nabídce **Upravit** vyberte **cílová Azure Stack rozhraní API**. <br>Restartujte Průzkumník služby Azure Storage.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Ověřte, že se název koncového bodu `<accountname>.blob.<serialnumber>.microsoftdatabox.com` přidal do souboru hostitelů v této cestě: <li>`C:\Windows\System32\drivers\etc\hosts`ve Windows nebo </li><li> `/etc/hosts`v systému Linux.</li>|
|Nelze načíst podřízené prostředky. <br>Podrobnosti: certifikát podepsaný svým držitelem |Importujte certifikát TLS/SSL pro vaše zařízení do Průzkumník služby Azure Storage: <li>Stáhněte si certifikát z Azure Portal. Další informace najdete v [části stažení certifikátu](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>V nabídce **Upravit** vyberte **certifikáty SSL** a pak vyberte **importovat certifikáty**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Chyby, které se zobrazují v AzCopy pro Windows

Tato část podrobně popisuje některé problémy při použití AzCopy pro Windows s úložištěm objektů BLOB v Data Box.

|Chybová zpráva  |Doporučená akce |
|---------|---------|
|Příkaz AzCopy se zdá, že před zobrazením této chyby přestane reagovat na minutu: <br>Nepovedlo se vytvořit výčet adresáře https://... Vzdálený název se nedal přeložit.`<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Ověřte, že se název koncového bodu `<accountname>.blob.<serialnumber>.microsoftdatabox.com` přidal do souboru hostitelů v umístění: `C:\Windows\System32\drivers\etc\hosts` .|
|Příkaz AzCopy se zdá, že před zobrazením této chyby přestane reagovat na minutu: <br>Při analýze zdrojového umístění došlo k chybě. Základní připojení bylo uzavřeno: nelze vytvořit vztah důvěryhodnosti pro zabezpečený kanál SSL/TLS.|Importujte certifikát TLS/SSL pro vaše zařízení do úložiště certifikátů systému. Další informace najdete v [části stažení certifikátu](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Chyby, které se zobrazují v AzCopy pro Linux

Tato část podrobně popisuje některé problémy při použití AzCopy pro Linux s úložištěm objektů BLOB Data Box.

|Chybová zpráva  |Doporučená akce |
|---------|---------|
|Příkaz AzCopy se zdá, že před zobrazením této chyby přestane reagovat na 20 minut: <br>Při analýze zdrojového umístění došlo k chybě `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>` . Žádné odpovídající zařízení ani adresa|Ověřte, že se název koncového bodu `<accountname>.blob.<serialnumber>.microsoftdatabox.com` přidal do souboru hostitelů v umístění: `/etc/hosts` .|
|Příkaz AzCopy se zdá, že před zobrazením této chyby přestane reagovat na 20 minut: <br>Při analýze zdrojového umístění došlo k chybě... Nebylo možné navázat připojení SSL.|Importujte certifikát TLS/SSL pro vaše zařízení do úložiště certifikátů systému. Další informace najdete v [části stažení certifikátu](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Chyby zobrazené v knihovně Azure Storage pro Python

Tato část podrobně popisuje některé hlavní problémy, na které čelí během nasazování Data Box Disk při použití klienta Linux pro kopírování dat.

|Chybová zpráva  |Doporučená akce |
|---------|---------|
|Hodnota pro jednu z hlaviček protokolu HTTP není ve správném formátu. |Data Box není podporována nainstalovaná verze knihovny Microsoft Azure Storage pro Python. Podporované verze najdete v tématu Azure Data Box požadavky na úložiště objektů BLOB.|
|… [SSL: CERTIFICATE_VERIFY_FAILED]...|Před spuštěním Pythonu nastavte proměnnou prostředí REQUESTS_CA_BUNDLE na cestu k souboru certifikátu TLS s kódováním Base64 (viz jak [Stáhnout certifikát](data-box-deploy-copy-data-via-rest.md#download-certificate)). <br>Například:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Alternativně přidejte certifikát do úložiště certifikátů systému a pak nastavte tuto proměnnou prostředí na cestu k danému úložišti. <br> Například na Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Běžné chyby

Tyto chyby nejsou specifické pro žádnou aplikaci.

|Chybová zpráva  |Doporučená akce |
|---------|---------|
|Vypršel časový limit připojení. |Přihlaste se k zařízení Data Box a ověřte, že je odemčený. Pokaždé, když se zařízení restartuje, zůstane uzamčené, dokud se někdo přihlásí.|

## <a name="next-steps"></a>Další kroky

- Seznamte se s [požadavky na systém data box BLOB Storage](data-box-system-requirements-rest.md).
