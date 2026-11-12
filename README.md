# Segmentação de clientes de supermercado com IA

[![Abrir no Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/dev-abraao/IAmarcos/blob/main/segmentacao_clientes.ipynb)


 [Pesquisa acadêmica de Inteligência Artificial](https://www.overleaf.com/project/6af5108043d52d3928a81eae/share#bfdf8c66d86b8e6522707e9634931ae63501cb73fcbad276) que compara **K-Means, agrupamento hierárquico aglomerativo e DBSCAN** para identificar perfis de clientes a partir de seus gastos em hortifruti, carnes e laticínios. O objetivo é produzir segmentos interpretáveis e propor campanhas por categoria para um supermercado.

**Autores:** Abraão Sousa de Andrade e Arthur Scortegagna de Almeida  
**Curso:** Ciência da Computação  
**Instituição:** Centro Universitário de Maceió — UNIMA AFYA  
**Local:** Maceió, Alagoas  
**Contato:** abraao_andrade_sousa@hotmail.com · arthurscortegagna@gmail.com

## Pergunta de pesquisa

> Em que medida modelos de agrupamento identificam segmentos de clientes com padrões de gastos distintos, capazes de orientar propostas de campanhas por categoria em um supermercado?

O estudo é exploratório e utiliza aprendizado não supervisionado: os clientes não possuem segmentos previamente definidos. Comparamos os grupos encontrados quanto à separação, cobertura e estabilidade. Depois, descrevemos seus padrões de compra e formulamos propostas comerciais para teste.

## Estrutura do repositório

```text
IAmarcos/
├── README.md
├── segmentacao_clientes.ipynb
└── p45b.xlsx
```

- [segmentacao_clientes.ipynb](segmentacao_clientes.ipynb): notebook com análises, implementação e avaliação dos modelos, perfis dos segmentos, propostas e exportação dos resultados.
- [p45b.xlsx](p45b.xlsx): dataset utilizado nos experimentos.

A preparação do ambiente e o carregamento dos dados são executados na primeira célula de código do notebook. As seções seguintes apresentam as análises e os modelos. As pastas e os ZIPs de resultados são criados durante a execução; não fazem parte da estrutura versionada acima.

## Como executar no Google Colab

1. Baixe o arquivo [p45b.xlsx](p45b.xlsx) deste repositório. Na página do arquivo no GitHub, use a opção de baixar o arquivo original.
2. Clique no botão **Abrir no Colab** no início deste README. Também é possível abrir o [notebook no GitHub](segmentacao_clientes.ipynb) e usar o botão do Colab que aparece nele.
3. Use uma sessão com **CPU**. Não é necessário habilitar GPU.
4. No Colab, escolha **Ambiente de execução → Executar tudo**.
5. Quando aparecer **“Envie o arquivo p45b.xlsx”**, selecione a planilha baixada no primeiro passo. Mantenha esse nome de arquivo.
6. Aguarde a execução das células e consulte as tabelas, os gráficos e as propostas comerciais no notebook.
7. Ao final, baixe o ZIP de resultados pelo painel de arquivos à esquerda, no caminho informado pela última seção.

**Abrir o notebook pelo GitHub não envia a planilha para a sessão do Colab.** É necessário fazer o upload quando solicitado. Como alternativa, envie `p45b.xlsx` pelo painel de arquivos do Colab antes de executar as células; o notebook encontrará o arquivo automaticamente.

Se preferir enviar o próprio notebook, baixe `segmentacao_clientes.ipynb`, acesse [Google Colab](https://colab.research.google.com/) e abra **Arquivo → Fazer upload de notebook**. Depois, siga os mesmos passos de execução e envio do dataset.

### Dependências e configurações

O notebook usa as bibliotecas disponíveis na sessão e instala automaticamente as que estiverem ausentes. Não é necessário um arquivo `requirements.txt` para a execução no Colab. Entre as bibliotecas utilizadas estão NumPy, pandas, SciPy, scikit-learn, Matplotlib, Seaborn e openpyxl.

As opções abaixo ficam na primeira célula de código:

| Variável | Padrão | Função |
| --- | --- | --- |
| `ARQUIVO_DADOS` | `None` | Procura a planilha nos caminhos previstos ou solicita upload no Colab. Pode receber um caminho explícito. |
| `FIXAR_VERSOES` | `False` | Usa as dependências já disponíveis. Com `True`, solicita as versões de referência definidas no código. |
| `BAIXAR_ZIP_NO_COLAB` | `False` | Gera o ZIP sem iniciar o download. Com `True`, a última célula inicia o download automaticamente. |

Para ativar o download automático, altere `BAIXAR_ZIP_NO_COLAB = True` antes de executar todas as células. Para usar `FIXAR_VERSOES = True`, prefira uma sessão nova; se houver bibliotecas já importadas com outras versões, reinicie a sessão após a instalação e execute novamente. As versões efetivamente utilizadas são registradas nos resultados.

O armazenamento do Colab é temporário. Baixe os resultados antes de encerrar a sessão. Se ela for reiniciada, poderá ser necessário enviar a planilha novamente. [Informações oficiais sobre o Colab](https://research.google.com/colaboratory/faq.html).

### Execução local opcional

Também é possível executar em Jupyter local, mantendo a planilha e o notebook na mesma pasta. Recomenda-se Python 3.12 para aproximar o ambiente de referência.

No Windows PowerShell, dentro da pasta do repositório:

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install jupyterlab
.\.venv\Scripts\python.exe -m jupyter lab segmentacao_clientes.ipynb
```

Selecione o kernel do ambiente criado e execute as células em ordem. A primeira célula instala as dependências analíticas ausentes e encontra `p45b.xlsx` na pasta atual. A execução local gera os mesmos tipos de arquivos de saída.

## Dataset

A planilha contém **81 clientes e sete variáveis originais**, sem valores ausentes ou identificadores repetidos na base utilizada. Segundo o relatório anterior da pesquisa, os gastos agregam três meses. A planilha não documenta a moeda, a estratégia de amostragem ou se os registros são reais ou sintéticos.

| Variável | Conteúdo | Utilização |
| --- | --- | --- |
| `cliente` | Identificador do cliente | Rastrear os resultados; não entra no agrupamento |
| `hortifruti` | Gasto em hortifruti | Formação dos grupos |
| `carnes` | Gasto em carnes | Formação dos grupos |
| `laticinios` | Gasto em laticínios | Formação dos grupos |
| `canal` | Internet (`net`) ou telefone (`fone`) | Descrever os grupos após o ajuste |
| `pedidos` | `10menos`, `11a20` ou `21mais` | Descrever os grupos; são faixas de pedidos |
| `sexo` | `fem` ou `masc` | Conferido na validação da base; não é utilizado nos modelos ou nas propostas |

O código valida as colunas, as categorias, a unicidade dos clientes e a consistência dos gastos. Todos os registros são mantidos na análise principal. O gasto total calculado corresponde apenas à soma das três categorias disponíveis.

## Modelos e experimentos

Os três modelos são aplicados a duas representações dos mesmos clientes:

- **Gastos absolutos:** os valores de hortifruti, carnes e laticínios são padronizados com `StandardScaler`. Essa representação considera volume e composição das compras.
- **Participações:** cada gasto é dividido pela soma das três categorias do cliente. Essa representação enfatiza a distribuição do orçamento. Por exemplo, um cliente pode ter 70% em hortifruti, 17% em carnes e 13% em laticínios.

| Modelo | Funcionamento e justificativa | Configurações testadas |
| --- | --- | --- |
| **K-Means** | Forma grupos em torno de centros e permite resumir os perfis pelas médias das variáveis. É a referência inicial pela facilidade de interpretação. | De 2 a 6 grupos, inicialização `k-means++`, 30 inicializações e semente 42 |
| **Hierárquico aglomerativo** | Une progressivamente os grupos pelo critério Ward. Permite comparar outra estratégia de formação e visualizar as uniões em um dendrograma. | De 2 a 6 grupos, critério `ward` |
| **DBSCAN** | Agrupa regiões densas e pode deixar clientes sem grupo, identificados pelo rótulo `-1`. Permite investigar perfis isolados. | `min_samples` de 3, 5 e 8; candidatos de `eps` definidos por distâncias de vizinhança |

Para o DBSCAN, os candidatos de `eps` usam os quantis 50%, 65%, 80%, 90% e 95% das distâncias ao vizinho correspondente a `min_samples`, incluindo o próprio cliente. Esses valores são multiplicados por 0,8, 1,0 e 1,2. No dataset fornecido, são avaliadas **110 configurações** no total.

Os gráficos de análise de componentes principais, ou PCA, servem apenas para visualização. Os modelos são ajustados nas três dimensões de cada representação.

### Avaliação dos grupos

| Indicador | Interpretação |
| --- | --- |
| **Silhueta** | Mede a separação dos grupos; valores maiores, até 1, indicam melhor separação segundo a distância adotada. |
| **Davies–Bouldin** | Compara dispersão interna e separação; valores menores são preferíveis. |
| **Calinski–Harabasz** | Relaciona dispersão entre grupos e dentro deles; valores maiores são preferíveis. |
| **Cobertura** | Fração dos clientes atribuídos a grupos. No DBSCAN, exclui os casos classificados como ruído. |
| **ARI de estabilidade** | Mede concordância entre a divisão original e as divisões obtidas em subamostras, ajustada ao acaso. |

Uma configuração precisa apresentar pelo menos dois grupos, cinco clientes ou mais em cada grupo, cobertura mínima de 80% e silhueta definida. Entre as configurações elegíveis de cada algoritmo e representação, a seleção maximiza:

```text
criterio_selecao = cobertura × silhueta − (1 − cobertura)
```

Essa expressão é uma heurística do projeto para penalizar clientes sem grupo. Os desempates usam maior silhueta, menor Davies–Bouldin e menor quantidade de grupos. Para o DBSCAN, as métricas internas excluem ruído e precisam ser interpretadas junto da cobertura.

A estabilidade é avaliada em **30 subamostras sem reposição**, com fração nominal de 80%, resultando em 64 clientes por repetição. Os parâmetros selecionados permanecem fixos e a padronização é reajustada em cada subamostra. São reportados ARI mediano, quartis, cobertura comum e fração de repetições elegíveis.

O notebook também examina a retirada temporária de quatro casos sinalizados pela regra do intervalo interquartil e compara as partições entre representações. Os casos continuam presentes na análise principal.

## Principais resultados

Os resultados registrados no notebook para a representação por participações foram:

| Modelo | Grupos | Cobertura | Silhueta | ARI mediano de estabilidade |
| --- | ---: | ---: | ---: | ---: |
| K-Means | 2 | 100,0% | 0,611 | 1,000 |
| Hierárquico | 2 | 100,0% | 0,594 | 0,906 |
| DBSCAN | 2 | 97,5% | 0,599 | 0,896 |

No K-Means em participações, os perfis encontrados foram:

| Característica | Grupo 0 | Grupo 1 |
| --- | ---: | ---: |
| Clientes | 35 | 46 |
| Participação média de hortifruti | 38,81% | 69,88% |
| Participação média de carnes | 34,20% | 17,06% |
| Participação média de laticínios | 26,99% | 13,05% |

O grupo 0 distribui seus gastos de maneira mais equilibrada, enquanto o grupo 1 concentra uma parcela maior em hortifruti. Isso permite propor testes de comunicação de ofertas por categoria. As participações apresentadas são médias dos percentuais individuais dos clientes.

**ARI mediano de 1,000 indica elevada estabilidade sob o protocolo; não significa acurácia de 100% nem que todas as repetições foram idênticas.** Não existem rótulos corretos previamente conhecidos para validar os segmentos.

Nos gastos absolutos, o DBSCAN manteve os critérios de elegibilidade em apenas uma das 30 subamostras, mostrando sensibilidade às condições desse experimento. A seleção considera conjuntamente separação, cobertura, estabilidade e interpretação dos perfis.

## Arquivos gerados durante a execução

O notebook cria uma pasta `resultados/execucao_<data_hora>/` e um ZIP correspondente **na sessão em que está sendo executado**. Essas saídas não precisam estar previamente no repositório.

As saídas incluem:

- **Tabelas CSV:** estatísticas descritivas, todas as configurações testadas, comparação dos modelos, estabilidade, sensibilidade, perfis e propostas comerciais.
- **Figuras PNG e PDF:** distribuições dos gastos, silhueta por quantidade de grupos, dendrogramas, projeções PCA e composição dos segmentos.
- **Modelos ajustados:** arquivos `.joblib` com os modelos e as transformações utilizadas.
- **Reprodutibilidade:** `configuracao.json`, versões das dependências, hash da planilha e `resumo_resultados.md`.

Para conferir os resultados principais após executar, procure `comparacao_com_estabilidade.csv`, `perfis_segmentos.csv`, `canal_pedidos_por_segmento.csv` e `acoes_comerciais_propostas.csv` na subpasta `tabelas/` gerada.

## Aplicação e limitações

O estudo oferece uma demonstração de segmentação para apoiar o planejamento de campanhas no supermercado. As propostas comerciais ainda precisam ser avaliadas em um teste com grupo de comparação e medidas de conversão e margem incremental.

A amostra reduzida e de origem desconhecida, as três categorias agregadas e a ausência de histórico de campanhas limitam as conclusões. Não são medidos aumento de vendas, lucratividade, causalidade ou demanda futura. As participações somam 1 e são dependentes por construção; sua distância euclidiana é uma escolha exploratória. As métricas são internas, e a busca de configurações e a avaliação usam a mesma base.

## Referências técnicas

- [K-Means — scikit-learn](https://scikit-learn.org/1.6/modules/generated/sklearn.cluster.KMeans.html)
- [Agrupamento hierárquico — scikit-learn](https://scikit-learn.org/1.6/modules/generated/sklearn.cluster.AgglomerativeClustering.html)
- [DBSCAN — scikit-learn](https://scikit-learn.org/1.6/modules/generated/sklearn.cluster.DBSCAN.html)
- [Avaliação de agrupamentos — scikit-learn](https://scikit-learn.org/1.6/modules/clustering.html#clustering-performance-evaluation)
- [ARI — scikit-learn](https://scikit-learn.org/1.6/modules/generated/sklearn.metrics.adjusted_rand_score.html)
