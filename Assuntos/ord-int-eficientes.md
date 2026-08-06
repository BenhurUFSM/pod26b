# Algoritmos de ordenação

## Algoritmos de ordenação em memória interna

### Heap

O algoritmo heap é uma otimização em relação ao algoritmo de seleção.
Ele ordena os dados do maior ao menor.
No início do vetor estão os dados não ordenados, no final os ordenados.
A cada passo, é retirado o maior elemento da parte não ordenada e colocado no início da parte ordenada.

A diferença em relação ao algoritmo de seleção está na forma como a seleção é realizada.
Os dados não ordenados são organizados em uma estrutura chamada uma árvore heap, em que o maior elemento está sempre na raiz.
A raiz da árvore é colocada no índice 0 do vetor. O algoritmo consiste então em:
1. organiza os dados no vetor na forma de uma árvore heap.
2. coloca o dado que está na raiz no final do vetor não ordenado, e o dado que está nessa posição na raiz. Isso coloca o maior dado no início da parte ordenada do vetor, mas desfaz a estrutura heap (a raiz provavelmente não contém mais o maior valor).
3. reorganiza a heap (que agora tem um elemento a menos), sabendo que só a raiz foi bagunçada.
4. volta ao passo 2, se ainda sobram elementos não ordenados.

Em C:
```c
// funções auxiliares

// no vetor v de tamanho n, continha uma árvore heap a partir do índice i
//   mas o valor em i foi alterado.
// reorganiza para que volte a ser uma heap.
void heap_ajeita(int n, dado_t v[n], int i);
// o vetor v de tamanho n contém dados em uma ordem qualquer.
// organiza para que contenha uma árvore heap.
void heap_constroi(int n, dado_t v[n]);

void ordena_heap(int n, dado_t v[n])
{
  // organiza os dados em v na ordem de uma árvore heap
  heap_constroi(n, v);

  // v contém uma árvore heap, com o maior elemento em v[0].
  // a cada passo do laço, v[p] contém o último elemento da heap,
  //   e a partir da posição p+1, os valores em v são todos não maiores
  //   que os valores na heap, e estão em ordem crescente.
  for (int p = n-1; p > 0; p--) {
    // a heap tem p+1 elementos, troca v[0] (o maior elemento)
    //   por v[p] (o final do vetor com a heap / início do vetor ordenado)
    troca(v, 0, p);
    // a heap agora tem p elementos, as subárvores são heap, mas
    //   a raiz talvez não -> ajeita a raiz
    heap_ajeita(p, v, 0);
  }
}
```

Uma árvore heap é uma árvore binária em que:
- o valor na raiz da árvore é maior que o valor na raiz de suas subárvores
- suas subárvores são árvores heap
- a árvore é semicompleta (todos os níveis exceto o último têm todos os elementos possíveis, e os elementos do último nível estão justificados à esquerda).

Uma árvore binária semicompleta pode ser colocada em um vetor de uma forma bem simples e econômica, por nível:
- a raiz é colocada no início do vetor;
- as posições seguintes do vetor são ocupadas pelos filhos da raiz, da esquerda para a direita;
- as posições seguintes do vetor são ocupadas pelos filhos dos filhos da raiz, da esquerda para a direita;
- etc

Nessa organização, em um vetor que inicia no índice 0, o filho esquerdo do nó que está no índice `i` será colocado no índice `2i+1`, e o filho direito no índice `2i+2`, o pai estará em `(i-1)/2`.

"Ajeitar" uma árvore heap em que a raiz não é heap mas os filhos são, consiste em colocar na raiz o maior elemento entre o que está na raiz e os que estão nas raízes dos filhos. Esse maior elemento ou é o que já está na raiz (e não tem que fazer nada, a árvore inteira é heap), ou é o que está na raiz de um dos filhos. Nesse último caso, o outro filho não é afetado e continua sendo uma árvore heap, e o filho afetado cede seu valor para o pai e recebe o valor que estava no pai. Ele agora é uma árvore que era heap e teve sua raiz alterada, que é o mesmo problema que tínhamos no pai. É um problema recursivo.

