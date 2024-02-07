
# Trabalhando com Machine Learning na Prática do Azure ML

> [!TIP]
> O aprendizado de máquina automatizado permite que você experimente vários algoritmos e parâmetros para treinar vários modelos e identificar o melhor para seus dados. Nesse passo a passo, você usará um conjunto de dados de detalhes históricos de aluguel de bicicletas para treinar um modelo que prevê o número de aluguel de bicicletas esperado em um determinado dia, com base em características sazonais e meteorológicas.

> [!WARNING]
> O processo de treinamento do modelo costuma demorar em média 15 minutos, mas pode se estender. Caso demore mais que 1 hora para concluir é recomendável refazer o processo.

# Uso

Criar um novo trabalho automatizado, treinar, criar modelo, implantar, testar o modelo e o serviço implantado.

# Processo

<details>
<summary>Crie um espaço de trabalho do Azure Machine Learning</summary>
           
1. Entre no portal do **[Azure](https://azure.microsoft.com)** com suas credenciais da Microsoft;
1. Selecione + Criar um recurso, pesquise Machine Learning e crie um novo recurso do Azure Machine Learning com as seguintes configurações:
    - Assinatura: sua assinatura do Azure;
    - Grupo de recursos: Crie ou selecione um grupo de recursos;
    - Nome: Insira um nome exclusivo para seu espaço de trabalho;
    - Região: Selecione a região geográfica mais próxima;
    - Conta de armazenamento: observe a nova conta de armazenamento padrão que será criada para seu espaço de trabalho;
    - Cofre de chaves: Observe o novo cofre de chaves padrão que será criado para seu espaço de trabalho;
    - Insights de aplicativos: observe o novo recurso padrão de insights de aplicativos que será criado para seu espaço de 
    trabalho;
    - Registro de contêiner: Nenhum ( um será criado automaticamente na primeira vez que você implantar um modelo em um contêiner);
    - Selecione Revisar + criar e selecione Criar. Aguarde a criação do seu espaço de trabalho (pode demorar alguns minutos) e, em 
    seguida, vá para o recurso implantado;
    - Selecione Launch Studio (ou abra uma nova guia do navegador e navegue até **[Azure Machine Learning Studio](https://ml.azure.com)** usando sua conta da Microsoft).
    - Feche todas as mensagens exibidas;
      

1. No estúdio Azure Machine Learning, você deverá ver seu espaço de trabalho recém-criado. Caso contrário, selecione Todos os espaços de trabalho no menu à esquerda e selecione o espaço de trabalho que você acabou de criar.

</details>
<details>
<summary>Use aprendizado de máquina automatizado para treinar um modelo</summary>

1. No Azure Machine Learning Studio, veja a página Automated ML **(em Authoring)**.

1. Crie um novo trabalho de ML automatizado com as seguintes configurações, usando Next conforme necessário para avançar pela interface do usuário:

     ### Configurações básicas

   - Nome do trabalho: mslearn-bike-automl;
   - Novo nome do experimento: mslearn-bike-rental;
   - Descrição: Aprendizado de máquina automatizado para previsão de aluguel de bicicletas;
   - Marcadores: nenhum;

        
        #### Tipo de tarefa e dados:

        - Selecione o tipo de tarefa: Regressão;
        - Selecionar conjunto de dados: crie um novo conjunto de dados com as seguintes configurações:
      
 
        #### Tipo de dados:
     
        - Nome : aluguel de bicicletas;
        - Descrição : dados históricos de aluguel de bicicletas;
        - Tipo : Tabular;

        #### Fonte de dados:
     
        - Selecione "Dos arquivos da web";

        #### URL da Web:

        - URL da Web: https://aka.ms/bike-rentals;
        - Ignorar validação de dados: não selecionar;
          
        #### Configurações:

        - Formato de arquivo : Delimitado;
        - Delimitador : Vírgula;
        - Codificação : UTF-8;
        - Cabeçalhos de coluna : apenas o primeiro arquivo possui cabeçalhos;
        - Pular linhas : Nenhum;
        - O conjunto de dados contém dados multilinhas : não selecione;

        #### Esquema:

        - Incluir todas as colunas exceto Caminho;
        - Revise os tipos detectados automaticamente;

       #### Criar:

        - Selecione Criar. Após a criação do conjunto de dados, selecione o conjunto de dados de aluguel de 
        bicicletas para continuar a enviar o trabalho de ML automatizado;

       ##### Configurações de tarefas:
  
       - Tipo de tarefa: Regressão;
       - Conjunto de dados: aluguel de bicicletas;
       - Coluna de destino: Aluguéis (inteiro);

       ### Configurações adicionais:

       - Métrica primária: raiz do erro quadrático médio normalizado;
       - Explique o melhor modelo: Não selecionado;
       - Usar todos os modelos suportados: Desmarcado. Você restringirá o trabalho para tentar apenas alguns algoritmos 
       específicos;
       - Modelos permitidos: Selecione apenas **RandomForest** e **LightGBM** — normalmente você gostaria de tentar o máximo possível, mas 
       cada modelo adicionado aumenta o tempo necessário para executar o trabalho;

       #### Limites: Expanda a seção
    
       - Máximo de testes: 3;
       - Máximo de testes simultâneos: 3;
       - Máximo de nós: 3;
       - Limite de pontuação da métrica: 0,085 ( para que, se um modelo atingir uma pontuação da métrica de erro quadrático médio normalizado de 0,085 ou menos, o trabalho termina;
       - Tempo limite: 15;
       - Tempo limite de iteração: 15;
       - Habilitar rescisão antecipada: selecionado;

        #### Validação e teste:

       - Tipo de validação: divisão de validação de treinamento;
       - Porcentagem de dados de validação: 10;
       - Conjunto de dados de teste: Nenhum;

       #### Calcular:

      - Selecione o tipo de computação: sem servidor;
      - Tipo de máquina virtual: CPU;
      - Camada de máquina virtual: Dedicada;
      - Tamanho da máquina virtual: Standard_DS3_V2*;
      - Número de instâncias: 1;
      
1. Envie o trabalho para treinamento e espere concluir.


</details>
<details>
<summary>Avalie o melhor modelo</summary>

1. Na guia Visão geral do trabalho automatizado de aprendizado de máquina, observe o melhor resumo do modelo.
  
  ![imagem de status de modelo](https://github.com/dani-peixoto/mslearn-bike-automl/assets/3649843/81d7ad4e-7247-43f6-9c49-efbe6c4b9d24)
  
1. Selecione o texto em Nome do algoritmo do melhor modelo para visualizar seus detalhes.
1. Selecione a guia Métricas e selecione os gráficos residuais e predito_true se eles ainda não estiverem selecionados.
1. Revise os gráficos que mostram o desempenho do modelo. O gráfico de resíduos mostra os resíduos (as diferenças entre os valores previstos e reais) como um histograma. O gráfico predito_true compara os valores previstos com os valores verdadeiros.
</details>
<details>
<summary>Implantar e testar o modelo</summary>

1. Na guia melhor modelo treinado pelo seu trabalho automatizado de machine learning, selecione Implantar e use a opção de serviço Web para implantar o modelo com as seguintes configurações:
        Nome : prever-aluguéis;
        Descrição : Prever aluguel de bicicletas;
        Tipo de computação : Instância de Contêiner do Azure;
        Habilitar autenticação : selecionado;
1. Aguarde o início da implantação – isso pode levar alguns segundos. O status de implantação do endpoint de previsão de aluguel será indicado na parte principal da página como Running.
1. Aguarde até que o status da implantação mude para Succeeded. Isso pode levar de 5 a 10 minutos.
</details>
<details>
<summary>Testar o serviço implantado</summary>

Agora você pode testar seu serviço implantado.

1. No estúdio Azure Machine Learning, no menu esquerdo, selecione **Endpoints** e abra o ponto final em tempo real de previsão de aluguel.
1. Na página do endpoint em tempo real de previsão de aluguel, visualize a guia Teste.
1. No painel Dados de entrada para testar o endpoint, substitua o modelo **JSON** pelos seguintes dados de entrada:

                                             {
                                               "Inputs": { 
                                               "data": [
                                                 {
                                                   "day": 1,
                                                   "mnth": 1,   
                                                   "year": 2022,
                                                   "season": 2,
                                                   "holiday": 0,
                                                   "weekday": 1,
                                                   "workingday": 1,
                                                   "weathersit": 2, 
                                                   "temp": 0.3, 
                                                   "atemp": 0.3,
                                                   "hum": 0.3,
                                                   "windspeed": 0.3 
                                                 } 
                                               ]    
                                             },   
                                             "GlobalParameters": 1.0
                                           }

1. Clique no botão Testar.
1. Revise os resultados do teste, que incluem um número previsto de aluguéis com base nos recursos de entrada - semelhante a este:

                                           {
                                             "Results": [
                                               444.27799000000000
                                             ]
                                           }
</details>
<details>
<summary>Excluir</summary>

> O serviço web que você criou está hospedado em uma instância de contêiner do Azure. Se não pretender experimentá-lo ainda mais, deverá excluir para evitar cobrança desnecessária de recursos do Azure.

1. No Studio Azure Machine Learning, na guia Endpoints, selecione o ponto de extremidade de previsão de aluguel. Em seguida, selecione Excluir e confirme que deseja excluir o endpoint.
1. Excluir sua computação garante que sua assinatura não será cobrada por recursos de computação. No entanto, será cobrada uma pequena quantia pelo armazenamento de dados, desde que o espaço de trabalho do Azure Machine Learning exista na sua assinatura. Se tiver terminado de explorar o Azure Machine Learning, poderá eliminar o espaço de trabalho Azure Machine Learning e os recursos associados.

Para excluir seu espaço de trabalho:

1. No portal Azure, na página Grupos de recursos, abra o grupo de recursos que especificou ao criar o seu espaço de trabalho Azure Machine Learning.
1. Clique em Excluir grupo de recursos, digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione Excluir.
</details>

# Informações do repositório

[![Repo Card](https://github-readme-stats.vercel.app/api/pin/?username=dani-peixoto&repo=mslearn-bike-automl&bg_color=white&border_color=green&show_icons=true&icon_color=blue&title_color=green&text_color=green)](https://github.com/dani-peixoto/mslearn-bike-automl)

# Contato

![Gmail](https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white)
![LINKEDIN](https://img.shields.io/badge/linkedin-%230077B5.svg?style=for-the-badge&logo=linkedin&logoColor=white)
