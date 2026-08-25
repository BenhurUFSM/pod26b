## Ordenação de dados em memória externa

Nos algoritmos de ordenação que vimos anteriormente, os dados a ordenar estavam todos em um vetor, na memória principal.
O tempo de acesso a qualquer dos dados era considerado igual.

Existem casos em que o volume de dados a ordenar é tal que não é possível colocá-los todos ao mesmo tempo na memória principal, impedindo o uso desses algoritmos.
Nesses casos, precisamos de outros algoritmos, que levem em consideração que há uma diferença potencialmente enorme entre o custo de se acessar um dado que já foi lido da memória externa e está na memória interna e o custo de acesso a um dado que está somente em memória externa.
Um algoritmo de ordenação externa deve minimizar o número de vezes que um dado é lido ou escrito no dispositivo de memória externa.

Em geral, a ordenação é realizada em duas etapas. Na primeira etapa (classificação), várias partes dos dados são lidas para a memória interna, ordenadas e devolvidas para a memória externa. Na segunda etapa (intercalação), as partes ordenadas são intercaladas (merge) até se produzir um arquivo contendo todos os dados, ordenados.

### Etapa de Classificação

Considera-se que o arquivo de entrada tem $N$ registros, e a memória interna tem capacidade para ordenar $M$ registros por vez.

#### Classificação interna

Lê cada subconjunto de dados do arquivo de entrada que cabe na memória principal ($M$ registros), ordena esse subconjunto com um algoritmo de ordenação interna e grava em um subarquivo de saída. Quando terminar essa fase, teremos $k$ arquivos com $M$ registros ordenados ($k = N/M$), mais um arquivo com $N%M$ registros ordenados, se a divisão $N/M$ não for exata.

Essa forma de classificar os dados não leva em consideração a possível ordem inicial em que os dados se encontram. Caso os dados já estejam parcialmente ordenados, pode ter uma forma de realizar a classificação com maior desempenho.

Abaixo está um exemplo, com 17 registros de entrada e capacidade para 3 registros na memória.
```
   entrada 29 44 73 80 24 71 56 40 70 2 42 41 6 26 96 5 43
   saída1  29 44 73
   saída2  24 71 80
   saída3  40 56 70
   saída4   2 41 42
   saída5   6 26 76
   saída6   5 43
```
#### Classificação usando seleção por substituição

Nessa forma de classificação dos dados, o objetivo é gerar arquivos intermediários maiores que a classificação interna, retirando os dados ordenados da memória para o arquivo de saída, e substituindo cada dado por outro da entrada.

O algoritmo é assim:
1. Lê $M$ registros do arquivo de entrada para a memória, marca todos como disponíveis
2. Encontra o menor entre os registros disponíveis na memória e grava no arquivo de saída
3. Lê um registro da entrada para substituir o registro gravado
4. Se esse registro lido deve estar antes do último registro gravado, marca o registro lido como indisponível
5. Se houver algum registro disponível, continua no passo 2
6. Finaliza o arquivo de saída
6. So houver registros indisponíveis, marca como disponíveis, inicializa um novo arquivo de saída e continua no passo 2

Exemplo, com a mesma entrada e capacidade de memória do exemplo anterior.
```
   entrada 29 44 73 80 24 71 56 40 70 2 42 41 6 26 96 5 43
   memória 29 44 73 | 80 44 73 | 80 24* 73 | 80 24* 71*  | 56* 24* 71*
   saída1  29       | 29 44    | 29 44 73  | 29 44 73 80 |

   memória 56 24 71 | 56 40 71 | 56 70 71 | 2* 70 71    | 2* 42* 71      | 2* 42* 41*
   saída2  24       | 24 40    | 24 40 56 | 24 40 56 70 | 24 40 56 70 71 |

   memória 2 42 41 | 6 42 41 | 26 42 41 | 96 42 41  | 96 42 5*     | 96 43 5*
   saída3  2       | 2 6     | 2 6 26   | 2 6 26 41 | 2 6 26 41 42 | 2 6 26 41 42 43 96

   memória 5
   saída4  5
```
#### Classificação usando seleção natural