Em C:
```c
// no vetor n, os nós abaixo de i contêm árvores heap, mas talvez o nó i
//   não seja maior que seus filhos.
// reorganiza a árvore à partir de i para que seja uma heap.
void heap_ajeita(int n, dado_t v[n], int i)
{
  // posição dos filhos de i
  int esq = 2 * i + 1;
  int dir = esq + 1;
  // se o nó i não tem filho esquerdo, também não tem filho direito,
  //   e o nó i é uma árvore heap
  if (esq >= n) return;

  // encontra o filho que tem o maior valor
  int maior_filho = esq;
  if (dir < n && !em_ordem_v(v, dir, esq)) maior_filho = dir;

  // se o maior valor dos filhos for maior que o valor em i,
  //   a árvore deve ser corrigida. Coloca o maior valor em i
  //   e desce o valor em i para o filho. Isso pode fazer com
  //   que a árvore desse filho não seja mais heap e deve ser
  //   corrigida.
  if (!em_ordem_v(v, maior_filho, i)) {
    troca(v, i, maior_filho);
    heap_ajeita(n, v, maior_filho);
  }
}
```

Para criar uma árvore heap à partir de um vetor qualquer, parte-se do princípio que uma árvore que só tem a raiz é uma heap.
Os nós folha de uma árvore binária qualquer são heaps.
Em uma árvore binária semicompleta com `n` elementos armazenada em um vetor, os nós que estão nos índices à partir de `n/2` são folha.
O nó em `n/2-1` é um nó que tem filhos que são todos folha, e portanto árvores heap. Ele pode ser transformado em uma heap com a função `heap_ajeita` acima. Agora todos os nós à partir de `n/2-1` são heap. Os filhos do nó em `n/2-2` estão após essa posição, e são portanto heap. Aplica-se essa lógica até chegar à raiz.

Em C:
```c
// o vetor v contém dados quaisquer.
// organiza para que contenha uma árvore heap.
void heap_constroi(int n, dado_t v[n])
{
  // os nós à partir de n/2 não têm filhos, logo são heaps.
  // os nós antes disso podem não ser -- ajeita eles.
  // tem que fazer isso de trás para diante, para que os
  //   filhos do nó a ser ajeitado já sejam heaps
  for (int i = n / 2 - 1; i >= 0; i--) {
    heap_ajeita(n, v, i);
  }
}
```

### Merge

A ideia do algoritmo de merge sort é que deve ser simples produzir um vetor ordenado à partir de dois vetores ordenados, mesclando seus dados.
Basta, a cada passo, retirar o primeiro elemento do vetor que contém o menor valor e colocar no final do vetor resultado.
Essa operação é chamada de merge.

Em C, com dados `int`:
```c
// v1 e v2 contém dados ordenados
// junte-os em w, mantendo a ordem
void merge(int n1, int v1[n1], int n2, int v2[n2], int w[n1+n2])
{
    int i1 = 0;  // índice que vai varrer v1
    int i2 = 0;  // índice que vai varrer v2
    for (int iw = 0; iw < n1+n2; iw++) {
        if (i2 >= n2)              w[iw] = v1[i1++];
        else if (i1 >= n1)         w[iw] = v2[i2++];
        else if (v1[i1] <= v2[i2]) w[iw] = v1[i1++];
        else                       w[iw] = v2[i2++];
    }
}
```
Na nossa implementação, os vetores não são independentes, mas partes do mesmo vetor, então usamos um só vetor e índices dizendo onde estão os subvetores.
A saída é colocada em um vetor auxiliar e depois devolvida ao vetor principal.

