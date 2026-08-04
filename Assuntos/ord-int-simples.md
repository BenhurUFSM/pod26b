# Algoritmos de ordenação

Objetivo: Dado um conjunto de dados em ordem desconhecida, colocá-los em ordem, de acordo com um critério de comparação.
A comparação envolve uma "chave" associada a cada dado, que pode ser o dado inteiro, ou geralmente só parte dele.
A comparação deve produzir uma ordem total entre os dados do conjunto (se `A<B` e `B<C` então `A<C`; se `A<B` então `!(B<A)`).

Os dados a ordenar podem ser em quantidade tal que é viável ordená-los com todos eles disponíveis em memória principal. Os primeiros algoritmos são para esse caso (ordenação em memória interna). No cado de o volume de dados não permitir essa forma de ordenação (por não haver memória principal suficiente), necessitamos algoritmos diferentes para se ter um bom desempenho na ordenação. Esses algoritmos serão vistos mais tarde.

## Algoritmos de ordenação em memória interna

Os dados estão todos em memória, `n` elementos em um vetor.

Um grande número de algoritmos já foram propostos.
Veremos alguns deles, que exemplificam as principais ideias.
A grande maioria dos demais algoritmos são derivaçõs ou combinações desses.
Após a apresentação dos algoritmos, vamos analisar e comparar algumas de suas características.


### Bolha

A ideia do algoritmo é realizar uma passagem sobre os dados, levando o maior deles (a "bolha") para o final do vetor.
Na segunda passagem, o segundo maior ficará na penúltima posição etc.
Com `n-1` passagens, garante-se que todos estão ordenados.

Em uma passagem, o algoritmo compara elementos vizinhos.
Caso dois vizinhos não estejam na ordem certa, troca a ordem deles.
Dessa forma, um dado que seja grande vai sendo levado para posições posteriores do vetor até encontrar um dado que seja maior, quando esse dado maior passará a ser levado, até que no final da passagem o maior dado encontrado é o que está no final.

Em C:
```c
// diz se está ok o dado na posição i1 do vetor v
//   estar antes do dado na posição i2
bool em_ordem_v(dado_t v[], int i1, int i2);

// troca o dado na posição i1 do vetor v
//   pelo dado na posição i2
void troca(dado_t v[], int i1, int i2);

// ordena os n dados do vetor v usando o algoritmo da bolha
void ordena_bolha(int n, dado_t v[n])
{
  // faz n-1 passagens pelo vetor
  for (int i = 0; i < n - 1; i++) {
    // em cada passagem, verifica n-1 dados
    for (int j = 0; j < n - 1; j++) {
      // troca o dado e seu vizinho se não estiverem em ordem
      if (!em_ordem_v(v, j, j + 1)) troca(v, j, j + 1);
    }
  }
}
```

Algumas otimizações possíveis:
- em cada passagem, se tem certeza de colocar o maior elemento restante no final, então pode-se verificar um elemento a menos em cada passagem;
- se em uma passagem não houve nenhuma troca, o vetor está ordenado, então dá para trocar o teste do laço externo por um teste de ter ou não havido uma troca na passagem anterior;
- os dados após a última troca em uma passagem não precisam ser verificados na passagem seguinte;
- os dados anteriores à primeira troca de uma passagem não precisam ser verificados na passagem seguinte.

Outras alterações:
- varrer o vetor alternadamente, do início ao fim e do fim ao início -- algoritmo da coqueteleira.
- em vez de comparar os vizinhos, compara elementos a uma distância maior, e vai reduzindo essa distância até 1 -- algoritmo do pente.

### Seleção

Divide o vetor em duas seções: uma já ordenada no início do vetor (inicialmente vazia) e outra não ordenada no final do vetor (inicialmente o vetor inteiro).
A cada passo, encontra (ou seleciona, para dar nome ao algoritmo) o menor elemento da seção não ordenada para passá-lo para a seção ordenada.
Como a cada vez é escolhido o menor dado da seção não ordenada para colocar na seção ordenada, os dados não ordenados remanescentes serão todos maiores (ou no limite iguais) aos dados que já foram ordenados, e o dado escolhido é maior (ou no limite igual) ao maior dado já ordenado.
Então, colocando o dado escolhido no final da seção ordenada mantém a ordem dos dados dessa seção.
O dado escolhido é trocado com o dado que está na primeira posição da região não ordenada. Essa posição então passa do início da seção não ordenada para o final da seção já ordenada (ele já está no local certo, basta trocar o tamanho das seções).

