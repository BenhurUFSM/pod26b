A árvore está inicialmente vazia. Com a inclusão de `P`, a raiz é transformada em um nó folha. As inclusões seguintes de `M` e `G` são colocadas no mesmo nó, reordenando a cada vez. Ao final temos a situação abaixo, com o nó cheio. Nesse desenho, os nós folha estão sendo representados sem os links para os registros de dados correspondentes às chaves, nem o link que interliga os nós folha.
```mermaid
block
  block G M P end
```
Com a inclusão de `V`, o nó estoura, e não sem outro nó para onde enviar dados excedentes. Um novo nó folha é criado, e o total de dados é dividido assim: metade dos dados permanece no nó já existente e o restante é colocado no novo nó. O primeiro valor do novo nó, juntamente com a referência a esse novo nó é enviado para ser colocado no nó superior. Como o nó que foi quebrado é a raiz, não existe nó superior. É criado um novo nó intermediário, que passa a ser a nova raiz:
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
