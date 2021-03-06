---
title: Copiar arquivos novos e alterados por LastModifiedDate com Azure Data Factory | Microsoft Docs
description: Saiba como usar um modelo de solução para copiar arquivos novos e alterados por LastModifiedDate com Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/8/2019
ms.openlocfilehash: a2a8f0478d1ae4fb19cb911b02572145ff59839b
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030064"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Copiar arquivos novos e alterados por LastModifiedDate com Azure Data Factory

Este artigo descreve um modelo de solução que você pode usar para copiar arquivos novos e alterados somente pelo LastModifiedDate de um repositório baseado em arquivo para um repositório de destino. 

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Esse modelo primeiro seleciona os arquivos novos e alterados somente por seus atributos **LastModifiedDate**e, em seguida, copia esses arquivos selecionados do repositório de fonte de dados para o repositório de destino de dados.

O modelo contém uma atividade:
- **Copiar** para copiar arquivos novos e alterados somente por LastModifiedDate de um repositório de arquivos para um repositório de destino.

O modelo define quatro parâmetros:
-  *FolderPath_Source* é o caminho da pasta onde você pode ler os arquivos do repositório de origem. Você precisa substituir o valor padrão pelo seu próprio caminho de pasta.
-  *FolderPath_Destination* é o caminho da pasta onde você deseja copiar os arquivos para o repositório de destino. Você precisa substituir o valor padrão pelo seu próprio caminho de pasta.
-  *LastModified_From* é usado para selecionar os arquivos cujo atributo LastModifiedDate é posterior ou igual a esse valor DateTime.  Para selecionar somente os novos arquivos, que não foram copiados pela última vez, esse valor DateTime pode ser o horário em que o pipeline foi disparado pela última vez. Você pode substituir o valor padrão ' 2019-02-01T00:00:00Z ' para o seu LastModifiedDate esperado no fuso horário UTC. 
-  *LastModified_To* é usado para selecionar os arquivos cujo atributo LastModifiedDate é anterior a esse valor DateTime. Para selecionar somente os novos arquivos, que não foram copiados pela última vez, esse valor DateTime pode ser o tempo atual.  Você pode substituir o valor padrão ' 2019-02-01T00:00:00Z ' para o seu LastModifiedDate esperado no fuso horário UTC. 

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Ir para o modelo **copiar novos arquivos somente por LastModifiedDate**. Crie uma **nova** conexão com o repositório de armazenamento de origem. O repositório de armazenamento de origem do qual você deseja copiar arquivos.

    ![Criar uma nova conexão para a origem](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Primeiro, selecione o **tipo**de armazenamento. Depois disso, insira o **nome da conta** de armazenamento e a **chave da conta**. Por fim, selecione **concluir**.

    ![Inserir uma cadeia de conexão](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. Crie uma **nova** conexão com o repositório de destino. O repositório de destino é onde você deseja copiar arquivos. Você também precisa inserir as informações de conexão do repositório de destino de dados semelhante ao que fez na etapa 2.

    ![Criar uma nova conexão para o destino](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. Selecione **usar este modelo**.

    ![Usar este modelo](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. Você verá o pipeline disponível no painel, conforme mostrado no exemplo a seguir:

    ![Mostrar o pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. Selecione **depurar**, grave o valor para os **parâmetros** e selecione **concluir**.  Na imagem abaixo, definimos os parâmetros como a seguir.
   - **FolderPath_Source** =  **/Source/**
   - **FolderPath_Destination** =  **/destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     O exemplo indica os arquivos que foram modificados pela última vez dentro do período entre *2019-02-01T00:00:00Z* e *2019-03-01T00:00:00Z* serão copiados de uma pasta */Source/* para uma pasta */Destination/* .  Você pode substituí-los por seus próprios parâmetros.
    
     ![Executar o pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. Revise o resultado. Você verá que somente os arquivos modificados pela última vez no TimeSpan configurado foram copiados para o repositório de destino.

    ![Revisar o resultado](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. Agora você pode adicionar um gatilho do Windows em cascata para automatizar esse pipeline, para que o pipeline sempre possa copiar arquivos novos e alterados somente pelo LastModifiedDate periodicamente.  Selecione **Adicionar gatilho**e selecione **novo/editar**.

    ![Revisar o resultado](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. Na janela **Adicionar Gatilhos**, selecione **+ Novo**.

    ![Revisar o resultado](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. Selecione a **janela em cascata** para o tipo de gatilho, defina a **cada 15 minuto (s)** como a recorrência (você pode alterar para qualquer hora de intervalo) e, em seguida, selecione **Avançar**.

    ![Criar gatilho](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. Grave o valor para os **parâmetros de execução do gatilho** conforme a seguir e selecione **concluir**.
    - **FolderPath_Source** =  **/Source/** .  Você pode substituir por sua pasta no armazenamento de dados de origem.
    - **FolderPath_Destination** =  **/Destination/** .  Você pode substituir por sua pasta no armazenamento de dados de destino.
    - **LastModified_From** =   **\@trigger (). Outputs. windowStartTime**.  É uma variável de sistema do gatilho que determina a hora em que o pipeline foi disparado pela última vez.
    - **LastModified_To** =  **\@trigger (). Outputs. windowEndTime**.  É uma variável de sistema do gatilho que determina a hora em que o pipeline é disparado desta vez.
    
    ![Parâmetros de entrada](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. Selecione **Publicar Tudo**.
    
    ![Publicar Tudo](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. Crie novos arquivos na pasta de origem do repositório de fonte de dados.  Agora você está aguardando que o pipeline seja disparado automaticamente e somente os novos arquivos serão copiados para o repositório de destino.

14. Selecione a guia **monitoramento** no painel de navegação à esquerda e aguarde cerca de 15 minutos se a recorrência do gatilho tiver sido definida para a cada 15 minutos. 

    ![Selecionar monitoramento](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. Revise o resultado. Você verá que seu pipeline será disparado automaticamente a cada 15 minutos e somente os arquivos novos ou alterados do repositório de origem serão copiados para o repositório de destino em cada execução de pipeline.

    ![Revisar o resultado](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Data Factory](introduction.md)
