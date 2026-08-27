# pod26b

Disciplina de Pesquisa e Ordenação de Dados, 2º semestre de 2026
ELC1068 DLSC/CT/UFSM\
professor: Benhur Stein ([benhur+pod26b@inf.ufsm.br](mailto:benhur%2bpod26b@inf.ufsm.br))\
terças e quintas, 14h30, turma SI\
sala 255, anexo A do CT\
alunos: [planilha](https://docs.google.com/spreadsheets/d/1JzLYYH0k_aYOZ6918-5DghNZpLkP6mJTy2nJ5AVTGeM/edit?usp=sharing)

arquivo [rascunho](https://docs.google.com/document/d/1N21tqLkZEnALHvoJXw3apZegu-AAAba2f8wwZH7tIng/edit?usp=sharing)

## Desenvolvimento da disciplina

Os canais de comunicação entre o professor e os alunos são:
- as aulas presenciais,
- esta página,
- e-mail (ver endereço acima),
- conversa individual, manda um mail que marcamos.

O e-mail pode ser usado para o envio de perguntas, exercícios, reclamações, sugestões, críticas e o que mais for, durante todo o período em que durar a disciplina.

Esta página concentra os assuntos vistos em aula, a descrição de exercícios e trabalhos, avaliações, etc. Ela será atualizada durante todo o semestre. Ela é o canal oficial de informações sobre a disciplina. Todos os alunos devem olhar o que está aqui periodicamente (recomendo habilitar notificações no github).

A disciplina aborda o seguinte:
- ordenação de dados em memória interna
- ordenação de dados em memória externa
- busca de dados em memória interna
- busca de dados em memória externa
- compressão de dados

A disciplina é extensa, e 50% da carga horária é prática, na qual os alunos devem desenvolver trabalhos de programação.
Para realizar essa prática, é necessário acesso a um computador e a um ambiente de desenvolvimento de programas, com (pelo menos) um editor de textos e um compilador C.
O desenvolvimento dos trabalhos deve ser registrado em um servidor git, então é necessário também acesso a um cliente git e uma conta em um servidor git.
A universidade dispõe de laboratórios com computadores para acesso aos alunos, com todo o software necessário instalado; consulte o NCC caso necessite.

Os trabalhos serão avaliados em um ambiente linux, usando o compilador `gcc`. Os programas entregues pelos alunos devem compilar e executar corretamente nesse ambiente.

A principal forma de avaliação dos alunos será através do desenvolvimento de pequenos (ou médios) programas de computador, a serem disponibilizados para o professor.
A descrição, prazo e forma de entrega dos trabalhos será disponibilizada nesta página.

O desenvolvimento dos trabalhos deve ser feito pelo aluno, de forma individual.
Quando entrega um trabalho, o aluno está implicitamente dizendo "eu que fiz".
A entrega de um trabalho que não foi feito pelo aluno constitui tentativa de fraude.
Recomendo desenvolver os trabalhos usando um editor sem suporte a ajuda de codificação, ou a desativar esse tipo de ajuda.
Em geral, para o que se espera nesta disciplina, a ajuda automática dos ambientes de programação atrapalha mais do que ajuda.
A ajuda (não código) de colegas é incentivada.

Cada aluno deve ter uma conta git (codeberg, github, gitlab etc) com um repositório para a disciplina, ao qual o professor tenha acesso de leitura. O endereço de acesso a esse repositório deve ser cadastrado [neste link](https://docs.google.com/forms/d/e/1FAIpQLSf6tS2xIksZFwHSidszwuHs_iNflRVRNyRpoCHYbcEW5W7AdA/viewform?usp=publish-editor).
É nesse repositório que o desenvolvimento dos trabalhos deve acontecer, com commits e pushs frequentes.
A atividade do repositório pode ser considerada na avaliação e na presença em aula.

Parte da avaliação será realizada pelo aluno. Cada aluno deve entregar uma autoavaliação no final do semestre descrevendo o seu desenvolvimento durante o semestre e contendo uma sugestão de nota.
Cada aluno será entrevistado individualmente pelo professor. A nota final será então calculada, considerando:
- o estado de cada trabalho na data definida de entrega
- a versão final dos trabalhos
- a auto-avaliação
- a entrevista
- as provas.

##  Aulas 

Os assuntos vistos em aula serão listados aqui.

|    N |   data | assunto
| ---: | -----: | :--------
|    1 |   4ago | apresentação, [algoritmos simples de ordenação](Assuntos/ord-int-simples.md)
|    2 |   6ago | [algoritmos mais eficientes de ordenação](Assuntos/ord-int-eficientes.md)
|    3 |  11ago | [algoritmos de ordenação por distribuição](Assuntos/ord-int-distribuicao.md)
|    4 |  13ago | [complexidade de algoritmos](Assuntos/complexidade.md)
|    5 |  18ago | complexidade de algoritmos, cont
|    6 |  20ago | [ordenação em memória externa](Assuntos/ord-ext.md)
|    7 |  25ago | [busca, árvore AVL](Assuntos/busca.md)
|    8 |  27ago | [árvore AA](Assuntos/busca-aa.md)

## Exercícios, trabalhos

|     N |     prazo | descrição
| ----: | --------: | :-----------
| t1    | ~12~17ago     | [medições de tempo de algoritmos de ordenação](Trabalhos/t1.md)
| t2    |               | [medições de tempo de árvores binárias de busca](Trabalhos/t2)

