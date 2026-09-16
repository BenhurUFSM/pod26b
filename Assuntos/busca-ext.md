## Árvore B+

Uma árvore B+ é constituída de nós, que podem ser 'nó folha' ou 'nó intermediário'. Cada nó tem um conjunto de até $N$ links e até $N-1$ chaves.

No caso de um nó folha, tem um link associado a cada chave, que diz qual o registro no arquivo de dados que corresponde a essa chave. O link restante é usado para apontar para o nó folha seguinte. As chaves são mantidas em ordem dentro de um nó, e os valores das chaves em um nó são todos menores que os valores das chaves no nó seguinte. Isso permite o percurso do arquivo por ordem de chave.

### Busca

A busca por uma chave em uma árvore B+ inicia pelo nó raiz.
No nó corrente:
- se o nó for folha, busca-se a chave entre as chaves desse nó. Se for encontrada, o link correspondente à chave é o resultado da busca. Se não for encontrado, a chave não existe na árvore.
- se o nó for intermediário, compara-se a chave buscada com os valores de chaves no nó, e segue-se para o nó correspondente ao primeiro link com uma chave maior ou igual à chave buscada. Se todas as chaves do nó forem menores que a buscada, segue-se para o nó do último link. Por exemplo, se o nó contiver as chaves `G`, `K` e `P`, e a chave buscada for `J`, a segunda chave (`K`) é a primeira que é maior ou igual a `J`, então continua-se no nó correspondente ao segundo link. Se a busca fosse pela chave `Q`, que é maior que todas as chaves do nó, o próximo nó seria o referenciado pelo último link do nó.

### Inserção

A inserção é sempre realizada em um nó folha, o mesmo encontrado quando se faz a busca. Se esse nó tem espaço para mais uma chave, basta inserir essa chave e o link para o arquivo de dados que contém o dado correspondente e a inserção está feita.
Se o nó já está cheio, um novo nó deve ser alocado, e o conjunto de chaves é dividido entre os dois nós. O nó pai deve ser alterado para incluir um ponteiro para esse novo nó, juntamente com o valor de uma chave para se poder decidir entre os dois nós. Uma chave adequada é aquela que ficou no início do novo nó.

Essa inserção no nó pai pode provocar a necessidade de divisão desse nó, e isso pode acontecer em cascata até o nó raiz.
Abaixo está um sequência de inserções em uma árvore inicialmente vazia, que exemplifica esses casos. As chaves são caracteres, e são inseridos, em ordem, os caracteres `PMGVTAEBJKFHDICLRNUQOS`.