Na seleção por substituição, os registros indisponíveis ocupam a memória principal, reduzindo o número de registros disponíveis. Uma forma alternativa é gravar esses registros em um arquivo teemporário, com o mesmo tamanho da memória principal. Quando esse arquivo enche, para-se de ler os arquivos de entrada, grava-se os registros em memória no arquivo de saída (em ordem), preenche-se a memória com o conteúdo do arquivo temporário e inicia-se um novo arquivo de saída. O exemplo anterior ficaria assim:
```
   entrada 29 44 73 80 24 71 56 40 70 2 42 41 6 26 96 5 43
   memória 29 44 73 | 80 44 73 | 80 71 73 | 80 -- 73
   temp             |          | 24       | 24 56 40
   saída1  29       | 29 44    | 29 44 71 | 29 44 71 73 80
  
   memória 24 56 40 | 70 56 40 | 70 56 42 | 70 56 --
   temp             |          | 2        | 2 41 6
   saída2  24       | 24 40    | 24 40 42 | 24 40 42 56 70

   memória 2 41 6 | 26 41 6 | 26 41 96 | 43 41 96
   temp           |         |          | 5
   saída3  2      | 2 6     | 2 6 26   | 2 6 26 41 43 96

   memória 5
   temp
   saída4  5
```

### Etapa de intercalação

A etapa de classificação produziu uma coleção de $k$ partições pré-ordenados, que devem ser intercaladas para produzir o arquivo final contendo todos os dados ordenados.

#### Intercalação de $k$ vias

Define-se $k$ buffers de entrada, um para cada partição pré-ordenada, e um buffer de saída. A cada passo, identifica-se o menor registro entre os primeiros registros de cada buffer de entrada, retira-se esse registro desse buffer e grava-se esse registro no buffer de saída.
Deve-se tomar o cuidado de ler a continuação de cada partição quando o buffer correspondente esvaziar, de gravar o buffer de saída para o arquivo de saída quando encher, e de saber quais buffers de entrada ainda contêm dados.

A tarefa de encontrar o menor registro a cada passo pode ser realizada por uma heap, o que pode compensar para um $k$ não muito pequeno.

Dependendo dos recursos de hardware e software, pode ser necessário o merge em mais de uma passagem, intercalando menos de $k$ partições a cada vez.

#### Intercalação balanceada de $n$ vias

Usa-se $n$ arquivos de entrada e $n$ arquivos de saída.
Escreve-se as partições nos arquivos de entrada, de forma intercalada (a primeira partição no primeiro arquivo, a segunda no segundo, a $n+1$-ésima no primeiro arquivo, etc).
Essa gravação pode ser a saída da fase de classificação.

A intercalação balanceada é realizada lendo-se registros dos $n$ primeiros arquivos, e gravando-se o menor deles em um dos arquivos de saída, até o fim das $n$ primeiras partições (uma de cada arquivo). A intercalação do segundo lote de partições de entrada é gravada no segundo arquivo e assim sucessivamente, até terminarem os arquivos de entrada.
Então inverte-se: os arquivos de saída são usados como entrada e os de entrada como saída, e procede-se a uma nova fase. Fica-se alternando até que só resta um arquivo de saída.

