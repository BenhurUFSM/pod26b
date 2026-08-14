## Complexidade de Algoritmos

Nos gráficos do t1, é possível verificar a enorme disparidade no tempo de execução dos diversos algoritmos de ordenação vistos.
Dependendo da escolha do algoritmo, o tempo de execução da mesma tarefa (no caso, ordenar um conjunto de dados) pode levar um tempo irrisório ou um tempo inviável.
É importante se poder realizar uma análise de um algoritmo para se ter uma ideia do tempo que uma execução irá levar, e avaliar até que volume de dados é viável se utilizar determinado algoritmo, ou até mesmo se é razoável se buscar novos algoritmos para realizar determinada tarefa de forma mais rápida.

Desconsiderando características arquiteturais como a memória cache, a implementação do pipeline, o tipo de processador, as otimizações do compilador (entre outras), o tempo de execução de um algoritmo é função do número de operações que o algoritmo executa e do tempo necessário para a execução de cada uma dessas operações.
O algoritmo define que operações devem ser realizadas, então analisando-se o algoritmo deve ser possível avaliar (pelo menos de forma aproximada) o tempo que ele levará para ser executado. Esse tempo é ligado à *complexidade* do algoritmo, que dá uma ideia do número de operações necessárias, de acordo com o volume de dados que será processado.

Considere por exemplo a função abaixo, que implementa a ordenação de um vetor pelo algoritmo da bolha:
```c
   void bolha(int n, dado v[n])
   {
       int i, j;
       i = n - 1;
       while (i > 0) {
           j = 0;
           while (j < i) {
               if (!menor(v[j], v[j + 1])) {
                   troca(v, j, j + 1);
               }
               j++;
           }
           i--;
       }
   }
```
Ela contém instruções para soma de inteiros, atribuição de inteiro, comparação de inteiros, incremento de inteiro, comparação de chaves e troca de dados.
Digamos que cada uma dessas instruções demore $t_s$, $t_a$, $t_{ci}$, $t_i$, $t_c$ e $t_t$, respectivamente.
Os comandos controlados pelo primeiro `while` serão executados $n-1$ vezes (e a comparação será executada $n$ vezes).
Os comandos controlado pelo segundo `while` serão executados $n-1$ vezes na primeira repetição, $n-2$ na segunda, etc, $1$ na última, um total de $n^2/2$ vezes.
Pode-se aproximar o tempo de execução dessa função calculando o número de vezes que cada uma é executada:
$$t_{ex} = t_s + t_a + n t_{ci} + (n-1) (t_a + t_i) + (n^2/2+1)t_{ci} + (n^2/2)\times(t_s + t_c + t_t + t_i)$$
Foi considerado o pior caso, que é quando os dados estão em ordem reversa, e a troca é feita todas as vezes. No melhor dos casos, os dados já estão ordenados e esse tempo seria 0.
Como a troca é a operação mais cara, explica-se a enorme diferença no tempo de execução da ordenação por bolha observada no t1.
Reorganizando os termos para isolar $n$, temos:
$$t_{ex} = n^2(t_{ci} + t_s + t_c + t_t + t_i)/2 + n(t_ci+t_a+t_i) + t_s + t_a - t_i + t_{ci}$$
Como os tempos individuais foram considerados constantes, podemos agrupá-los em 3 constantes e simplificar:
$$t_{ex} = n^2 k_1 + n k_2 + k_3$$
De todos os tempos, os maiores são $t_t$ e $t_c$, o que faz com que $k_1$ deva ser a maior das constantes.
Mas podemos ignorar isso e supor que as 3 constantes sejam iguais. Façamos uma outra suposição, ainda mais improvável, de que as constantes $k_2$ e $k_3$ sejam bem maiores que $k_1$.
Se plotarmos os dois gráficos, $y=x^2+x+1$ e $y=x^2+10x+10$ (colocando $k_2$ e $k_3$ 10 vezes maiores que $k_1$), vemos que, se o gráfico for plotado com $x$ entre 1 e 10 os gráficos são bem diferentes, mas se plotarmos com $x$ entre 1 e 1000, já fica até difícil de distingui-los. Isso significa que quanto maior o número de dados considerado, tanto mais o valor de $t_{ex}$ pode ser aproximado como $k_b n^2$, ignorando os demais termos.

Fazendo uma análise semelhante para outro dos algoritmos de ordenação, merge, simplificado um pouco para considerar somente os casos em que $n$ é potência de 2:
```c
void ordena_merge(int n, dado_t v[n], dado_t w[n])
{
  for (int t = 1; t < n; t *= 2) {
    for (int i = 0; i < n; i += 2 * t) {
      int j = i + t;
      int u = j + t - 1;
      merge(v, i, j, u, w);
    }
  }
}
```
O segundo *for* vai causar várias chamadas a *merge*. Independente do número de chamadas, os valores de *i* e *u* passados vão percorrer todo o vetor, sem sobreposição.
O primeiro *for* vai causar a execução do segundo $log_2 n$ vezes.
```c
void merge(dado_t v[], int i, int j, int u, dado_t w[])
{
  int u1 = j - 1;
  int i1 = i;
  int i2 = j;
  int np = u - i + 1;
  int iv, iw;

  for (iw = 0; iw < np; iw++) {
    if (i1 <= u1 && (i2 > u || em_ordem_v(v, i1, i2))) iv = i1++;
    else iv = i2++;
    copia(&w[iw], &v[iv]);
  }

  iv = i;
  for (iw = 0; iw < np; iw++) {
    copia(&v[iv], &w[iw]);
    iv++;
  }
}
```
Ignorando os tempos pequenos (considerando só $t_c$ e $t_t$), e considerando o pior caso, em que a função de comparação *em_ordem* vá ser chamada todas as vezes, teremos o tempo de execução dessa função como sendo $np t_c + 2np t_t$. No melhor dos casos, a comparação seria feita metade das vezes.
Nesse tempo, $np$ é $u-i$, e a soma dos vários $u-i$ do segundo `for` da primeira função é $n$. Isso faz com que o tempo das várias chamadas a *merge* em uma repetição do primeiro *for* da primeira função seja $n t_c + 2n t_t$. Como esse `for` repete $log_2 n$ vezes, o tempo total de execução da ordenação será aproximadamente $n log_2 n (t_c + 2 t_t)$ ou, renomeando as constantes, $k_m n log n$.

Podemos comparar os dois algoritmos, bolha e merge plotando os gráficos para $k_b n^2$ e $k_m n log n$.
Variando-se os valores das constantes, muda-se basicamente o ponto (o valor de $x$) em que a linha da bolha ultrapassa a do merge, mas para quaisquer valores dessas constantes, depois que esse cruzamento acontece, o tempo do algoritmo da bolha afasta-se vertiginosamente daquele do merge (como pode ser observado no t1).
