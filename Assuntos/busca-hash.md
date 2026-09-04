#### Tabela de dispersão ou de espalhamento (tabela *hash*)

Uma tabela de dispersão é uma estrutura de dados de busca que otimiza o tempo de busca priorizando o acesso direto disponibilizado por um vetor.
A busca é realizada em duas etapas: primeiro, calcula-se o índice do vetor onde o dado buscado está, usando uma *função de dispersão* (função *hash*), que realiza esse cálculo à partir do valor da chave; depois, acessa-se o vetor no índice calculado. Nessa posição do vetor, é colocada a chave e o dado associado à ela.
Tudo dando certo, o tempo de acesso depende do tempo de cálculo da função de dispersão e do tempo de acesso ao vetor, sendo um tempo tipicamente constante, independente do número de dados existentes no vetor.

Em geral, nem tudo dá certo, e acontece de chaves diferentes produzirem o mesmo índice.
Isso quer dizer que se duas dessas chaves forem colocadas na tabela, as duas deveriam ser colocadas na mesma posição do vetor (o que não é possível). Essa situação é chamada de *colisão*, e deve ser resolvida.

Infelizmente, a não ser em casos muito especiais, temos que conviver com colisões em uma tabela de dispersão.
Em uma tabela com 10000 posições e uma função de dispersão uniforme (em que a probabilidade de uma chave cair em qualquer dos índices da tabela é a mesma), tem-se aproximadamente 40% de chances de pelo menos um conflito nas primeiras 100 inserções, e praticamente 100% de chances nas primeiras 400 inserções (quando a tabela ainda estaria 96% livre).

Existem duas formas usuais de se resolver conflitos, por encadeamento ou por endereçamento aberto.

##### Resolução de conflito por encadeamento

Nessa solução, em cada posição do vetor, em vez de se colocar um par chave-valor, coloca-se uma lista encadeada (ou outra estrutura de dados) de chave-valor. Quando um dado é inserido, calcula-se o índice correspondente à sua chave e insere-se o par chave-valor na lista correspondente à esse índice.
Quando se busca um dado, calcula-se o índice e faz-se uma busca na lista correspondente.

Tendo uma boa função de dispersão, o número de elementos em cada lista deve ser pequeno. Com taxa de ocupação de 300% (tabela com 3 vezes mais dados que o tamanho do vetor) e função de dispersão uniforme, aproximadamente 65% das listas têm 3 ou menos elementos, e menos de 1% têm 10 ou mais.

##### Resolução de conflito por endereçamento aberto

Nesse caso, todos os dados são colocados no vetor, em uma posição alternativa, caso o índice calculado pela função *hash* já esteja ocupado. Uma forma de calcular a posição alternativa é tendo mais de uma função *hash*. Outra forma é tendo uma função que calcula uma distância, à partir do número da tentativa de se encontrar uma posição, e essa distância é adicionada à posição calculada pela função *hash*. Uma função comum é o próprio número da tentativa (na primeira tentativa, usa o próprio índice *hash*, na segunda, o índice seguinte, na terceira o índice do *hash* + 2 etc). Uma outra é usar o quadrado da distância, visando diminuir a criação de grupos quando a função *hash* não é muito uniforme.

Na busca, tenta-se na posição dada pela função *hash*. Se essa posição estiver vazia, a chave não existe. Se a chave nessa posição for igual à buscada, a busca é bem sucedida. Se a chave for diferente, tenta-se na segunda posição e assim por diante.

Na inserção, procede-se como na busca, e insere-se na primeira posição vazia encontrada. Ou, se uma chave igual for encontrada, trata-se como erro ou como substituição de dado, dependendo do funcionamento da tabela.

Na remoção, não podemos simplesmente considerar como vazia a posição, porque isso interromperia a busca por uma chave conflitante que porventura esteja mais adiante. Uma possibilidade é buscar um dado mais adiante que possa ser movido para essa posição sem causar interrupção de tais buscas. Outra é marcar essa posição como outro tipo de livre, que pode ser usado em uma inserção futura mas não interrompe uma busca. Nesse caso, a busca, inserção e remoção devem operar de acordo quando encontrar uma posição que esteja marcada assim.

#### Função de dispersão

A função de dispersão deve calcular o índice do vetor à partir do valor da chave.
Uma boa função de dispersão espalha uniformemente as chaves entre as posições do vetor.
Isso depende de como as chaves estão distribuídas no universo de chaves possíveis.
Se a distribuição dos valores das chaves for uniforme nesse universo, implementar uma função *hash* não é difícil, as formas mais comuns são por divisão e por multiplicação.
Considerando que a chave possui o valor numérico $c$ e o vetor possua $m$ posições, o índice $h$ pode ser calculado assim:
- por divisão: $h = c \mod m$ (o resto da divisão de $c$ por $m$)
- por multiplicação: $h = m \times (c \times A) mod 1$ (a parte fracionária da multiplicação de um número real $A$ por $c$, multiplicado por $m$).

