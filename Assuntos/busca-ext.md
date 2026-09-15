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
  space:9
  block:rl1 space end ra["I"] block:rl2 space end rb["—"] block:rl3 space end rc["—"] block:rl4 space end
  space:5
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

  rl1-->i1c
  rl2-->i2a
  i1l1-->f1
  i1l2-->f7
  i1l3-->f5
  i1l4-->f3
  i2l1-->f6
  i2l2-->f4
  i2l3-->f2
```