Em C:
```c
// no vetor v tem duas partições ordenadas, uma nas posições p1 até
//   p2-1, e outra nas posições p2 até u2.
// esta função mistura essas partições, produzindo uma única partição
//   com os mesmos dados, ordenados, nas posições p1 até u2.
// usa o vetor w como espaço auxiliar (ele tem o mesmo tamanho de v).
void merge(dado_t *v, int p1, int p2, int u2, dado_t *w)
{
  int u1 = p2 - 1;      // última posição da partição 1
  int i1 = p1;          // índice que vai varrer a partição 1
  int i2 = p2;          // índice que vai varrer a partição 2
  int n = u2 - p1 + 1;  // número total de itens nas duas partições
  int iv, iw;           // índices no vetor v e w

  // copia os n dados de v para w, em ordem
  for (iw = 0; iw < n; iw++) {
    // copia para w[iw] o menor entre v[i1] e v[i2]
    if (i1 <= u1 && (i2 > u2 || em_ordem_v(v, i1, i2))) iv = i1++;
    else iv = i2++;
    copia(&w[iw], &v[iv]);
  }

  // copia os n dados ordenados, de w para v
  iv = p1;
  for (iw = 0; iw < n; iw++) {
    copia(&v[iv], &w[iw]);
    iv++;
  }
}
```
Tendo essa função que faz a mistura de dois subvetores ordenados, podemos usá-la para ordenar 2 vetores já ordenados pequenos produzindo um vetor ordenado maior.
Tratando um vetor desordenado de `n` elementos como `n` vetores ordenados de tamanho 1, podemos usar essa função para produzir `n/2` vetores ordenados de tamanho 2, depois `n/4` vetores de tamanho 4 etc, até ter um vetor ordenado de tamanho `n`.

Em C:
```c
void ordena_merge(int n, dado_t v[n])
{
  // aloca um vetor auxiliar de mesmo tamanho que v
  dado_t *w = malloc(n * sizeof(dado_t));
  assert(w != NULL);

  // começa com n partições de tamanho 1 (que são naturalmente ordenadas),
  //   mistura duas a duas obtendo partições ordenadas com o dobro do
  //   tamanho, até ter uma só partição ordenada, de tamanho n
  // t é o tamanho das partições
  for (int t = 1; t < n; t *= 2) {
    // mistura cada duas partições vizinhas, ordenadas, de tamanho t,
    //   transformando-as em uma partição ordenada de tamanho t*2,
    //   ocupando o mesmo local do vetor
    for (int i = 0; i < n; i += 2 * t) {
      // a primeira partição inicia na posição i, a segunda na posição j
      int j = i + t;
      // no final do vetor, pode ser que não tenha 2 partições
      if (j >= n) break;
      // u é a última posição da segunda partição
      int u = j + t - 1;
      if (u > n - 1) u = n - 1;
      // faz a junção
      merge(v, i, j, u, w);
    }
  }
  free(w);
}
```
Algumas otimizações:
- no merge, em vez de ordenar os dois subvetores de `v` para `w` e depois copiar de volta para `v`, fazer 2 etapas em uma: pegar 4 subvetores em `v`, misturar para 2 subvetores em `w`, e depois misturar esses 2 subvetores para um em `v`, evitando as cópias.
- no merge, copiar um dos subvetores para `w` e depois fazer o merge de volta para `v`, copiando só metade dos dados, e limitando a necessidade de alocação extra (`w` pode ser alocado com metade do tamanho de `v`).
- no merge, verificar os elementos do primeiro subvetor de `v` que não precisam ser mexidos (aqueles menores que o primeiro elemento do segundo subvetor), e os últimos elementos do segundo subvetor que não precisam ser mexidos (aqueles que são maiores que o último elemento do primeiro subvetor). Só os elementos do meio precisam participar do merge. Isso pode reduzir bastante o número de operações no caso de os dados já estarem quase ordenados.
- usar um algoritmo mais simples para ordenar subvetores menores que determinado tamanho.

### Quick