No caso da divisão, recomenda-se que $m$ seja um número primo; no caso da multiplicação não há esse problema, e sugere-se que $A$ seja, por exemplo, o [número áureo](https://pt.wikipedia.org/wiki/Propor%C3%A7%C3%A3o_%C3%A1urea).

Quando as chaves não têm uma distribuição uniforme, deve-se procurar uma função que uniformize esses valores, e essa escolha é dependente das chaves ligadas ao problema.
No caso de chaves não numéricas, deve-se converter o valor da chave para um número antes de aplicar a função de dispersão. Uma forma de se fazer essa conversão é tratar cada byte da chave como um número, atribuir um peso a cada byte e somar esses valores.

##### Redimensionamento da tabela

O desempenho de uma tabela *hash* está ligado a uma boa função de dispersão e ao fator de carga ou taxa de ocupação, a relação entre o número de elementos presentes na tabela e o tamanho do vetor. Em tabelas com resolução de conflitos interna (endereçamento aberto), é geralmente considerado que acima de 60 e 75% de taxa de ocupação o desempenho começa a sofrer. No cado de resolução encadeada, esse limite em geral é entre 200 e 300%.
Se a taxa de ocupação está muito baixa (abaixo de 25%), tem-se um desperdício de memória.

Por isso, dependendo da aplicação, é interessante que o tamanho da tabela possa ser alterado dinamicamente.
Infelizmente, essa operação não é muito simples, porque a função *hash* geralmente é dependente do tamanho do vetor, o que faz com que a alteração desse tamanho altere o índice onde se espera encontrar determinada chave.
Uma forma de redimensionar a tabela é criar uma nova tabela com o novo tamanho, inserir todos os elementos na nova tabela e depois apagar a original, uma operação que não tem um desempenho muito bom, fazendo com que o programa interrompa suas outras atividades durante esse tempo. Nem todas as aplicações toleram tal comportamento. 
Algumas formas menos drásticas de redimensionamento de uma tabela *hash* foram desenvolvidas, aumentando a complexidade de sua implementação. Uma das mais usadas é a tabela *hash* linear.

No *hash* linear, o valor do *hash* é considerado como um número binário, do qual os últimos bits são considerados. Se o tamanho da tabela for uma potência de 2, é considerado o número de bits que é o logaritmo dessa potência. Se não, são considerados dois números de bits, os necessários para as potências de 2 imediatamente menor e maior que o tamanho. Por exemplo, se a tabela tem tamanho 8, usa-se 3 bits do valor *hash* para indexar o vetor. Se o tamanho for 10, usa-se 3 ou 4 bits. A forma de escolha é: se o valor com 3 bits for inferior à diferença entre o tamanho da tabela e a potência de 2 inferior a esse tamanho (no exemplo, 10-8=2), usa o valor de 4 bits, senão usa o valor de 3 bits.

Com 8 entradas e 3 bits, os índices são números de 0 a 7. Em binário:
```
  000 001 010 011 100 101 110 111
```
Se a tabela aumentar para 9 entradas, necessitamos do número 8, que é representado em 4 bits (`1000`). Os 3 últimos bits do 8 são os mesmos últimos bits do 0. Então, chaves que tenham sido colocadas na tabela com o índice `000` podem estar em conflito com novas chaves com o valor `1000`. As chaves que estão no `000` devem ser reanalizadas com 4 bits, as que são `0000` continuam onde estão, as que são `1000` devem ser colocadas no novo índice. Nenhuma outra chave é alterada, porque não estão em conflito. A chave `000` é renomeada para `0000`. As chaves passam a ser:
```
  0000 001 010 011 100 101 110 111 1000
```
Se uma nova chave é inserida (ou buscada), calcula-se o valor dos últimos 3 bits (entre 0 e 7). Se estiver entre 1 e 7, ele está representado com 3 bits na tabela, se for 0, recalcula-se com 4 bits, para diferenciar entre `0000` e `1000`.

Com 10 entradas, os índices `001` são quebrados em `0001` e `1001` (1 e 9):
```
0000 0001 010 011 100 101 110 111 1000 1001
```
Agora, calcula-se os 3 bits do *hash*, e se o valor for menor que 2 (0 ou 1), calcula-se de novo com 4 bits (que vai resultar em 0 ou 1 ou 8 ou 9, que são as entradas de 4 bits).

Para a tabela diminuir de tamanho, faz-se o contrário, junta-se os elementos da última entrada na entrada que tem o mesmo valor com um bit a menos.
A tabela aumenta ou diminui um tamanho por vez.
A decisão de aumentar a tabela é tomada na inclusão, se o fator de ocupação ficar maior que um limite superior, e diminui na remoção, se ficar menor que um limite inferior.
