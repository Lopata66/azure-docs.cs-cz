---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/22/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 0cfa0fdb51969c92e767adfa86a0065d11da56e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237752"
---
[Služba soubory Azure](../articles/storage/files/storage-files-introduction.md) podporuje ověřování na základě identity přes protokol SMB (Server Message Block) (preview) prostřednictvím [domény služby Azure Active Directory (Azure AD)](../articles/active-directory-domain-services/overview.md). Připojené k doméně Windows virtuálních počítačů (VM) můžete přístup k sdílenými složkami Azure pomocí [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md) přihlašovací údaje. 

Služba Azure AD ověřuje identitu, která například uživatel, skupina nebo instanční objekt s [řízení přístupu na základě role (RBAC)](../articles/role-based-access-control/overview.md). Můžete definovat vlastní role RBAC, které zahrnuje společné sady oprávnění pro přístup k Azure Files. Když přiřadíte vlastní roli RBAC do identity Azure AD, že identita je udělen přístup k sdílené složky Azure podle těchto oprávnění.

V rámci verze preview se soubory Azure také podporuje zachování dědění a vynucování [NTFS DACL](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) na všechny soubory a adresáře ve sdílené složce. Pokud se kopírování dat ze sdílené složky do služby soubory Azure, nebo naopak, můžete určit, že se zachovají DACL systému souborů NTFS. Tímto způsobem můžete implementovat scénáře zálohování pomocí služby soubory Azure, zachování vašeho systému souborů NTFS DACL mezi vaší místní sdílené složky a sdílené složce cloudu. 

> [!NOTE]
> - Ověřování Azure AD prostřednictvím protokolu SMB se nepodporuje pro virtuální počítače s Linuxem ve verzi preview. Podporují se jenom virtuální počítače Windows serveru.
> - Ověřování Azure AD prostřednictvím protokolu SMB se nepodporuje pro místní počítače přistupující k Azure Files.
> - Ověřování Azure AD je k dispozici pouze pro účty úložiště vytvořené po 24. září 2018.
> - Ověřování Azure AD prostřednictvím protokolu SMB a systému souborů NTFS ACL trvalé nepodporuje sdílené složky Azure spravované pomocí služby Azure File Sync. 
