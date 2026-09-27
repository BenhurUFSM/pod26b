# Busca em memória secundária

## Índices multidimensionais

Um índice do tipo hash permite que se realize uma busca por um registro à partir de sua chave. Os registros vizinhos, no entanto, só têm como semelhança com o registro procurado o fato de terem um valor de *hash* igual ou próximo, o que quase nunca é útil.

Uma árvore B+, por outro lado, mantém os índices ordenados pelo valor da chave, permitindo encontrar registros com valores próximos de chave sem necessitar realizar novo percurso na chave e mesmo sem saber o valor da chave. Isso permite também realizar consultas de intervalo, onde se quer obter os (potencialmente vários) registros que têm o valor da chave entre dois valores limite.

Nenhum deles resolve consultas que envolvem mais de uma chave, como identificar os registros de pessoas que têm idade entre 20 e 30 anos e que moram no interior de São Paulo, em uma base de dados da receita federal, por exemplo, ou as pessoas fichadas pela polícia que moravam a menos de 50km do centro de Santa Maria em 2023.

Com arquivos indexados em árvores B+, com um índice por data de nascimento e outro por endereço, daria com uma pesquisa de intervalo no índice da data de nascimento selecionar as pessoas entre 20 e 30 anos, e uma pesquisa por endereço permitiria identificar os que moram no interior de São Paulo, mas seria necessário realizar a interseção entre esses dois conjuntos para fazer a consulta, o que é uma operação cara.

Um índice multidimensional é um índice que indexa em mais de um campo (mais de uma dimensão) ao mesmo tempo, para apoiar consultas como essas.

### Índices de bitmap

Um índice de bitmap é um tipo de índice que contém um bit por registro de arquivo de dados, que diz se tal registro satisfaz ou não determinado atributo.
É um índice relativamente pequeno, porque mantém um só bit por registro, mas exige que:
- um registro seja identificável por um número pequeno, geralmente sua posição sequencial no arquivo.
- o atributo que se deseja seja representável por um bit. Alguns campos de um registro são naturalmente assim, outros podem ser divididos em faixas, com um bit representando cada faixa (por exemplo, salário entre 3 e 5 mil), e usando-se alguns índices de bitmap para cobrir todos as faixas.

No exemplo acima, poderia ter alguns índices para faixas de idade, e se usaria o que representa a faixa entre 20 e 30 anos, e alguns para regiões de moradia, e se usaria aqueles que representam o interior de São Paulo.
A interseção entre esses índices seria realizada com uma operação binária AND entre esses índices, obtendo o conjunto de registros desejado.

Se os índices não corresponderem exatamente ao que se está buscando (por exemplo, se a consulta fosse relativa a idades entre 22 e 32) anos e o houvesse índices do tipo 0-10, 10-20 etc, seria necessário usar mais de um índice e depois fazer uma filtragem nos registros que foram obtidos.

### Arquivos de grade

### *Hash* particionado

### Índices de várias chaves

### Árvore kD

### Árvore de quadrante

### Árvore R

