# Algoritmos de ordenação

## Algoritmos de ordenação em memória interna

### Ordenação por distribuição

Os algoritmos de ordenação vistos anteriormente são baseados na comparação entre os elementos a ordenar.
Uma outra classe de algoritmos de ordenação baseia-se na distribuição desses elementos, do acordo com o valor de suas chaves individualmente, sem ser necessário realizar a comparação de chaves de elementos distintos.
Algum conhecimento prévio sobre os valores das chaves é necessário.
Além disso, esses algoritmos necessitam memória auxiliar para realizar a ordenação.

#### Contagem

Ordena dados inteiros, quando se conhece o menor e o maior desses valores.
Aloca-se um vetor de contadores, que irá contar quantos dados existem para cada valor possível.
Pode ser muito ineficiente no uso de memória auxiliar, se o número de valores possível for muito maior que o número de itens a ordenar.

Por simplicidade, vamos considerar que os valores das chaves são entre 0 e `k`.
Não é difícil alterar o código abaixo caso os valores sejam entre `k1` e `k2`.
Se o valor de `k` (ou de `k1` e `k2`) não for conhecido previamente, pode ser descoberto com um percurso prévio nos dados.

O algoritmo funciona assim:
- inicialmente, conta-se quantos valores de cada chave existem na entrada.
- então, altera-se esses contadores para que contenham quantos elementos existem na entrada com chave menor ou igual a cada valor.
- finalmente, percorre-se o vetor de entrada, na ordem inversa, copiando cada elemento para o vetor de saída. Sua posição é dada pelo número de elementos menores ou iguais a ele que existem, o que foi calculado previamente.

Em C:
```c
// coloca em saida os n dados de entrada, em ordem de chave,
//   sabendo que a chave é um inteiro >=0 e <=k
void ordena_contagem(int n, dado_t entrada[n], dado_t saida[n], int k)
{
    int ct[k + 1] = {0};
    for (int i = 0; i < n; i++) {
        int chave = entrada[i].chave;
        ct[chave]++;
    }
    for (int i = 1; i <= k; i++) ct[i] += ct[i-1];
    for (int i = n-1; i >= 0; i--) {
        int chave = entrada[i].chave;
        ct[chave]--;
        saida[ct[chave]] = entrada[i];
    }
}
```
Otimizações:
- se não tem dados além das chaves (a chave **é** o dado), o laço de saída pode ser mais simples, não necessita da entrada.
- se não tem chave repetida, a lógica pode ser simplificada.
- se não tem chave repetida e `k == n`, nem precisa da entrada.

#### Raiz (*radix sort*)

Neste algoritmo, os dados são ordenados por uma parte da chave a cada vez.
Por exemplo, suponha que as chaves sejam numéricas, pode-se ordenar por um dígito a cada vez.

Por exemplo, se a entrada contiver os números 45, 85, 47, 32, 13, 37, 10, ordenando pelo último dígito (unidade) e depois pelo primeiro dígito (dezenas), teríamos:
```
    45 85 47 32 13 37 10
    10|32|13|45 85|47 37
    10 13|32 37|45 47|85
```

Existem 10 dígitos, e usa-se a ordenação por contagem para ordenar cada dígito.
A função de ordenação por contagem acima poderia ser alterada para ordenar pelo d-ésimo dígito:
```c
void ordena_contagem_digito(int n, dado_t entrada[n], int d)
{
    dado_t saida[n];
    int k = 10;
    int ct[k + 1] = {0};
    int div = 1;
    for (int i = 0; i < d; i++) div *= 10;
    for (int i = 0; i < n; i++) {
        int chave = (entrada[i].chave / div) % 10;
        ct[chave]++;
    }
    // a soma dos ct e a cópia para a saída ficam iguais
    // copia da saída para a entrada
    for (int i = 0; i < n; i++) {
        entrada[n] = saida[n];
    }
}
```
A ordenação por raiz então ordena sucessivamente por cada dígito, a partir do menos significativo:
```c
void ordena_raiz(int n, dado_t entrada[n], int ndig)
{
    for (int i = 0; i < ndig; i++) {
        ordena_contagem_digito(n, entrada, i)
    }
}
```
Também é possível iniciar no dígito mais significativo, mas nesse caso os dígitos seguintes devem ser ordenados separadamente (ordena-se o segundo dígito de quem tem o primeiro dígito 0, depois de quem tem o primeiro dígito 1 etc)

Algumas otimizações possíveis:
- em vez de copiar a saída para a entrada, daria para fazer a ordenação de um dígito da entrada para a saída e do seguinte da saída para a entrada.
- testar se todos os valores têm o dígito 0 (pode ser comum em caso de se suportar mais dígitos do que efetivamente estão sendo usados).
- pode-se usar outras bases e não só base 10. Por exemplo, com base 256 se ordena um byte por vez, e se pode evitar a divisão e o resto. Pode-se também ordenar outros tipos de dados (strings, por exemplo).
- no cado de dados cuja chave tem mais de um componente, pode-se ter bases diferentes (ou até algoritmos diferentes) para cada componente.

#### Bucket sort

Nesse algoritmo, os dados são distribuídos em `k` *buckets* (baldes), cada um contendo uma fatia dos valores possíveis na entrada.
Essa distribuição deve acontecer de forma que os dados do primeiro balde têm chaves menores que os do segundo, os do segundo menores que o terceiro etc.
Por exemplo, se os dados da entrada forem números reais entre 0 e 1 e tivéssemos 5 baldes, o primeiro conteria valores entre 0 e 0,2, o segundo entre 0,2 e 0,4 e assim por diante.
Os dados em cada *bucket* são então ordenados usando um algoritmo adequado de ordenação.
Os dados são retirados dos *buckets*, em ordem, para o vetor de saída.
Um *bucket* pode ser implementado como uma lista, por exemplo.

Em pseudo-código:
```
    função ordena_bucket(v:vetor de n dados)
        buckets = vetor de k listas vazias
        max = 1 + maior valor de chave nos dados de v
        para cada dado d em v
            indice = (d.chave / max) * k
            adiciona d em buckets[indice]
        para cada lista l de buckets
            ordena os dados na lista l
        indice = 0
        para cada lista l de buckets
            enquanto l não estiver vazia
                remove um dado de l
                v[indice++] = dado

```
Algumas otimizações:
- Se a distribuição dos dados for tal que se consegue ter poucos dados em cada lista, a ordenação das listas influi pouco no tempo total.
- A ordenação pode ser feita no vetor inteiro em vez de em cada bucket, após os dados serem devolvidos para o vetor. Com buckets pequenos, os dados já estarão quase ordenados, o que dá um desempenho muito bom para o algoritmo de ordenação por inserção, por exemplo.
- Fazendo-se uma análise prévia dos dados, pode-se saber quantos dados serão colocados em cada bucket. Pode-se reorganizar os dados no próprio vetor, não sendo necessário memória auxiliar para as listas, nem a cópia dos dados de volta dos buckets para o vetor.