A árvore está inicialmente vazia. Com a inclusão de `P`, a raiz é transformada em um nó folha. As inclusões seguintes de `M` e `G` são colocadas no mesmo nó, reordenando a cada vez. Ao final temos a situação abaixo, com o nó cheio. Nesse desenho, os nós folha estão sendo representados sem os links para os registros de dados correspondentes às chaves, nem o link que interliga os nós folha.
```mermaid
block
  block:f1:3 f1a("G") f1b("M") f1c("P") end
```
Com a inclusão de `V`, o nó estoura. Um novo nó folha é criado, e o total de dados é dividido assim: metade dos dados permanece no nó já existente (`G` e `M`) e o restante (`P` e `V`) é colocado no novo nó. O primeiro valor do novo nó (`P`), juntamente com a referência a esse novo nó é enviado para ser colocado no nó superior. Como o nó que foi quebrado é a raiz, não existe nó superior. É criado um novo nó intermediário, que passa a ser a nova raiz:
```mermaid
block
  columns 8
  space
  block:rl1 space end P block:rl2 space end rb["—"] block:rl3 space end rc["—"] block:rl4 space end
  space:8

  block:f1:3 f1a("G") f1b("M") f1c("—") end space
  block:f2:3 f2a("P") f2b("V") f2c("—") end
  space

  rl1-->f1
  rl2-->f2
```
A inclusão de `T` enche o nó folha da direita, e a inclusão de `A` enche o nó folha da esquerda.
```mermaid
block
  columns 8
  space
  block:rl1 space end P block:rl2 space end rb["—"] block:rl3 space end rc["—"] block:rl4 space end
  space:8

  block:f1:3 f1a("A") f1b("G") f1c("M") end space
  block:f2:3 f2a("P") f2b("T") f2c("V") end
  space

  rl1-->f1
  rl2-->f2
```
A inclusão de `E` causa o estouro da folha à esquerda e a criação de uma nova folha. Os valores envolvidos (`A`, `E`, `G` e `M`) são distribuídos como antes: metade na folha existente (`A` e `E`), o restante na nova (`G` e `M`). O primeiro valor da nova folha (`G`) e a referência ao novo nó são enviados para o nó acima (a raiz), que tem espaço para acomodá-los:
```mermaid
block
  columns 11
  space:3
  block:rl1 space end ra["G"] block:rl2 space end rb["P"] block:rl3 space end rc["—"] block:rl4 space end
  space:1
  space:11

  block:f1:3 f1a("A") f1b("E") f1c("—") end space
  block:f3:3 f3a("G") f3b("M") f3c("—") end space
  block:f2:3 f2a("P") f2b("T") f2c("V") end

  rl1-->f1
  rl2-->f3
  rl3-->f2
```
As inclusões de `B` e `J` enchem os nós folha da esquerda. A inclusão de `K` causa o estouro do nó folha com `G`, `J` e `M`, que fica com `G` e `J`, indo `K` e `M` para um novo nó, e subindo `K` e a referência ao novo nó para a raiz:
```mermaid
block
  columns 15
  space:4
  block:rl1 space end ra["G"] block:rl2 space end rb["K"] block:rl3 space end rc["P"] block:rl4 space end
  space:4
  space:15

  block:f1:3 f1a("A") f1b("B") f1c("E") end space
  block:f3:3 f3a("G") f3b("J") f3c("—") end space
  block:f4:3 f4a("K") f4b("M") f4c("—") end space
  block:f2:3 f2a("P") f2b("T") f2c("V") end

  rl1-->f1
  rl2-->f3
  rl3-->f4
  rl4-->f2
```
A inserção de `F` causa o estouro do nó `ABE`, que é quebrado em `AB` e `EF`, subindo `E` e um link para o novo nó. Não tem espaço no nó raiz para essa nova informação, e o nó raiz não tem irmão e tem que ser quebrado em dois nós intermediários. A quebra de nós intermediários é um pouco diferente, metade das chaves fica no nó esquerdo (`EG`); a chave seguinte (`K`) sobe junto com o link para o novo nó e as chaves restantes (`P`) vão para o novo nó.
Não existe nó acima (já que estamos quebrando a raiz), então a chave que sobe é acomodada em um novo nó raiz:
```mermaid
block
  columns 17
  space:8
  block:rl1 space end ra["K"] block:rl2 space end rb["—"] block:rl3 space end rc["—"] block:rl4 space end
  space:2
  space:17

  space:2
  block:i1l1 space end i1a["E"] block:i1l2 space end i1b["G"] block:i1l3 space end i1c["—"] block:i1l4 space end
  space
  block:i2l1 space end i2a["P"] block:i2l2 space end i2b["—"] block:i2l3 space end i2c["—"] block:i2l4 space end
  space:17

  block:f1:3 f1a("A") f1b("B") f1c("—") end
  block:f5:3 f5a("E") f5b("F") f5c("—") end
  block:f3:3 f3a("G") f3b("J") f3c("—") end
  block:f4:3 f4a("K") f4b("M") f4c("—") end
  block:f2:3 f2a("P") f2b("T") f2c("V") end
  space:2

  rl1-->i1c
  rl2-->i2a
  i1l1-->f1
  i1l2-->f5
  i1l3-->f3
  i2l1-->f4
  i2l2-->f2
```
A inclusão de `H` e `D` enche os nós `ABD` e `GHJ`. A inclusão de `I` causa o estouro deste último e sua divisão em `GH` e `IJ`, subindo `I` para o nó `EG`, que vira `EGI`:
```mermaid
block
  columns 20
  space:9
  block:rl1 space end ra["K"] block:rl2 space end rb["—"] block:rl3 space end rc["—"] block:rl4 space end
  space:4
  space:20

  space:3
  block:i1l1 space end i1a["E"] block:i1l2 space end i1b["G"] block:i1l3 space end i1c["I"] block:i1l4 space end
  space:3
  block:i2l1 space end i2a["P"] block:i2l2 space end i2b["—"] block:i2l3 space end i2c["—"] block:i2l4 space end
  space:20

  block:f1:3 f1a("A") f1b("B") f1c("D") end
  block:f5:3 f5a("E") f5b("F") f5c("—") end
  block:f3:3 f3a("G") f3b("H") f3c("—") end
  block:f6:3 f6a("I") f6b("J") f6c("—") end
  block:f4:3 f4a("K") f4b("M") f4c("—") end
  block:f2:3 f2a("P") f2b("T") f2c("V") end
  space:2

  rl1-->i1c
  rl2-->i2a
  i1l1-->f1
  i1l2-->f5
  i1l3-->f3
  i1l4-->f6
  i2l1-->f4
  i2l2-->f2
```
A inserção de `C` causa o estouro do nó `ABD`, que é dividido em `AB` e `CD`, subindo `C` e o link para o novo nó. Essa informação não cabe no nó acima (`EGI`). O nó acima tem um irmão (`P`) com espaço, então a informação dos nós é distribuída entre eles. Nessa redistribuição, deve-se considerar, além dos valores constantes nos dois nós que serão mesclados, também o valor da chave intermediária que está no nó pai (`K`). A redistribuição então é dos valores `CEGIKP`, em que o valor do meio (`G` ou `I`) sobe para o pai, os da esquerda ficam no nó da esquerda e os da direita no nó da direita. Subindo `I`, resulta na árvore abaixo.
```mermaid
block
  columns 21
  space:10
  block:rl1 space end ra["I"] block:rl2 space end rb["—"] block:rl3 space end rc["—"] block:rl4 space end
  space:4
  space:21

  space:2
  block:i1l1 space end i1a["C"] block:i1l2 space end i1b["E"] block:i1l3 space end i1c["G"] block:i1l4 space end
  space:5
  block:i2l1 space end i2a["K"] block:i2l2 space end i2b["P"] block:i2l3 space end i2c["—"] block:i2l4 space end
  space:21

  block:f1:3 f1a("A") f1b("B") f1c("—") end
  block:f7:3 f7a("C") f7b("D") f7c("—") end
  block:f5:3 f5a("E") f5b("F") f5c("—") end
  block:f3:3 f3a("G") f3b("H") f3c("—") end
  block:f6:3 f6a("I") f6b("J") f6c("—") end
  block:f4:3 f4a("K") f4b("M") f4c("—") end
  block:f2:3 f2a("P") f2b("T") f2c("V") end

  rl1-->i1l4
  rl2-->i2l1
  i1l1-->f1
  i1l2-->f7
  i1l3-->f5
  i1l4-->f3
  i2l1-->f6
  i2l2-->f4
  i2l3-->f2
```
A inclusão de `LRNUQOS` fica como exercício. A árvore resultante está abaixo:
```mermaid
block
  columns 23
  space:10
  block:rl1 space end ra["I"] block:rl2 space end rb["P"] block:rl3 space end rc["—"] block:rl4 space end
  space:6
  space:23

  block:i1l1 space end i1a["C"] block:i1l2 space end i1b["E"] block:i1l3 space end i1c["G"] block:i1l4 space end
  space
  block:i2l1 space end i2a["K"] block:i2l2 space end i2b["M"] block:i2l3 space end i2c["—"] block:i2l4 space end
  space
  block:i3l1 space end i3a["R"] block:i3l2 space end i3b["T"] block:i3l3 space end i3c["—"] block:i3l4 space end
  space:23

  block:f1:2 f1a("A") f1b("B") f1c("—") end
  block:f7:2 f7a("C") f7b("D") f7c("—") end
  block:f5:2 f5a("E") f5b("F") f5c("—") end
  block:f3:2 f3a("G") f3b("H") f3c("—") end
  block:f6:2 f6a("I") f6b("J") f6c("—") end
  block:f4:2 f4a("K") f4b("L") f4c("—") end
  block:f8:2 f8a("M") f8b("N") f8c("O") end
  space:2
  block:f2:2 f2a("P") f2b("Q") f2c("—") end
  block:fa:2 faa("R") fab("S") fac("—") end
  block:f9:2 f9a("T") f9b("U") f9c("V") end
  space:1

  rl1-->i1l4
  rl2-->i2b
  rl3-->i3l1
  i1l1-->f1
  i1l2-->f7
  i1l3-->f5
  i1l4-->f3
  i2l1-->f6
  i2l2-->f4
  i2l3-->f8
  i3l1-->f2
  i3l2-->fa
  i3l3-->f9
```

