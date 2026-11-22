# Análise do grafo congress-Twitter (SNAP) — Teoria dos Grafos

Trabalho prático **"TP — Analisando Grafos do SNAP"** (Instituto de Computação, UFBA).
O grafo analisado é o **G24, congress-Twitter**, atribuído pela regra da matrícula definida no enunciado.

O grafo representa a **influência no Twitter entre 475 membros do Congresso dos EUA** (117º Congresso, 2021–2023):
uma aresta dirigida `u → v` indica que `u` influencia `v`, e o peso é a probabilidade estimada dessa influência.

## Estrutura do repositório

```
.
├── dados/
│   └── congress_network/          dados originais, sem alteração
│       ├── congress.edgelist      lista de arestas (dirigida e ponderada)
│       ├── congress_network_data.json
│       └── README.txt             descrição original do dataset
├── resultados/                    arquivos gerados pelos notebooks
│   ├── estrutura_basica.csv
│   ├── tempos_algoritmos.csv
│   ├── stats_algoritmos.csv
│   ├── analise_estrutural.csv
│   └── grafos_manipulados.csv
├── estrutura_basica.ipynb         Parte I: análise estrutural obrigatória
├── algoritmos.ipynb               Parte II: algoritmos da disciplina e tempos de execução
├── analise_estrutural.ipynb       Parte III: small-world, lei de potência e robustez
└── README.md
```

## Como executar

Requisitos: **Python 3.11** e um editor com suporte a Jupyter (por exemplo, VS Code com a extensão Jupyter).

1. Crie e ative um ambiente virtual na pasta do projeto.

   Windows (PowerShell):
   ```powershell
   py -3.11 -m venv .venv
   .\.venv\Scripts\Activate.ps1
   ```

   Linux / macOS:
   ```bash
   python3.11 -m venv .venv
   source .venv/bin/activate
   ```

2. Instale as dependências (versões usadas no trabalho):
   ```bash
   pip install networkx==3.6.1 numpy==2.4.6 scipy==1.17.1 matplotlib==3.11.2 ipykernel
   ```

3. Abra os notebooks, selecione o kernel do `.venv` e execute cada um do início ao fim (**Run All**), nesta ordem:
   1. `estrutura_basica.ipynb`
   2. `algoritmos.ipynb`
   3. `analise_estrutural.ipynb`

   A última seção de cada notebook grava os resultados na pasta `resultados/`.
   No `algoritmos.ipynb`, cada algoritmo pode ser executado sozinho depois de rodar **Carregar o grafo** e **Medição de tempo**.

Tempo aproximado de execução: alguns segundos para `estrutura_basica`, menos de 1 minuto para `algoritmos`
e alguns minutos para `analise_estrutural`, dependendo do computador.
Os tempos medidos na Parte II variam de máquina para máquina.

## O que cada notebook faz

### `estrutura_basica.ipynb` — Parte I
Número de vértices e arestas, grau mínimo, máximo e médio, distribuição de graus, densidade,
componentes conexas e seus tamanhos, diâmetro, raio, comprimento médio dos caminhos,
coeficiente de clusterização médio, número de triângulos e visualização do grafo.
Saída: `resultados/estrutura_basica.csv`.

### `algoritmos.ipynb` — Parte II
BFS, DFS, verificação de eulerianidade, Dijkstra, Bellman-Ford, Floyd-Warshall, Tarjan,
e árvore geradora mínima (Kruskal e Prim), usando as implementações do NetworkX.
Cada algoritmo é executado 30 vezes; são calculados média, desvio padrão e intervalo de confiança de 95%.
Saídas: `resultados/tempos_algoritmos.csv` (tempos brutos) e `resultados/stats_algoritmos.csv` (estatísticas).

### `analise_estrutural.ipynb` — Parte III
- **Small-world:** comparação do comprimento médio e da clusterização com 30 grafos aleatórios equivalentes (mesmo número de vértices e arestas) e cálculo do índice σ.
- **Lei de potência:** distribuição de graus, CCDF em escala log-log e ajuste de reta.
- **Robustez:** remoção aleatória de 5% dos vértices (30 repetições) e remoção dos 5% mais centrais por intermediação (*betweenness*).

Saídas: `resultados/analise_estrutural.csv` (resultados) e `resultados/grafos_manipulados.csv`
(métricas de cada grafo após as remoções, com os vértices removidos).

## Tratamento dos dados

- O grafo original é **dirigido e ponderado**, sem auto-loops nem arestas múltiplas.
- **Versão não dirigida:** usada onde a direção não se aplica ou não é necessária
  (clusterização, triângulos, small-world, lei de potência e árvore geradora mínima).
  Quando existem `u → v` e `v → u`, elas viram uma única aresta.
- **Componentes:** o grafo tem 1 componente fracamente conexa (475 vértices) e 7 fortemente conexas
  (uma com 469 e seis com 1 vértice). Diâmetro, raio e comprimento médio são infinitos no grafo dirigido inteiro;
  por isso também são calculados na maior componente forte (dirigido) e na componente fraca (não dirigido).
- **Pesos nos caminhos mínimos (Parte II):** o peso é uma probabilidade, então o custo de cada aresta é
  `−log(probabilidade)`; maior influência corresponde a menor custo. Na versão não dirigida, quando há as duas
  direções, usa-se a maior das duas probabilidades.
- **Robustez:** feita no grafo dirigido, com componentes fortemente conexas.

## Fonte dos dados

Fink et al. (2023). *A Congressional Twitter network dataset quantifying pairwise probability of influence*. Data in Brief.
Disponível no Stanford Network Analysis Project (SNAP): https://snap.stanford.edu/data/congress-twitter.html
