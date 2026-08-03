# Algoritmos de ordenação

Objetivo: Dado um conjunto de dados em ordem desconhecida, colocá-los em ordem, de acordo com um critério de comparação.
A comparação envolve uma "chave" associada a cada dado, que pode ser o dado inteiro, ou geralmente só parte dele.

## Algoritmos de ordenação em memória interna

Os dados estão todos em memória, em geral em um vetor.
Um grande número de algoritmos já foram propostos.
Veremos alguns deles, que exemplificam as principais ideias.
A grande maioria dos demais algoritmos são derivaçõs ou combinações desses.
Após a apresentação dos algoritmos, vamos analisar e comparar algumas de suas características.


### Bolha

O algoritmo da bolha compara elementos vizinhos.
Se todos os dados estiverem em ordem dois a dois, o conjunto completo está ordenado.

Caso dois vizinhos não estejam na ordem certa, troca a ordem deles.
Dessa forma, em uma passagem sobre os dados, garante-se que no mínimo o maior dado será colocado no final do vetor -- é essa característica que dá nome ao algoritmo.
Com n-1 passagens, garante-se que todos estão ordenados.

Em C:
```c
// diz se está ok o dado na posição i1 do vetor v estar antes do dado na posição i2
bool em_ordem(dado_t v[], int i1, int i2);

// troca o dado na posição i1 do vetor v pelo dado na posição i2
void troca(dado_t v[], int i1, int i2);

void ordena_bolha(int n, dado_t v[n])
{
  for (int i = 0; i < n - 1; i++) {
    for (int j = 0; j < n - 1; j++) {
      if (!em_ordem(v, j, j + 1)) troca(v, j, j + 1);
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
- varrer o vetor alternadamente, de baixo para cima e de cima para baixo -- algoritmo da coqueteleira.
- em vez de comparar os vizinhos, compara elementos a uma distância maior, e vai reduzindo essa distância -- algoritmo do pente.

### Seleção

Divide o vetor em duas seções: uma já ordenada no início do vetor (inicialmente vazia) e outra não ordenada no final do vetor (inicialmente o vetor inteiro).
A cada passo, encontra (ou seleciona, para dar nome ao algoritmo) o menor elemento da seção não ordenada e troca ele com o primeiro elemento dessa mesma seção. Esse elemento então passa da seção não ordenada para o final da seção já ordenada.

Alternativamente, inverte-se a posição das seções e procura-se o maior elemento a cada vez.

Em C:
```c
void ordena_selecao(int n, dado_t v[n])
{
  // elementos antes de i já estão ordenados, de i em diante não
  for (int i = 0; i < n - 1; i++) {
    // i é a posição onde será colocado o próximo elemento
    // procura onde está o menor elemento à partir da posição i
    int menor = i;
    for (int j = i + 1; j < n; j++) {
      if (!em_ordem(v, menor, j)) {
        menor = j;
      }
    }
    // troca o menor elemento com o que está na posição i, se já não
    //   estiver aí
    if (menor != i) troca(v, menor, i);
  }
}
```
Uma otimização possível é usar organizar os dados não ordenados de forma que seja mais rápido encontrar o menor (ou maior) dado -- algoritmo *heap*, que será visto mais tarde.

### Inserção

Este algoritmo também separa o vetor em duas metades, como o da inserção, com o início do vetor contendo os dados já ordenados e o final os que ainda não estão.
A cada passo, pega o primeiro dado da região não ordenada e insere na região ordenada.
Para realizar a inserção, desloca para a direita os dados já ordenados que são maiores que o dado a inserir.

Em C:

```c
void ordena_insercao(int n, dado_t v[n])
{
  // dados antes de i estão ordenados; de i em diante não
  for (int i = 1; i < n; i++) {
    // o dado na posição i é o próximo a ser inserido, salva ele
    dado_t salvo;
    copia(&salvo, &v[i]);
    // avança os dados antes de i que são maiores que o dado a inserir
    int pos = i;
    while (pos > 0 && !chaves_em_ordem(v[pos - 1].chave, salvo.chave)) {
      copia(&v[pos], &v[pos - 1]);
      pos--;
    }
    // copia o dado salvo para sua posição (se já não tiver lá)
    if (pos != i) copia(&v[pos], &salvo);
  }
}
```
Dá para otimizar um pouco realizando a cópia para `salvo` só se houver necessidade de movimentação de dados.

Uma variação, chamada "ordenação shell" consiste em aplicar a ordenação por inserção em dados que estão a uma certa distância entre si, e usar distâncias cada vez menores.
