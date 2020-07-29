---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8817f5c7ab53c5b0ebb0e8adf8c6f0f7b8a2ff42
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87372314"
---
[Soubory Azure](../articles/storage/files/storage-files-introduction.md) podporují ověřování na základě identity přes protokol SMB (Server Message Block) prostřednictvím [místních Active Directory Domain Services (služba AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) a [Azure Active Directory Domain Services (Azure služba AD DS)](../articles/active-directory-domain-services/overview.md). Tento článek se zaměřuje na to, jak můžou sdílené složky Azure používat doménové služby, a to buď místně, nebo v Azure, k podpoře přístupu na základě identity ke sdíleným složkám Azure přes protokol SMB. Povolení přístupu na základě identity pro sdílené složky Azure vám umožní nahradit stávající souborové servery službou Azure Shared File, aniž byste museli stávající adresářovou službu nahradit, a přitom zajistit bezproblémové uživatelské oprávnění ke sdíleným složkám. 

Soubory Azure vynutily autorizaci při přístupu uživatelů ke sdílené složce i k úrovni adresářů a souborů. Přiřazení oprávnění na úrovni sdílené složky se dá provádět u uživatelů Azure Active Directory (Azure AD) nebo skupin spravovaných prostřednictvím modelu [řízení přístupu na základě role (RBAC)](../articles/role-based-access-control/overview.md) . V případě RBAC by přihlašovací údaje, které používáte pro přístup k souborům, měly být k dispozici nebo synchronizovány do Azure AD. Uživatelům nebo skupinám v Azure AD můžete přiřadit předdefinované role Azure, jako je soubor úložiště. čtečka sdílení souborů SMB pro udělení oprávnění ke čtení sdílené složky Azure.

Na úrovni adresářů a souborů podporuje Azure Files udržování, dědění a vynucování [seznamů DACL](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) , stejně jako všechny souborové servery Windows. Při kopírování dat přes SMB mezi stávající sdílenou složkou a sdílenými složkami Azure se můžete rozhodnout zachovat seznamy DACL. Bez ohledu na to, jestli plánujete autorizaci, můžete k zálohování seznamů ACL spolu s daty použít sdílené složky Azure. 