Usando os dados do primeiro exemplo acima, e 2 vias (2 arquivos de entrada e 2 de saída):
```
   FASE 1 - partições 1, 3 e 5 na entrada1 e 2, 4 e 6 na entrada2
   entrada1  29 44 73 | 40 56 70 | 6 26 76   saída1  --
   entrada2  24 71 80 | 2 41 42 | 5 43       saída2  --
   partições 1 e 2 intercaladas na saída1
   entrada1  40 56 70 | 6 26 76   saída1 24 29 44 71 73 80
   entrada2  2 41 42 | 5 43       saída2
   partições 3 e 4 intercaladas na saída2
   entrada1  6 26 76   saída1 24 29 44 71 73 80
   entrada2  5 43      saída2 2 40 41 42 56 70
   partições 5 e 6 intercaladas na saída1
   entrada1  --        saída1 24 29 44 71 73 80 | 5 6 26 43 76
   entrada2  --        saída2 2 40 41 42 56 70
   FASE 2 - inverte E/S
   saída1  --        entrada1 24 29 44 71 73 80 | 5 6 26 43 76
   saída2  --        entrada2 2 40 41 42 56 70
   partições 12 e 56 intercaladas na saída1
   saída1  2 24 29 40 41 42 44 56 70 71 73 80    entrada1 5 6 26 43 76
   saída2  --                                    entrada2 --
   partição 34 na saída2
   saída1  2 24 29 40 41 42 44 56 70 71 73 80    entrada1 --
   saída2  5 6 26 43 76                          entrada2 --
   FASE 3 - inverte E/S, intercala partições 1256 e 34 na saída1
   entrada1 --       saída1  2 5 6 24 26 29 40 41 42 43 44 56 70 71 73 76 80
   entrada2 --       saída2  --
```
   entrada1 29 44 73 | 40 56 70 | 6 26 76 | V | V
   entrada2 24 71 80 | 2 41 42 | 5 46
   saída

1 1 0 | 0 2 1 | 2 0 3 | 5 3 0 |

   saída1  29 44 73
   saída2  24 71 80
   saída3  40 56 70
   saída4   2 41 42
   saída5   6 26 76
   saída6   5 43


#### Intercalação polifásica

Usa só um arquivo de saída, os demais são de entrada.
Intercala uma partição de cada arquivo de entrada, produzindo uma partição no arquivo de saída. Prossegue produzindo partições no arquivo de saída até que um arquivo de entrada esvazie. Então esse arquivo vazio passa a ser o arquivo de saída e o que era de saída passa a ser o de entrada.
Para melhor desempenho, as partições de entrada deve ser distribuídas entre os arquivos de entrada de forma a não esvaziar dois arquivos ao mesmo tempo. Pode ser necessário incluir partições vazias para isso.

Usando os dados do primeiro exemplo acima, e 2 vias (2 arquivos de entrada e 2 de saída), e colocando-se 5 partições na primeira entrada e 3 na segunda (para isso é necessário colocar 2 vazias), tem-se:
```
   entrada1 29 44 73 | 40 56 70 | 6 26 76 | 5 46 | V
   entrada2 24 71 80 | 2 41 42 | V
   saída

   intercalação das partições 1 e 2:
   entrada1 40 56 70 | 6 26 76 | 5 46 | V
   entrada2 2 41 42 | V
   saída    24 29 44 71 73 80 

   intercalação das partições 3 e 4:
   entrada1 6 26 76 | 5 46 | V
   entrada2 V
   saída    24 29 44 71 73 80 | 2 40 41 42 56 70

   intercalação das partições 5 e vazia:
   entrada1 5 46 | V
   entrada2 
   saída    24 29 44 71 73 80 | 2 40 41 42 56 70 | 6 26 76

   entrada2 esvaziou, vira saída
   entrada1 5 46 | V
   saída
   entrada2 24 29 44 71 73 80 | 2 40 41 42 56 70 | 6 26 76

   intercalação das partições 6 e 12:
   entrada1 V
   saída    5 24 29 44 46 71 73 80
   entrada2 2 40 41 42 56 70 | 6 26 76

   intercalação das partições vazia e 34:
   entrada1 
   saída    5 24 29 44 46 71 73 80 | 2 40 41 42 56 70
   entrada2 6 26 76

   entrada1 esvaziou, vira saída
   saída
   entrada1 5 24 29 44 46 71 73 80 | 2 40 41 42 56 70
   entrada2 6 26 76

   intercalação das partições 126 e 5:
   saída    5 6 24 26 29 44 46 71 73 76 80
   entrada1 2 40 41 42 56 70
   entrada2

   entrada2 esvaziou, vira saída; intercala as partições restantes:
   entrada2
   entrada1
   saída    2 5 6 24 26 29 40 41 42 44 46 56 70 71 73 76 80
```
Para calcular o número de partições necessárias em cada arquivo, parte-se de:
- na última fase, todos os arquivos de entrada têm uma partição, que serão intercaladas no arquivo de saída.
- no início de uma fase tem tantas partições em cada arquivo de entrada e o arquivo de saída vazio. Um desses números é o menor ($m$), e vai ser zerado quando esse tanto de partições for intercalado para o arquivo de saída. No início da fase seguinte, o arquivo que era o de saída terá $m$ partições, o que tinha $m$ partições terá 0, os demais terão $m$ a menos do que tinham.

