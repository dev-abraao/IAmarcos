# Segmentação de clientes de supermercado

Repositório do projeto: [dev-abraao/IAmarcos](https://github.com/dev-abraao/IAmarcos).

Projeto acadêmico de Inteligência Artificial para comparar **K-Means, agrupamento hierárquico aglomerativo e DBSCAN** na identificação de perfis de gastos de clientes. O notebook é autocontido, executável no Google Colab e também em Jupyter local.

O notebook contém as análises, a implementação e avaliação dos modelos, os perfis dos segmentos, as propostas comerciais e a exportação. A primeira célula executa a preparação necessária (dependências, configurações e carregamento da base) sem seções explicativas. A apresentação da pesquisa e as instruções de execução ficam neste README.

**Autores:** Abraão Sousa de Andrade e Arthur Scortegagna de Almeida  
**Curso:** Ciência da Computação  
**Instituição:** Centro Universitário de Maceió — UNIMA AFYA  
**Local:** Maceió, Alagoas  
**E-mails:** abraao_andrade_sousa@hotmail.com e arthurscortegagna@gmail.com

## Pergunta de pesquisa e aplicação

> Em que medida modelos de agrupamento identificam segmentos de clientes com padrões de gastos distintos, capazes de orientar propostas de campanhas por categoria em um supermercado?

O resultado esperado é uma descrição dos segmentos encontrados, acompanhada de sugestões de campanhas para teste. Por exemplo, um grupo que concentra uma parcela relativamente maior dos gastos em hortifruti pode receber uma proposta de comunicação dessa categoria. Os perfis são identificados pelos experimentos, sem nomes ou quantidades de segmentos definidos previamente.

O trabalho demonstra preparação de dados, aprendizado não supervisionado, avaliação de estabilidade, visualização e comunicação de resultados comerciais. Não mede resposta a ofertas, aumento de vendas, lucratividade ou previsão de demanda futura.

O relatório anterior já explorou a base. Por isso, este estudo é exploratório: eventuais expectativas sobre os grupos não são hipóteses confirmatórias independentes.

## Arquivos

```text
segmentacao-supermercado/
├── segmentacao_clientes.ipynb    # Código, explicações e resultados executados
├── README.md                    # Documentação do projeto e dos algoritmos
├── requirements.txt             # Versões do ambiente analítico de referência
├── .gitignore
├── dados/
│   └── p45b.xlsx                 # Cópia da planilha fornecida
├── artigo/
│   ├── artigo.tex               # Artigo no formato IEEE
│   ├── artigo.pdf               # Versão compilada
│   └── figuras_artigo/          # Figuras utilizadas no artigo
└── resultados/
    ├── README.md                # Identificação da execução validada na entrega
    ├── execucao_<data_hora>/
    │   ├── configuracao.json
    │   ├── requirements-execucao.txt
    │   ├── resumo_resultados.md
    │   ├── tabelas/
    │   ├── figuras/              # PNG a 300 dpi e PDF
    │   └── modelos/              # Modelos ajustados e transformações
    └── execucao_<data_hora>.zip   # Exportação de cada execução
```

As pastas `.tools/` e `.venv/`, quando presentes na cópia local, servem somente à geração e à validação da entrega. Estão ignoradas pelo Git e excluídas do ZIP do projeto.

## Executar no Google Colab

1. Acesse [Google Colab](https://colab.research.google.com/).
2. Abra **Arquivo → Fazer upload de notebook** e selecione `segmentacao_clientes.ipynb`. Depois de publicar o repositório, também é possível abri-lo pela guia **GitHub** do Colab.
3. Use uma sessão de CPU; este projeto não precisa de GPU.
4. Execute as células em ordem, por **Ambiente de execução → Executar tudo**.
5. Quando a célula de carregamento solicitar, envie `dados/p45b.xlsx`. Também é possível enviar a planilha pelo painel lateral antes de executar.
6. Consulte as tabelas, os gráficos e as propostas comerciais no próprio notebook.
7. Ao final, baixe o ZIP indicado na saída. Você pode usar o painel lateral ou definir `BAIXAR_ZIP_NO_COLAB = True` na configuração e executar novamente a última célula.

O notebook usa as bibliotecas disponíveis e instala somente dependências ausentes. Para solicitar as versões do ambiente de referência, altere `FIXAR_VERSOES = True` em uma sessão nova, antes das importações. Se já houver bibliotecas importadas com outras versões, reinicie a sessão depois da instalação e execute novamente. As versões realmente usadas são sempre exportadas.

O armazenamento da sessão do Colab é temporário: baixe os resultados antes de encerrá-la. [Informações oficiais sobre o Colab](https://research.google.com/colaboratory/faq.html).

## Executar localmente

Use Python 3.12 para aproximar o ambiente de validação da entrega. Em um terminal na raiz do projeto:

```bash
python -m venv .venv
```

Ative o ambiente no Windows PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

Ou no Linux/macOS:

```bash
source .venv/bin/activate
```

Instale as dependências e abra o notebook:

```bash
python -m pip install -r requirements.txt jupyterlab
python -m jupyter lab segmentacao_clientes.ipynb
```

A planilha é procurada em `dados/p45b.xlsx`, `p45b.xlsx`, `../dados/p45b.xlsx` e `/content/p45b.xlsx`. Também é possível definir `ARQUIVO_DADOS` explicitamente. O programa não altera a planilha de entrada.

## Dataset e preparação

A base fornecida para a atividade tem **81 clientes e sete variáveis originais**. Segundo o relatório anterior, os gastos correspondem a um período agregado de três meses. A planilha não documenta datas individuais, moeda, método de amostragem nem se os registros são reais ou sintéticos.

| Variável | Conteúdo | Papel no projeto |
| --- | --- | --- |
| `cliente` | Identificador único | Rastrear os resultados; excluído do agrupamento |
| `hortifruti` | Gasto em hortifruti | Formação dos grupos |
| `carnes` | Gasto em carnes | Formação dos grupos |
| `laticinios` | Gasto em laticínios | Formação dos grupos |
| `canal` | `net`, `fone` | Descrever os grupos depois do ajuste |
| `pedidos` | `10menos`, `11a20`, `21mais` | Descrever os grupos; são faixas, não contagens exatas |
| `sexo` | `fem`, `masc` | Validado como coluna original; excluído da modelagem e das propostas |

O código verifica colunas, valores ausentes, identificadores repetidos, domínios das categorias, gastos finitos e não negativos e gasto total positivo. Inconsistências geram erro explícito; não há exclusão ou imputação silenciosa. A execução registra o SHA-256 da planilha para identificar a versão exata utilizada.

O gasto total é a soma das três categorias disponíveis; não representa necessariamente toda a compra do cliente no supermercado.

### Duas representações

**Gastos absolutos:** são usados os três gastos, com padronização `z = (x − média) / desvio-padrão` pelo `StandardScaler`. O gasto total não é incluído como uma quarta dimensão redundante. Essa representação considera volume e composição das compras.

**Participações:** cada gasto é dividido pelo gasto total das três categorias. As três parcelas somam 1, sem padronização adicional. Essa representação destaca a composição dos gastos e reduz a influência direta do valor total.

As participações são dependentes por construção. A distância euclidiana entre elas é uma escolha exploratória; o projeto não implementa análise composicional por log-razões. A média das participações individuais também não equivale à participação da categoria no gasto agregado de todos os clientes.

As duas representações respondem a perguntas diferentes. Não é correto escolher uma apenas porque sua silhueta é maior: a transformação muda a geometria das distâncias.

## Algoritmos implementados

### K-Means

Agrupa os clientes em `k` segmentos e procura minimizar a soma das distâncias quadráticas de cada observação ao centro do seu grupo. O centro representa a média das variáveis dos clientes daquele segmento.

- Implementação: `sklearn.cluster.KMeans`.
- Parâmetros: `k = 2, 3, 4, 5, 6`, inicialização `k-means++`, `n_init = 30` e semente 42.
- Motivo da inclusão: fornece uma referência simples e centros interpretáveis para descrever perfis de compra.
- Limitações: exige escolher `k`, tende a favorecer grupos compactos e pode ser influenciado por valores extremos e pela representação dos dados.
- Novos clientes: permite `predict`, desde que sejam usados os mesmos atributos e a transformação ajustada na amostra original.

[Documentação oficial do K-Means](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html).

### Agrupamento hierárquico aglomerativo

Começa com um grupo para cada cliente e une grupos progressivamente. O critério **Ward** escolhe uniões que minimizam o aumento da variância interna e utiliza distância euclidiana. Um dendrograma mostra a sequência das uniões.

- Implementação: `sklearn.cluster.AgglomerativeClustering`.
- Parâmetros: `n_clusters = 2, 3, 4, 5, 6`, `linkage = "ward"`.
- Motivo da inclusão: comparar os segmentos do K-Means com uma estratégia de formação diferente e visualizar a estrutura das uniões.
- Limitações: as uniões não são desfeitas; escala, valores extremos e critério de união afetam o resultado.
- Novos clientes: esta implementação não oferece `predict`; o modelo descreve os clientes usados no ajuste.

O dendrograma é calculado com `scipy.cluster.hierarchy.linkage`, também com Ward, e apresentado de forma truncada para facilitar a leitura.

[Documentação oficial do agrupamento aglomerativo](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.AgglomerativeClustering.html).

### DBSCAN

Forma grupos em regiões densas do espaço. `eps` define o raio da vizinhança e `min_samples` a quantidade mínima de observações nessa vizinhança, incluindo o próprio ponto, para um ponto central. O rótulo `-1` sinaliza ruído e não é interpretado como segmento comercial.

- Implementação: `sklearn.cluster.DBSCAN`.
- Parâmetros: `min_samples = 3, 5, 8`; distância euclidiana.
- Para cada `min_samples`, calculam-se as distâncias ao vizinho dessa posição, incluindo o próprio cliente. Os quantis 50%, 65%, 80%, 90% e 95%, multiplicados por 0,8, 1,0 e 1,2, geram os candidatos de `eps`. Valores nulos e candidatos repetidos são descartados.
- Motivo da inclusão: investigar agrupamentos por densidade e permitir que casos isolados fiquem sem segmento.
- Limitações: sensibilidade a `eps`, `min_samples`, escala e diferenças de densidade. Um resultado com poucos clientes agrupados pode apresentar boa silhueta e baixa utilidade comercial.
- Novos clientes: esta implementação não oferece `predict`.

Configurações que geram somente um grupo, todos os clientes como ruído ou cobertura insuficiente são registradas e não são forçadas a produzir uma segmentação elegível.

[Documentação oficial do DBSCAN](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.DBSCAN.html).

### Por que KNN não foi usado como agrupador?

O KNN de classificação atribui classes com base em exemplos rotulados. A base não contém segmentos previamente conhecidos. Busca por vizinhos pode apoiar uma ferramenta de clientes semelhantes, mas não substitui a descoberta dos segmentos proposta neste trabalho. Também não usamos classificadores para reproduzir os rótulos dos agrupamentos e apresentar essa concordância como validação independente.

[Documentação oficial de vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html).

## Avaliação e seleção

Não há uma classe correta conhecida para cada cliente. Portanto, **não são calculadas acurácia, precisão de classificação ou matriz de confusão**.

| Indicador | Como interpretar |
| --- | --- |
| Silhueta | Entre −1 e 1; maior indica melhor separação segundo a distância adotada |
| Davies–Bouldin | Menor sugere menor dispersão interna em relação à separação |
| Calinski–Harabasz | Maior sugere maior dispersão entre grupos em relação à interna |
| Cobertura | Fração dos clientes atribuídos a um grupo; exclui ruído |
| Menor grupo | Ajuda a identificar segmentos pequenos demais para interpretação |
| ARI de estabilidade | Concordância ajustada ao acaso entre partições; não é acurácia |

Para o DBSCAN, as três métricas internas são calculadas **apenas entre os clientes agrupados**. A quantidade de ruído e a cobertura são reportadas separadamente. A silhueta tende a favorecer certas geometrias e não deve ser o único critério.

Uma configuração é elegível se tiver ao menos dois grupos, pelo menos cinco clientes por grupo, cobertura mínima de 80% e silhueta definida. Os limites são escolhas deste projeto e podem ser alterados na célula de configuração; não são recomendações universais.

Entre as configurações elegíveis de **cada algoritmo em cada representação**, seleciona-se o maior valor de:

```text
criterio_selecao = cobertura × silhueta − (1 − cobertura)
```

Essa fórmula é uma **heurística explícita do projeto**, que atribui penalidade aos casos sem grupo. Não é uma métrica estabelecida da literatura nem uma estimativa de valor comercial. Os desempates consideram maior silhueta, menor Davies–Bouldin e menor número de grupos. As métricas originais continuam disponíveis.

Não há seleção automática de um vencedor global. A busca e a avaliação interna usam a mesma amostra; os valores selecionados podem ser otimistas. A ausência de uma configuração elegível é um resultado permitido.

### Estabilidade e sensibilidade

- **Estabilidade:** 30 subamostras de 80% sem reposição, com semente 42 e os mesmos índices para todos os métodos. Os parâmetros ficam fixos, mas o escalonamento é reajustado em cada subamostra. Para K-Means, as sementes também variam entre repetições.
- **ARI de todos:** compara os rótulos originais e reajustados dos mesmos clientes; ruído pode contar como um rótulo e inflar concordância.
- **ARI dos agrupados:** considera somente clientes que receberam grupo nas duas partições e exige pelo menos dois grupos em cada uma nesse conjunto comum. São exportados o número de comparações válidas e a cobertura comum.
- **Repetições elegíveis:** informa em quantas subamostras os limites de tamanho e cobertura continuam atendidos. Isso complementa o ARI.
- **Atípicos:** a regra de 1,5 IQR é aplicada às três categorias e ao gasto total. Todos os casos permanecem no ajuste principal. O ajuste sem os casos sinalizados é usado somente como controle de sensibilidade, sem nova busca de parâmetros.
- **Representação:** o ARI também compara as partições do mesmo algoritmo em gastos absolutos e participações.

A estabilidade é condicional às configurações selecionadas e não equivale a validação com novos clientes. Intervalos interquartis do ARI são descrições das repetições, não intervalos de confiança populacionais.

[Métricas e avaliação de agrupamento](https://scikit-learn.org/stable/modules/clustering.html#clustering-performance-evaluation).

## Resultados exportados

Cada execução cria uma pasta nova em `resultados/`, para não misturar arquivos de execuções anteriores.

| Arquivo em `tabelas/` | Conteúdo |
| --- | --- |
| `estatisticas_descritivas.csv` | Estatísticas dos gastos |
| `distribuicoes_cadastrais.csv` | Contagens e percentuais de canal, pedidos e sexo |
| `limites_iqr.csv` e `casos_atipicos_iqr.csv` | Diagnóstico de valores atípicos |
| `todas_configuracoes.csv` | Busca completa, incluindo resultados inelegíveis |
| `melhores_por_algoritmo.csv` | Configuração selecionada em cada combinação |
| `comparacao_com_estabilidade.csv` | Métricas principais e estabilidade |
| `estabilidade_repeticoes.csv` | Resultado de cada subamostra |
| `sensibilidade_atipicos.csv` | Mudanças após retirar temporariamente os atípicos |
| `concordancia_representacoes.csv` | Concordância entre as duas representações |
| `perfis_segmentos.csv` | Tamanhos, médias, medianas e composição dos segmentos |
| `canal_pedidos_por_segmento.csv` | Distribuições dessas características por grupo |
| `clientes_segmentados.csv` | Dados e rótulos das configurações selecionadas |
| `acoes_comerciais_propostas.csv` | Sugestões de campanhas ainda não testadas |

As figuras incluem distribuições dos gastos, curvas de vizinhança, silhueta por quantidade de grupos, dendrogramas, projeções PCA e composição dos segmentos. **PCA é somente visualização**: os agrupamentos são ajustados nas três dimensões originais de cada representação.

`configuracao.json` registra parâmetros, semente, tamanho da amostra, hash do dataset e versões. `requirements-execucao.txt` registra as dependências analíticas diretas utilizadas; `requirements.txt` define o ambiente de referência. A pasta `modelos/` contém modelos e transformações. Arquivos `.joblib` devem ser carregados somente de origem confiável e com bibliotecas compatíveis.

## Resultados da execução incluída

O notebook foi executado integralmente com os 81 clientes e as versões de `requirements.txt`. Foram avaliadas **110 configurações**. A validação também verificou entradas inválidas, partições degeneradas, ruído e integridade das exportações. Consulte a execução identificada em [resultados/README.md](resultados/README.md).

Na representação por participações, as configurações selecionadas foram:

| Algoritmo | Grupos | Cobertura | Silhueta | ARI mediano nas subamostras |
| --- | ---: | ---: | ---: | ---: |
| K-Means | 2 | 100,0% | 0,611 | 1,000 |
| Hierárquico | 2 | 100,0% | 0,594 | 0,906 |
| DBSCAN | 2 | 97,5% | 0,599 | 0,896 |

No K-Means em participações, os 35 clientes do grupo 0 tiveram participação média de 38,81% em hortifruti, 34,20% em carnes e 26,99% em laticínios. Os 46 clientes do grupo 1 tiveram, respectivamente, 69,88%, 17,06% e 13,05%. São descrições desta amostra, sem identificação de hábitos futuros ou benefícios de campanha.

O DBSCAN em gastos absolutos foi elegível no ajuste completo, mas somente **1 das 30 subamostras** continuou atendendo aos limites do protocolo. Essa fragilidade precisa ser discutida: selecionar uma configuração no conjunto completo não garante estabilidade. O ARI mediano 1,000 do K-Means também não significa acurácia de 100%: resume concordância nas subamostras sob este protocolo.

## Como interpretar as propostas de campanha

O exemplo comercial pré-definido usa K-Means em participações e destaca, em cada grupo, a categoria com maior diferença positiva de participação média em relação à amostra. Essa escolha facilita a demonstração e **não declara K-Means vencedor**. A referência pode ser alterada em `REFERENCIA_COMERCIAL`.

Uma categoria com maior participação observada sugere um tema de comunicação a investigar. Não comprova preferência permanente ou resposta a desconto. Para avaliar uma campanha real, seria necessário um teste aleatório com grupo de comparação e medidas de conversão e margem incremental após os descontos.

Os números dos segmentos são arbitrários. “Grupo 0” de um modelo não necessariamente corresponde ao “Grupo 0” de outro. Gastos em categorias também não autorizam inferências sobre dieta ou condições pessoais.

## Limitações e uso no artigo

- A amostra tem apenas 81 clientes e origem amostral desconhecida; não demonstra representatividade do mercado.
- Há somente três categorias agregadas e faixas de pedidos. Faltam produtos, datas, contagens exatas, preços, margens e histórico de campanhas.
- Não há rótulos externos para validar se os segmentos correspondem a grupos reais e persistentes.
- Transformações, parâmetros e observações extremas podem mudar a segmentação.
- Métricas internas e estabilidade não comprovam retorno comercial nem causalidade.
- As duas representações têm geometrias diferentes; índices não devem ser comparados isoladamente entre elas.

Para o artigo IEEE, aproveite a pergunta de pesquisa, o dicionário de dados, as justificativas dos algoritmos, o protocolo documentado, `comparacao_com_estabilidade.csv`, os perfis e as figuras. Descreva os resultados efetivamente gerados e diferencie propostas de aplicação de benefícios medidos.

## Artigo IEEE

O texto está em [artigo/artigo.tex](artigo/artigo.tex) e a versão compilada em [artigo/artigo.pdf](artigo/artigo.pdf). O artigo apresenta a pergunta de pesquisa, a justificativa dos três modelos, o dataset, as representações, os experimentos, as tabelas de resultados e estabilidade, as propostas comerciais e as limitações. Os dados numéricos correspondem à execução identificada em `resultados/README.md`.

Para editar no Overleaf, importe o pacote `artigo_overleaf.zip` fornecido na entrega ou envie `artigo.tex` com a pasta `figuras_artigo/` e a classe `IEEEtran.cls`. Defina `artigo.tex` como documento principal. As referências estão no próprio `.tex`, sem arquivo BibTeX separado.

## Publicar no GitHub

Envie o notebook, este README, `requirements.txt`, `.gitignore`, as pastas `dados/` e `artigo/` e os resultados que acompanham a entrega ao repositório [dev-abraao/IAmarcos](https://github.com/dev-abraao/IAmarcos). O notebook pode ser visualizado no GitHub e aberto pelo Colab. O código não publica arquivos automaticamente.

O botão opcional do Colab pode ser incluído no README com o endereço abaixo. Ajuste `main` se o notebook estiver em outra branch:

```markdown
[![Abrir no Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/dev-abraao/IAmarcos/blob/main/segmentacao_clientes.ipynb)
```