### Remoção

Na remoção, a chave deve ser encontrada em um nó folha (senão, não há nada a ser removido). A chave é então removida do nó folha e, caso o número de chaves restantes seja pelo menos o mínimo aceito para um nó folha, a remoção está feita.

Caso restem menos chaves que o permitido no nó folha, deve-se observar um nó vizinho irmão e:
- se as chaves nos dois nós forem suficientes para serem distribuídas entre os dois nós (de forma que ambos fiquem pelo menos com o mínimo permitido), é feito um remanejo;
- se o número de chaves for insuficiente, deve ser feita uma fusão.

No remanejo, as chaves são redistribuídas entre os nós, de forma a ficar aproximadamente metade em cada, e o nó pai é alterado para substituir o valor intermediário pelo valor que ficou no início do nó da direita. Após o remanejo, a remoção está completa.

Na fusão, as chaves do nó da direita são transferidas para o nó da esquerda, e o nó da direita é liberado. O ponteiro para o nó da direita e o valor correspondente a ele devem ser removidos do nó pai. Essa remoção no nó pai pode levar à necessidade de um remanejo ou fusão no nó pai. Todos os nós até a raiz podem ser afetados, e se a raiz ficar sem nenhuma chave, é removida e seu único filho se torna raiz.

O remanejo e fusão em um nó intermediário deve levar em consideração o valor contido no nó pai, entre os ponteiros para os dois nós envolvidos.