Para dois arquivos, teremos na última fase 1 partição em cada, na penúltima, um com 1 e outro com 2, na anterior, um com 2 outro com 3, na anterior, um com 3 outro com 5 (é a sequência de Fibonacci). O total de partições tem que ser 2 na última, 3 na penúltima, 5 na anterior, 8 na anterior, etc. No exemplo, tínhamos 6 partições, então colocamos 2 vazias para ter as 8 necessárias.

De forma geral, tendo o número de partições por arquivo em uma fase, na anterior teremos um dos arquivos com o maior número de partições da fase seguinte e nos demais a soma entre esse número e cada um dos demais números de partições. Por exemplo, com 4 arquivos em que em uma fase eles têm 8, 10, 13, 17 partições, na anterior teria 17, 25, 27, 30 partições.
A sequência do número de partições por arquivo, com 4 arquivos, seria:
fase | A1 | A2 | A3 | A4 | tot
---: | -- | -- | -- | -- | --
n|1|1|1|1|4
n-1|1|2|2|2|7
n-2|2|3|4|4|13
n-3|4|6|7|8|25
n-4|8|12|14|15|49
n-5|15|23|27|29|94

Com dois arquivos de entrada:
fase | A1 | A2 |  tot
---: | -- | -- |  --
n|1|1|2
n-1|1|2|3
n-2|2|3|5
n-3|3|5|8
n-4|5|8|13
n-5|8|13|21

Com três:
fase | A1 | A2 | A3 | tot
---: | -- | -- | -- | --
n|1|1|1|3
n-2|1|2|2|5
n-3|2|3|4|9
n-4|4|6|7|17
n-5|7|11|13|31
n-6|13|20|24|57
n-7|24|37|44|105
n-8|44|68|81|193

Por exemplo, suponha que se tenha 31 partições com 100 registros cada, e se use 3 arquivos de entrada e um de saída. No início, teríamos um arquivo com 7 partições, um com 11 e um com 13. A tabela abaixo mostra o número de partições e de registros em cada arquivo, no início de cada fase:
fase | A1 | A2 | A3 | A4
:--- | -- | -- | -- | --
1 | 7x100 | 11x100 | 13x100 | —
1.1 | 6x100 | 10x100 | 12x100 | 1x300
1.2 | 5x100 | 9x100 | 11x100 | 2x300
1.3 | 4x100 | 8x100 | 10x100 | 3x300
1.4 | 3x100 | 7x100 | 9x100 | 4x300
1.5 | 2x100 | 6x100 | 8x100 | 5x300
1.6 | 1x100 | 5x100 | 7x100 | 6x300
2 | —     | 4x100  | 6x100  | 7x300
3 | 4x500 | —      | 2x100  | 3x300
4 | 2x500 | 2x900  | —      | 1x300
5 | 1x500 | 1x900  | 1x1700 | —
final | —     | —      | —      | 1x3100