Alternativamente, pode ser ordenado do final ao início: coloca-se a seção ordenada após a seção ordenada e procura-se o maior elemento a cada vez.

Em C:
```c
void ordena_selecao(int n, dado_t v[n])
{
  // elementos antes de i já estão ordenados,
  //   de i em diante não estão ordenados e são
  //   todos maiores que os ordenados
  for (int i = 0; i < n - 1; i++) {
    // procura onde está o menor elemento à partir da posição i
    int menor = i;
    for (int j = i + 1; j < n; j++) {
      if (!em_ordem_v(v, menor, j)) {
        menor = j;
      }
    }
    // troca o menor elemento com o que está na posição i,
    //   se já não estiver aí
    if (menor != i) troca(v, menor, i);
  }
}
```
Uma otimização possível é organizar os dados não ordenados de forma que seja mais rápido encontrar o menor (ou maior) dado -- por exemplo o algoritmo *heap*, que será visto mais tarde.

### Inserção

Este algoritmo também separa o vetor em duas metades, como o da seleção, com o início do vetor contendo os dados já ordenados e o final os que ainda não estão.
A diferença é que em vez de buscar na região não ordenada o dado que deve ficar no final da região ordenada, ele encontra na região ordenada o local onde deve ficar o primeiro dado da região não ordenada.
A cada passo, pega o primeiro dado da região não ordenada e insere na região ordenada.
Para realizar a inserção, desloca para a direita os dados já ordenados que são maiores que o dado a inserir.

Em C:

```c
// copia o dado apontado por p2
//   para o local apontado por p1
void copia(dado_t *p1, dado_t *p2);

// diz se está ok o dado apontado por p1
//   estar antes do dado apontado por p2
bool em_ordem(dado_t *p1, dado_t *p2);

void ordena_insercao(int n, dado_t v[n])
{
  // dados antes de i estão ordenados; de i em diante não
  for (int i = 1; i < n; i++) {
    // o dado na posição i é o próximo a ser inserido, salva ele
    dado_t salvo;
    copia(&salvo, &v[i]);
    // avança os dados antes de i que são maiores que o dado a inserir
    int pos = i;
    while (pos > 0 && !em_ordem(&v[pos - 1], &salvo)) {
      copia(&v[pos], &v[pos - 1]);
      pos--;
    }
    // copia o dado salvo para sua posição (se já não tiver lá)
    if (pos != i) copia(&v[pos], &salvo);
  }
}
```
Dá para otimizar um pouco realizando a cópia para `salvo` só se houver necessidade de movimentação de dados.
Outra alteração seria usar uma forma mais otimizada para encontrar a posição de inserção na região ordenada (busca binária, por exemplo).

### Shell

Uma variação da ordenação por inserção, chamada "shell" por causa ser o nome do autor. Consiste em aplicar a ordenação por inserção em dados que estão a uma certa distância entre si, e usar distâncias cada vez menores.

Em C:
```c
void ordena_shell(int n, dado_t v[n])
{
  for (h) { // para cada distância h, que vai diminuindo
            //   o último valor de h tem que ser 1
    for (int i = h; i < n; i++) {
      // o dado na posição i é o próximo a ser inserido, salva ele
      dado_t salvo;
      copia(&salvo, &v[i]);
      // avança os dados antes de i que são maiores que o dado a inserir
      int pos = i;
      while (pos > h - 1 && !em_ordem(&v[pos - h], &salvo)) {
        copia(&v[pos], &v[pos - h]);
        pos -= h;
      }
      // copia o dado salvo para sua posição (se já não tiver lá)
      if (pos != i) copia(&v[pos], &salvo);
    }
  }
}
```
A escolha dos valores da distância (`h` no código acima) afeta enormemente o desempenho do algoritmo.
Escolhas comuns de `h` são:
- potências de 2, desde a maior que seja menor que `n` até `1` (não tem um desempenho especialmente bom).
- iniciar com `h` em 1 e aumentar com `h = 3*h+1` até passar de `n` e então usar `h = h/3` a cada passagem (desempenho melhor).
- usar a sequência empírica `701, 301, 132, 57, 23, 10, 4, 1`.

