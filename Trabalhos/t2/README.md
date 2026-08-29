
# Trabalho 2 - desempenho de árvores binárias de busca

Compare o desempenho de árvore binária de busca sem equilibrio automático, árvore AVL e árvore AA.
Você deve medir o tempo de inclusão, busca com sucesso, busca sem sucesso e remoção de conjuntos de dados em cada árvore.
As medidas devem ser feitas com 3 tamanhos de dados, um grande (teste um tamanho que leve alguns segundos), um médio (metade do grande) e um pequeno (metade do médio).
As medidas devem ser realizadas com os dados inseridos em ordem aleatória e em ordem menos aleatória.
A busca com sucesso deve buscar cada um dos dados inseridos. A busca sem sucesso deve buscar em cada intervalo entre os dados inseridos.

Sugestão de preparação dos dados:
- preencha um vetor de tamanho $n$ com os valores pares entre 0 e $2n$.
- embaralhe o vetor
- insira todos os dados na árvore
- busque os mesmos dados
- busque os dados ímpares
- remova os dados da árvore

Para embaralhar os dados em um caso faça o embaralhamento de todos os índices do vetor, no outro caso embaralhe somente os valores nos índices pares, mantendo os valores nos índices ímpares na ordem original.

A interface de acesso às árvores está em [abb.h](Trabalhos/t2/abb.h).
No mesmo local tem também a implementação de ABB, AVL e AA, e um programa de teste.

Para compilar, escolha sua árvore. Para avl por exemplo:
```
gcc -O3 -o teste teste_abb.c avl.c
```

No [artigo](https://user.it.uu.se/~arneande/ps/simp.pdf) em que apresenta a árvore AA, o autor sugere que a busca e remoção em uma ABB, que geralmente é implementada com uma comparação de 3 vias (se a chave buscada é igual, menor ou maior que a chave do nó) poderia ser implementada com uma busca de duas vias (se a chave é menor ou não), com melhorias no desempenho.
O artigo é dos anos 90. Será que essa diferença ainda existe em uma máquina atual?

Para testar isso, altere as implementações, para incluir busca e remoção de 2 vias, e inclua na comparação.

A alteração sugerida para a busca é percorrer a árvore até o final, seguindo no filho esquerdo se a chave buscada for menor que o valor do nó ou no filho direito em caso contrário. A cada vez que segue pela direita, guarda em uma variável global o nó onde essa decisão foi tomada.
Como se vai à direita quando a chave é maior ou igual que o valor do nó, se a chave for igual será tomada a direita, e como os nós à direita do nó que contém a chave só podem ter valores maiores que a chave, todos os demais desvios serão à esquerda. O nó onde se tomou o rumo da direita pela última vez é o único que pode conter a chave. Então, após percorrer a árvore toda, verifica-se se esse nó contém a chave.

Em código:
```c
dado_t *abb_busca2(ABB *a, chave_t chave)
{
  // variável que conterá nó onde houve o último desvio para a direita
  static ABB *candidato = NULL;
  if (!abb_é_vazia(a)) {
    if (compara_menor(chave, a->chave)) {
      return abb_busca2(a->esq, chave);
    } else {
      candidato = a;
      return abb_busca2(a->dir, chave);
    }
  }
  // chegou a um nó vazio, verifica se o candidato contém a chave
  dado_t *ret = DADO_NAO_EXISTE;
  if (candidato != NULL && compara_igual(chave, candidato->chave)) {
    ret = &candidato->dado;
  }
  candidato = NULL;  // reinicializa para a próxima busca
  return ret;
}
```
As funções de comparação de via dupla (considerando os tipos de dados do trabalho) seriam:
```c
bool compara_menor(chave_t a, chave_t b)
{
  return a < b;
}

bool compara_igual(chave_t a, chave_t b)
{
  return a == b;
}
```

Para a remoção, é necessário uma segunda variável global, para conter o último nó não nulo visitado.
Ao final do percurso, se a chave existir ela está no nó do último desvio à direita.
Se o último nó visitado é o nó que deve ser removido da árvore.
Se esse nós não forem o mesmo, o dado contido no último nó visitado deve ser salvo no nó que contém a chave a remover.

Em código:
```c
ABB *abb_remove2(ABB *a, chave_t chave)
{
  static ABB *candidato = NULL; // último nó visitado que é >= chave
  static ABB *último;           // último nó não nulo visitado
  if (abb_é_vazia(a)) return a;
  último = a;
  if (compara_menor(chave, a->chave)) {
    a->esq = abb_remove2(a->esq, chave);
  } else {
    candidato = a;
    a->dir = abb_remove2(a->dir, chave);
  }
  // no retorno, se for o último nó (o primeiro retorno)
  //   tem que fazer a remoção se a chave existe
  if (a == último) {
    // se a chave existe, está no candidato e o último nó deve ser removido
    //   depois de salvar seus dados no nó candidato
    if (candidato != NULL && compara_igual(chave, candidato->chave)) {
      if (candidato != último) {
        // salva os dados do último no nó onde está a chave
        abb_troca_dados(candidato, último);
        // o último saiu pela esquerda, pode ter uma subárvore à direita
        a = último->dir;
      } else {
        // o último saiu pela direita, pode ter uma subárvore à esquerda
        a = último->esq;
      }
      abb_libera_nó(último);
    } // dá para colocar um else aqui se quiser fazer algo quanto a chave não existe
    candidato = NULL; // inicializa para a próxima chamada
  }
  return a;
}
```