Algoritmo apoiado em uma operação de particionamento do vetor.
No particionamento, escolhe-se um elemento do vetor para ser o "pivô", e coloca-se os elementos do vetor que podem ficar antes do pivô no início do vetor, os elementos que têm que ficar depois do pivô no final do vetor e o pivô entre esses dois grupos. Após o particionamento, o pivô está em sua posição correta no vetor, e têm-se duas partições com elementos fora de ordem, mas os dados à esquerda do pivô vão continuar à esquerda quando estiverem em ordem, e os à direita vão continuar à direita. Então pode-se ordenar cada uma dessas partições de forma independente.
A ordenação de cada partição pode usar o mesmo algoritmo, recursivamente.

Em C:
```c
// funções auxiliares

// escolhe um pivô e particiona o vetor v em 3 subvetores:
//   - à esquerda os dados menores que o pivô, 
//   - à direita os dados maiores que o pivô,
//   - entre eles o pivô
// retorna a posição do pivô
int quick_particiona(int n, dado_t v[n]);

void ordena_quick(int n, dado_t v[n])
{
  // se o vetor tiver menos de 2 elementos, já está ordenado
  if (n < 2) return;

  // particiona o vetor em duas partições, com o pivô entre elas,
  //   a partição esquerda contendo dados menores que o pivô e a direita
  //   com os dados maiores
  int pos_pivô = quick_particiona(n, v);

  // ordena cada partição (o pivô tá no lugar certo)
  ordena_quick(pos_pivô, v);
  ordena_quick(n - pos_pivô - 1, v + pos_pivô + 1);
}
```

A função de particionamento tem 2 problemas: escolher o pivô e realizar o particionamento.
Na função abaixo é escolhido o primeiro elemento do vetor como pivô, por simplicidade.
Para realizar o particinamento, percorre-se o vetor do início para o final pulando os elementos que podem ficar na partição da esquerda (menores que o pivô), e do final para o início pulando os que podem ficar na partição da direita (maiores que o pivô).
Esses percursos terminam quando for encontrado, à esquerda, um dado que deveria estar à direita, e à direita um que deveria estar à esquerda.
Troca-se esses elementos de posição e continua-se os percursos, até que eles se encontrem. O ponto de encontro é o local onde deve ficar o pivô.

Em C:
```c
// particiona o vetor v em 2 partições.
// escolhe um valor no vetor para ser o pivô, e coloca todos
//   os valores menores que o pivô antes dele, e todos os maiores
//   depois do pivô.
// retorna a posição do pivô
int quick_particiona(int n, dado_t v[n])
{
  // escolhe o primeiro elemento do vetor para ser o pivô.
  // essa escolha é simples, mas é péssima se v já estiver ordenado...
  // se escolher um dado em outra posição, ele deve ser colocado na posição 0
  int pos_pivô = 0;

  // antes de i ficam os que são <= pivô
  // depois de j ficam os que são > pivô
  // entre i e j, os que ainda não se sabe
  int i = pos_pivô + 1;
  int j = n - 1;
  while (i <= j) {
    // avança o i até achar um que não pode ficar antes do pivô
    while (i <= j && em_ordem_v(v, i, pos_pivô)) i++;
    // recua o j até achar um que não pode ficar depois do pivô
    while (i <= j && !em_ordem_v(v, j, pos_pivô)) j--;
    if (i < j) {
      // em i tem um que é maior que o pivô, em j um que é <=
      troca(v, i, j);
      i++;
      j--;
    }
  }

  // em j está o último que é <= pivô -> coloca o pivô aí
  if (j != pos_pivô) {
    troca(v, pos_pivô, j);
    pos_pivô = j;
  }

  return pos_pivô;
}
```
Otimizações:
- melhor escolha do pivô. Uma simples é escolher o valor médio entre o elemento que está no início do vetor, o que está no final e o que está no meio.
- usar um algoritmo mais simples para ordenar subvetores pequenos.
