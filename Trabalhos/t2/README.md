
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

Breve disponibilizo um tanto mais de código.

