A árvore está inicialmente vazia. Com a inclusão de `P`, a raiz é transformada em um nó folha. As inclusões seguintes de `M` e `G` são colocadas no mesmo nó, reordenando a cada vez. Ao final temos a situação abaixo, com o nó cheio. Nesse desenho, os nós folha estão sendo representados sem os links para os registros de dados correspondentes às chaves, nem o link que interliga os nós folha.
```mermaid
block
  block:f1:3 f1a("G") f1b("M") f1c("P") end
```
Com a inclusão de `V`, o nó estoura. Um novo nó folha é criado, e o total de dados é dividido assim: metade dos dados permanece no nó já existente (`G` e `M`) e o restante (`P` e `V`) é colocado no novo nó. O primeiro valor do novo nó (`P`), juntamente com a referência a esse novo nó é enviado para ser colocado no nó superior. Como o nó que foi quebrado é a raiz, não existe nó superior. É criado um novo nó intermediário, que passa a ser a nova raiz:
```mermaid
block
  columns 7
  block:rl1 space end P block:rl2 space end rb["—"] block:rl3 space end rc["—"] block:rl4 space end
  space:7

  block:f1:3 f1a("G") f1b("M") f1c("—") end space
  block:f2:3 f2a("P") f2b("V") f2c("—") end

  rl1-->f1
  rl2-->f2
```
A inclusão de `T` enche o nó folha da direita, e a inclusão de `A` enche o nó folha da esquerda.
```mermaid
block
  columns 7
  block:rl1 space end P block:rl2 space end rb["—"] block:rl3 space end rc["—"] block:rl4 space end
  space:7

  block:f1:3 f1a("A") f1b("G") f1c("M") end space
  block:f2:3 f2a("P") f2b("T") f2c("V") end

  rl1-->f1
  rl2-->f2
```
A inclusão de `E` causa o estouro da folha à esquerda e a criação de uma nova folha. Os valores envolvidos (`A`, `E`, `G` e `M`) são distribuídos como antes: metade na folha existente (`A` e `E`), o restante na nova (`G` e `M`). O primeiro valor da nova folha (`G`) e a referência ao novo nó são enviados para o nó acima (a raiz), que tem espaço para acomodá-los:
```mermaid
block
  columns 11
  space:2
  block:rl1 space end ra["G"] block:rl2 space end rb["P"] block:rl3 space end rc["—"] block:rl4 space end
  space:2
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

* * *
```mermaid
block
columns 15
space:4
block:rl1 space end ra block:rl2 space end rb block:rl3 space end rc block:rl4 space end
space:4
space:15
block:l1 space end a block:l2 space end b block:l3 space end c block:l4 space end
space
block:dl1 space end d block:dl2 space end e block:dl3 space end f block:dl4 space end
%%dl1(("+")) d:2 dl2(("+")) e["e"]:2 dl3(("+")) f:2 dl4(("+"))
space:15
block:z:2 x y w end
space
block:zzz:3 xxx yyy qww end
space
block:zz:3 xx yy ww end
rl1-->b
l1-->z
l4-->zzz
l2-->z
dl2-->zz
```
