<properties
    pageTitle="Een lab maken in DevTest Labs | Microsoft Azure"
    description="Een nieuw lab voor virtuele machines maken in DevTest Labs"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/01/2016"
    ms.author="tarcher"/>

# Een lab maken in Azure DevTest Labs

## Vereisten

Als u een lab wilt maken, hebt u de volgende zaken nodig:

- Een Azure-abonnement. Zie [Azure aanschaffen](https://azure.microsoft.com/pricing/purchase-options/) of [Gratis proefversie van één maand](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie over Azure-aankoopopties. U moet de eigenaar van het abonnement zijn om het lab te maken.
- Een Azure-resourcegroep voor het lab. Zie [Overzicht van Azure Resource Manager](../resource-group-overview.md) en [Op rollen gebaseerd toegangsbeheer van Azure](../active-directory/role-based-access-control-configure.md).

## Een lab maken

1. Meld u aan bij [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecteer **Bladeren**.

1. Selecteer **DevTest Labs** uit de lijst.

1. Selecteer op de blade **DevTest Labs** de optie **Toevoegen**.

    ![Een lab toevoegen](./media/devtest-lab-create-lab/add-lab-button.png)

1. Op de blade **Een DevTest Lab maken**:

    1. Voer een **labnaam** in voor het nieuwe lab.
    1. Selecteer het **abonnement** dat u wilt koppelen aan het lab.
    1. Selecteer op welke **locatie** u het lab wilt opslaan.
    1. Selecteer **Maken**.

    ![Een labblade maken](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

## Volgende stappen

Wanneer u uw lab hebt gemaakt, kunt u onder andere de volgende stappen uitvoeren:

- [Toegang tot een lab beveiligen](devtest-lab-add-devtest-user.md).

- [Labbeleidsregels instellen](devtest-lab-set-lab-policy.md).

- [Een labsjabloon maken](devtest-lab-create-template.md).

- [Aangepaste artefacten maken voor uw virtuele machines](devtest-lab-artifact-author.md).

- [Een VM met artefacten toevoegen aan een lab](devtest-lab-add-vm-with-artifacts.md).


<!--HONumber=Jun16_HO2-->