Considerando a árvore final do exemplo acima, se removermos a chave (`N`), ela será simplesmente removida do nó raiz que a contém, transformando `MNO` em `MO`.
Se após isso removermos `S`, o nó `RS` ficará somente com `R`, o que não é permitido para um nó folha. O irmão direito desse nó tem `TUV`, sendo possível remanejar, ficando `RT` no nó esquerdo e `UV` no direito. No nó pai, a chave `T` deve ser a lterada para `U`, resultando na árvore abaixo.
```mermaid
block
  columns 23
  space:10
  block:rl1 space end ra["I"] block:rl2 space end rb["P"] block:rl3 space end rc["—"] block:rl4 space end
  space:6
  space:23

  block:i1l1 space end i1a["C"] block:i1l2 space end i1b["E"] block:i1l3 space end i1c["G"] block:i1l4 space end
  space
  block:i2l1 space end i2a["K"] block:i2l2 space end i2b["M"] block:i2l3 space end i2c["—"] block:i2l4 space end
  space
  block:i3l1 space end i3a["R"] block:i3l2 space end i3b["U"] block:i3l3 space end i3c["—"] block:i3l4 space end
  space:23

  block:f1:2 f1a("A") f1b("B") f1c("—") end
  block:f7:2 f7a("C") f7b("D") f7c("—") end
  block:f5:2 f5a("E") f5b("F") f5c("—") end
  block:f3:2 f3a("G") f3b("H") f3c("—") end
  block:f6:2 f6a("I") f6b("J") f6c("—") end
  block:f4:2 f4a("K") f4b("L") f4c("—") end
  block:f8:2 f8a("M") f8b("O") f8c("—") end
  space:2
  block:f2:2 f2a("P") f2b("Q") f2c("—") end
  block:fa:2 faa("R") fab("T") fac("—") end
  block:f9:2 f9a("U") f9b("V") f9c("—") end
  space:1

  rl1-->i1l4
  rl2-->i2b
  rl3-->i3l1
  i1l1-->f1
  i1l2-->f7
  i1l3-->f5
  i1l4-->f3
  i2l1-->f6
  i2l2-->f4
  i2l3-->f8
  i3l1-->f2
  i3l2-->fa
  i3l3-->f9
```
Se agora removermos o `Q`, o nó `PQ` ficará somente com `P`. Seu vizinho esquerdo não é irmão, e seu vizinho direito não tem chaves suficienter para remanejo, então será feita uma fusão entre `P` e `RT`, resultando em um nó com `PRT` e liberando o nó `RT`. O ponteiro para `RT` no nó pai deve ser removido, junto com a chave `R`. O nó pai fica só com a chave `U`, o que é permitido para um nó intermediário. A árvore fica:
```mermaid
block
  columns 23
  space:10
  block:rl1 space end ra["I"] block:rl2 space end rb["P"] block:rl3 space end rc["—"] block:rl4 space end
  space:6
  space:23

  block:i1l1 space end i1a["C"] block:i1l2 space end i1b["E"] block:i1l3 space end i1c["G"] block:i1l4 space end
  space
  block:i2l1 space end i2a["K"] block:i2l2 space end i2b["M"] block:i2l3 space end i2c["—"] block:i2l4 space end
  space
  block:i3l1 space end i3a["U"] block:i3l2 space end i3b["—"] block:i3l3 space end i3c["—"] block:i3l4 space end
  space:23

  block:f1:2 f1a("A") f1b("B") f1c("—") end
  block:f7:2 f7a("C") f7b("D") f7c("—") end
  block:f5:2 f5a("E") f5b("F") f5c("—") end
  block:f3:2 f3a("G") f3b("H") f3c("—") end
  block:f6:2 f6a("I") f6b("J") f6c("—") end
  block:f4:2 f4a("K") f4b("L") f4c("—") end
  block:f8:2 f8a("M") f8b("O") f8c("—") end
  space:2
  block:f2:2 f2a("P") f2b("R") f2c("T") end
  block:f9:2 f9a("U") f9b("V") f9c("—") end
  space:3

  rl1-->i1l4
  rl2-->i2b
  rl3-->i3l1
  i1l1-->f1
  i1l2-->f7
  i1l3-->f5
  i1l4-->f3
  i2l1-->f6
  i2l2-->f4
  i2l3-->f8
  i3l1-->f2
  i3l2-->f9
```
Nessa configuração, se removemos o `P`, ele é simplesmente retirado do nó, restando `RT`. Removendo então o `R`, o nó `T` deverá ser fundido ao nó `UV`, resultando em `TUV`. O nó que tinha `UV` é removido, e seu link e a chave `U` são removidos do seu pai, que fica sem chaves. Seu irmão esquerdo tem 2 chaves (`K` e `M`), então o remanejo pode ser feito. As chaves envolvidas no remanejo são `K`, `M` e `P` (que está no pai deles, a raiz). Fica `K` na esquerda, `P` na direita e `M` na raiz:
```mermaid
block
  columns 23
  space:10
  block:rl1 space end ra["I"] block:rl2 space end rb["M"] block:rl3 space end rc["—"] block:rl4 space end
  space:6
  space:23

  block:i1l1 space end i1a["C"] block:i1l2 space end i1b["E"] block:i1l3 space end i1c["G"] block:i1l4 space end
  space
  block:i2l1 space end i2a["K"] block:i2l2 space end i2b["—"] block:i2l3 space end i2c["—"] block:i2l4 space end
  space
  block:i3l1 space end i3a["P"] block:i3l2 space end i3b["—"] block:i3l3 space end i3c["—"] block:i3l4 space end
  space:23

  block:f1:2 f1a("A") f1b("B") f1c("—") end
  block:f7:2 f7a("C") f7b("D") f7c("—") end
  block:f5:2 f5a("E") f5b("F") f5c("—") end
  block:f3:2 f3a("G") f3b("H") f3c("—") end
  block:f6:2 f6a("I") f6b("J") f6c("—") end
  block:f4:2 f4a("K") f4b("L") f4c("—") end
  space:4
  block:f8:2 f8a("M") f8b("O") f8c("—") end
  block:f2:2 f2a("T") f2b("U") f2c("V") end
  space:3

  rl1-->i1l4
  rl2-->i2b
  rl3-->i3l1
  i1l1-->f1
  i1l2-->f7
  i1l3-->f5
  i1l4-->f3
  i2l1-->f6
  i2l2-->f4
  i3l1-->f8
  i3l2-->f2
```
#### Exercícios

Faça as inserções que faltaram, e continue as remoções, sempre da maior chave, até esvaziar a árvore.
