# Guia de leitura do artigo: o que cada parte quer dizer

Este guia acompanha o artigo [03-Artigo-Gerencia-de-Memoria-Virtual.md](03-Artigo-Gerencia-de-Memoria-Virtual.md) e segue a mesma numeração de seções. Para cada trecho difícil, aparece a frase do artigo (em citação) e, logo abaixo, o que ela quer dizer em linguagem simples.

No fim há um **glossário rápido** com os termos técnicos em ordem alfabética.

---

## Antes de começar: uma comparação que serve para o artigo inteiro

Quase tudo na memória virtual fica mais fácil de entender pensando em uma pessoa trabalhando numa mesa pequena, com uma estante grande no fundo da sala.

| No computador | Na comparação |
| --- | --- |
| Memória principal (RAM) | A **mesa de trabalho**: rápida de usar, mas pequena |
| Memória secundária (HD ou SSD) | A **estante**: cabe muito mais, mas é preciso levantar e ir buscar |
| Processo (programa em execução) | Uma pessoa trabalhando num projeto |
| Página | Uma **folha** de fichário; todas têm o mesmo tamanho |
| *Frame* | Um **espaço na mesa** onde cabe exatamente uma folha |
| Tabela de páginas | O **índice** que diz se cada folha está na mesa (e em qual espaço) ou na estante |
| MMU | Um **assistente** que consulta o índice toda vez que a pessoa pede uma folha |
| *Page fault* | A folha pedida **não está na mesa**: alguém precisa ir até a estante buscá-la |
| TLB | Um **post-it** com a posição das folhas mais usadas, para não abrir o índice toda vez |
| *Thrashing* | A mesa é tão pequena que a pessoa **passa o dia indo e voltando da estante** e não trabalha |

---

## Suas perguntas sobre a seção 3.4

> **Global:** qualquer página da memória é candidata, menos as do núcleo, que ficam bloqueadas.

### Que "núcleo" é esse?

É o **núcleo do sistema operacional**, também chamado de ***kernel***. É a parte central do Windows, do Linux ou do Android: fica sempre em execução e controla os processos, a memória, os discos, o teclado e a rede.

**Não tem relação com os núcleos (*cores*) do processador.** A palavra em português é a mesma, mas são coisas diferentes. Aqui, "núcleo" é um software, não uma peça do processador.

### Por que as páginas do núcleo ficam bloqueadas?

"Bloqueada" quer dizer **fixada na RAM**: a página nunca é escolhida para sair da memória. Não quer dizer travada nem com erro. Há dois motivos principais:

1. **Quem traz páginas do disco é o próprio núcleo.** A rotina que trata o *page fault* é código do núcleo. Se ela fosse para o disco, ocorreria um *page fault* e não haveria ninguém na memória para tratá-lo. Seria como trancar a chave dentro da caixa que ela abre. O livro faz exatamente essa pergunta no Exercício 9 do Capítulo 10.
2. **Partes do núcleo precisam responder na hora.** Quando você aperta uma tecla ou chega um dado pela rede, o núcleo precisa reagir imediatamente. Não dá para esperar uma leitura no disco para isso.

Na prática, os sistemas atuais bloqueiam as partes **essenciais** do núcleo. Partes menos críticas podem ir para o disco: o Windows, por exemplo, separa uma área de memória do núcleo que pode ser paginada de outra que nunca é.

### O que são as "páginas modificadas"? Incluem informações de cadastro? Textos livres?

**Sim, podem incluir.** Página modificada é qualquer página de **dados** do processo que o programa alterou desde que ela foi carregada na RAM. Exemplos:

- o texto que você digitou num editor e **ainda não salvou**;
- os campos de um **cadastro** preenchidos na tela, antes de clicar em "Salvar";
- uma planilha que você está editando;
- o andamento de um jogo, ou o conteúdo das abas abertas no navegador;
- dados que o usuário nem vê, como contadores, resultados de cálculos e listas que o programa carregou do banco de dados e alterou.

Um ponto importante: **o sistema operacional não sabe o que tem dentro da página.** Para ele, uma página é só um bloco de bytes, de 4 KB por exemplo. Ele não sabe se ali há um cadastro, um texto ou um número. Ele só sabe que a página **foi alterada**, porque o hardware liga automaticamente o **bit de modificação** (*dirty bit*) sempre que o programa grava nela.

O que **nunca** é modificado são as **páginas de código**, que guardam as instruções do programa, lidas do arquivo `.exe`. Por isso elas podem ser descartadas sem *page out*: se forem necessárias de novo, o sistema as relê do próprio executável.

### Então o *page out* "salva" meus dados?

**Não no sentido de salvar o arquivo.** O *page out* grava a página no **arquivo de paginação** (no Windows, o arquivo oculto `pagefile.sys`; no Linux, a área de *swap*). Isso serve só para liberar espaço na RAM **temporariamente**. Quando o programa precisa da página de novo, ela volta para a RAM (*page in*).

Se você fechar o programa sem salvar, os dados se perdem, mesmo que tenham passado pelo arquivo de paginação. Gravar o cadastro no banco de dados ou o texto num arquivo `.docx` é outro mecanismo, o **sistema de arquivos**, que é tema de outro grupo.

**Exemplo completo, passo a passo:**

1. Você abre um sistema de cadastro. As páginas de **código** do programa vão para a RAM. Elas não são modificadas.
2. Você digita nome e CPF de um cliente. O programa guarda isso em variáveis, e a página onde elas estão passa a ser **modificada** (bit de modificação = 1).
3. Você abre outro programa pesado e a RAM acaba. O sistema escolhe aquela página para sair e, como ela foi modificada, faz o ***page out*** dela no arquivo de paginação.
4. Você volta para a tela do cadastro. O programa precisa da página, ocorre um ***page fault***, o sistema faz o ***page in*** e os dados aparecem exatamente como estavam.
5. Você clica em "Salvar" e o **programa** grava o cliente no banco de dados. Isso já não é memória virtual.

### E a diferença entre "local" e "global"?

- **Local:** quando um processo precisa de espaço, ele só pode abrir mão das **próprias** páginas. É como se cada pessoa tivesse sua mesa e, para pegar uma folha nova, tivesse que devolver uma das suas.
- **Global:** o sistema pode tirar uma página de **qualquer** processo, menos as do núcleo. É como uma mesa compartilhada: quem precisa de espaço pode tirar a folha de outra pessoa.

---

## Seção por seção

### Resumo e 1 Introdução

> as memórias principal e secundária são combinadas

**Memória principal** é a RAM. **Memória secundária** é o HD ou SSD. A memória virtual usa os dois juntos, como se fossem uma memória só, maior que a RAM.

> Nas técnicas anteriores, como partições e *overlay* […] a fragmentação desperdiçava espaço

São técnicas antigas, explicadas no Capítulo 9 do livro (tema do outro grupo), em que o programa precisava **caber inteiro** na RAM. No *overlay*, o próprio programador dividia o programa em partes que se revezavam na memória, o que dava muito trabalho. **Fragmentação** são os pedaços de memória livre que sobram, pequenos demais para serem usados.

> TLB que identifica o processo e evita ser esvaziada na troca de contexto · bit de proteção contra execução

São resumos de temas explicados depois: TLB nas seções 5.3 e 9.2, e o bit de execução na seção 9.7.

> sistema Atlas […] família System/370

São os primeiros computadores com memória virtual: o Atlas, da Universidade de Manchester, no início dos anos 1960, e o System/370, da IBM, em 1972.

### 2 Fundamentos

> o programa usa apenas endereços virtuais, e cada um é traduzido para um endereço físico

Cada byte da memória tem um número, chamado **endereço**, como as casas de uma rua. O programa não usa os números reais da RAM: usa números "de mentira" (**virtuais**), e o hardware descobre onde cada um está de verdade (**físico**).

Pense no **ramal de uma empresa**: você disca 302, e a central sabe para qual telefone real encaminhar a ligação. Você nunca precisa saber o número verdadeiro.

> Espaço de endereçamento virtual: o conjunto de endereços que o processo pode usar.

É a faixa de endereços que cada programa enxerga. **Cada processo tem o seu.** Por isso dois programas podem usar o "endereço 8.200" ao mesmo tempo, e cada um aponta para um lugar diferente da RAM.

> unidade de gerência de memória (*Memory Management Unit*, MMU)

É uma parte do **processador** que faz a tradução de endereços. Ela é rápida porque é hardware, não um programa.

> Na troca de contexto, um registrador passa a apontar para a tabela do novo processo.

O processador alterna entre vários programas muitas vezes por segundo; essa alternância é a **troca de contexto**. Um **registrador** é uma memória minúscula dentro do processador. Na troca, o sistema muda o registrador para apontar para a tabela de páginas do programa que vai rodar agora.

> o programa não precisa ocupar posições contíguas na RAM

**Contíguas** quer dizer lado a lado, em sequência. Com a memória virtual, o programa pode ficar espalhado em pedaços pela RAM, e mesmo assim enxerga sua memória como uma sequência contínua.

> Tabela: 2³² endereços · blocos de 4 K · 2²⁰ entradas

- **2³² endereços** são cerca de 4,3 bilhões de posições, ou seja, 4 GB.
- **Bloco de 4 K** tem 4.096 posições.
- 2³² ÷ 2¹² = 2²⁰, cerca de **1 milhão de linhas** no índice.

A ideia da tabela: quanto maior o bloco, menos linhas o índice precisa ter.

### 3 Memória virtual por paginação

> páginas virtuais e páginas reais, também chamadas de *frames*

A **página** é o bloco do programa; o ***frame*** ("moldura") é o espaço na RAM onde cabe uma página. Os dois têm sempre o mesmo tamanho.

> uma entrada (ETP) por página virtual […] bit de validade

A **ETP** é uma linha da tabela de páginas. Um **bit** é um sinal de sim ou não (1 ou 0). O **bit de validade** responde à pergunta "esta página está na RAM agora?".

> O endereço virtual tem duas partes. O número da página virtual (NPV) […] e o deslocamento

Pense em "página 2, linha 8" de um livro. O **NPV** diz qual página, e o **deslocamento** diz a posição dentro dela.

**O exemplo do artigo, devagar:**

1. A página tem 4.096 posições.
2. O endereço 8.200 cabe duas páginas inteiras (2 × 4.096 = 8.192) e sobram 8. Logo, está na **página 2**, posição **8** (a contagem começa da página 0).
3. A tabela diz que a página 2 está no **frame 5**.
4. Posição real = 5 × 4.096 + 8 = **20.488**.

> Com endereços de 32 bits, são 20 bits de NPV e 12 de deslocamento

O endereço é um número binário de 32 dígitos. Os **12 dígitos finais** indicam a posição dentro da página, porque 2¹² = 4.096 posições. Os **20 iniciais** indicam qual é a página.

> ocorre um *page fault* […] *page in* […] operação de E/S

***Page fault*** significa "falta de página". **Não é um erro**: é uma situação normal, em que a página simplesmente ainda não está na RAM. ***Page in*** é a leitura da página do disco para a RAM. **E/S** é entrada e saída, ou seja, a leitura ou gravação no disco.

> o processo sai do estado de execução e vai para o estado de espera […] volta para a fila de prontos

Os estados de um processo:

- **Execução:** está usando o processador agora.
- **Espera:** está parado aguardando algo, como o disco.
- **Pronto:** pode rodar e está esperando a sua vez no processador.

> taxa de paginação […] excesso de E/S

**Taxa de paginação** é quantos *page faults* o programa causa por intervalo de tempo. Se for alta, o computador passa tempo demais lendo o disco e fica lento.

> *overhead*

É o trabalho extra que o sistema faz só para se organizar, e não para executar os programas do usuário.

> Busca por demanda […] rotinas de erro podem nunca ser carregadas

O código que mostra mensagens de erro só roda se um erro acontecer. Se não acontecer, essas páginas nunca precisam ir para a RAM.

> Busca antecipada […] ler páginas em sequência no disco é mais rápido

Se você já se levantou para ir à estante, aproveita e traz as folhas vizinhas também. Se não usar essas folhas, perdeu tempo e espaço na mesa.

> Alocação fixa / variável […] limite de *frames*

O **limite de *frames*** é quantos espaços da mesa cada processo pode ocupar. Na alocação **fixa**, esse número não muda. Na **variável**, o sistema aumenta ou diminui conforme a necessidade.

> Páginas de código não precisam de *page out*, porque o executável em disco já tem uma cópia.

As instruções do programa vêm do arquivo `.exe` e nunca mudam. Se o sistema descartar essas páginas, basta reler o `.exe` quando precisar delas de novo.

### 4 Localidade, *working set* e algoritmos de substituição

> Localidade espacial […] Localidade temporal

- **Espacial:** ao ler um vetor, o programa acessa o item 1, depois o 2, depois o 3, todos próximos uns dos outros.
- **Temporal:** num laço que repete 1.000 vezes, as mesmas instruções são usadas de novo e de novo.

**Laço** (*loop*) é um trecho de código que se repete, como `for` ou `while`.

> *working set* como o conjunto de páginas referenciadas por um processo numa janela de tempo Δt […] P2, P3 e P8

O ***working set*** são as páginas que o programa está usando **agora**. A **janela Δt** é "os últimos instantes"; Δ (delta) indica um intervalo. P2, P3 e P8 são as páginas número 2, 3 e 8.

> a partir de certo ponto, dar mais *frames* ao processo quase não reduz os *page faults*

Quando as páginas que o programa está usando já cabem na mesa, aumentar a mesa não ajuda mais.

> estratégia de frequência de *page fault* […] OpenVMS

Se um programa causa *page faults* demais, ganha mais espaço; se causa poucos, cede espaço para os outros. O **OpenVMS** é um sistema operacional criado pela empresa Digital (DEC) para os computadores VAX.

> Algoritmos: Ótimo, Aleatório, FIFO, LFU, LRU, NRU, FIFO com *buffer*, FIFO circular

| Algoritmo | Em uma frase |
| --- | --- |
| Ótimo | Um "vidente" que tira a página que não será mais usada. Impossível na prática, serve só para comparar. |
| Aleatório | Sorteia uma página. |
| FIFO | "Quem chegou primeiro sai primeiro", como uma fila de banco. |
| LFU | Tira a página **menos usada no total**. |
| LRU | Tira a página que está há **mais tempo sem uso**. |
| NRU | Usa um sinal "usada recentemente? sim ou não", zerado de tempos em tempos. |
| FIFO com *buffer* | A página que sai vai para uma "sala de espera" e pode voltar sem ir ao disco. |
| FIFO circular (*clock*) | Um ponteiro de relógio passa pelas páginas; quem foi usada ganha uma **segunda chance**. |

> BR = 0 e BM = 0 […] Mach

**BR** é o bit de referência ("foi usada recentemente?") e **BM** é o bit de modificação ("foi alterada?"). A melhor página para sair é a que **não foi usada** nem **alterada**, porque sai sem precisar gravar nada no disco. O **Mach** é um sistema operacional criado na Universidade Carnegie Mellon, que serviu de base para o núcleo do macOS.

> tabela do exemplo comparativo (F, sai 1, –)

Como ler a tabela:

- **F** é um *page fault*: a página não estava na RAM.
- **(sai 1)** indica qual página foi retirada para abrir espaço.
- **–** quer dizer que a página já estava na RAM (um acerto).

Quanto **menos F**, melhor o algoritmo. Na sequência 1 2 3 1 4 1 3 2 3 3, as páginas 1 e 3 aparecem várias vezes; o LRU percebe isso e as mantém, enquanto o FIFO tira a página 1 só porque ela chegou primeiro.

### 5 Suporte de hardware

> entre 512 e 16 M endereços

São os tamanhos de página possíveis, que dependem do processador. 16 M são cerca de 16 milhões de posições.

> Fragmentação interna (sobra espaço na última página)

Um programa de 10 KB com páginas de 4 KB ocupa 3 páginas (12 KB). Os **2 KB** que sobram na última página ficam desperdiçados. É como uma caixa de mudança que vai só pela metade.

> ler duas páginas de 512 bytes custa bem mais que uma de 1.024

Toda leitura no disco tem um custo fixo para começar. Ler tudo de uma vez é mais barato do que fazer duas leituras pequenas.

> 4 MB de tabela por processo

São 1 milhão de linhas × 4 bytes. Com 100 programas abertos, seriam 400 MB só de índices.

> tabela-diretório […] cada nível acrescenta pelo menos um acesso à memória

Em vez de um índice gigante, usa-se um **índice de índices**, como uma enciclopédia em vários volumes: o índice geral diz em qual volume procurar, e só os volumes em uso ficam na mesa. O custo é consultar dois índices em vez de um.

> TLB […] memória cache […] mapeamento associativo […] TLB *hit* / *miss* […] *tag*

- **Cache** é uma memória pequena e muito rápida que guarda cópias do que foi usado recentemente.
- ***Hit*** quer dizer que a tradução foi achada na TLB; ***miss***, que não foi.
- **Mapeamento associativo** significa que a TLB compara todas as entradas ao mesmo tempo, como perguntar a uma sala inteira de uma vez em vez de pessoa por pessoa.
- A ***tag*** é a etiqueta usada na busca: o número da página.

> precisa ser reinicializada a cada troca de contexto

Cada programa tem seu próprio "mapa". O post-it de um programa não serve para outro, então, se as anotações não disserem de qual programa são, é preciso apagar tudo a cada troca.

### 6 Proteção e compartilhamento

> cada processo só enxerga a sua tabela

O programa A não consegue nem "dar nome" à memória do programa B, porque seus endereços só existem dentro da própria tabela.

> Uma tentativa de gravar numa página só de leitura gera um erro.

É o erro que você já pode ter visto: no Windows, o programa fecha com "**violação de acesso**"; no Linux, aparece "***Segmentation fault***".

> um programa reentrante usado por vários processos tem uma única cópia na RAM

**Reentrante** é o código que vários processos podem executar ao mesmo tempo sem atrapalhar um ao outro, porque o código não se altera. Exemplo: 30 pessoas usando o mesmo editor num servidor ocupam **uma** cópia do código e **30** áreas de dados, uma para cada pessoa.

> a aplicação é responsável por sincronizar os acessos

Se dois programas mexem no mesmo dado, precisam combinar a vez, senão um estraga o trabalho do outro. Esse tema (sincronização) é de outro capítulo do livro.

### 7 Segmentação

> segmentos, que seguem a estrutura lógica do programa: um procedimento, uma função, um vetor ou a pilha

Em vez de folhas de tamanho igual, o programa é dividido em **pedaços com significado**, cada um com o seu tamanho. A **pilha** é a área onde ficam as variáveis locais das funções e o "caminho de volta" de cada chamada.

> estruturas de dados dinâmicas, como pilhas e listas

São estruturas que **crescem e diminuem** durante a execução, como uma lista de clientes que aumenta a cada cadastro.

> *best-fit*, *worst-fit* ou *first-fit*

São formas de escolher um espaço livre na memória (Capítulo 9):

- ***best-fit*:** o espaço mais justo;
- ***worst-fit*:** o maior espaço;
- ***first-fit*:** o primeiro espaço que servir.

> fragmentação externa

Pense num estacionamento: sobram várias vagas pequenas entre os carros, mas nenhuma serve para um ônibus.

> Programação modular: Dispensável / Indispensável

Na segmentação, o programa **precisa** ser dividido em módulos para funcionar bem. Na paginação, tanto faz, porque ela corta o programa em pedaços iguais sem olhar o conteúdo.

> Segmentação com paginação

O programador enxerga "capítulos" (segmentos), e o sistema guarda cada capítulo em "folhas" de tamanho igual (páginas).

### 8 *Swapping* e *thrashing*

> *swap out* […] *swap in* […] arquivo de *swap*

O ***swap out*** tira o **programa inteiro** da RAM e grava no disco; o ***swap in*** o traz de volta. O ***page out***, da seção 3.4, tira só **uma página**.

> prontos de menor prioridade

São programas que podem rodar, mas que o sistema considera menos urgentes.

> *Thrashing*

Já aconteceu de abrir programas demais num computador com pouca RAM e tudo travar, com o disco trabalhando sem parar? Isso é *thrashing*: o sistema passa o tempo trocando páginas entre RAM e disco e quase não executa nada.

> modularização extrema

Um programa dividido em pedaços pequenos demais, que precisa trocar de segmento o tempo todo.

### 9 Comparativo com as tecnologias atuais

**9.1 Tabelas com mais níveis**

- **x86-64** são os processadores Intel e AMD dos PCs e notebooks comuns. **ARM64** são os processadores de celulares, dos Macs com chip Apple e do Raspberry Pi.
- **TiB e PiB** são unidades enormes: 1 TiB tem cerca de 1,1 trilhão de bytes, e 1 PiB equivale a 1.024 TiB. 256 TiB é muito mais RAM do que qualquer PC tem, por isso 48 bits bastaram por muitos anos.
- *"com páginas de 64 KB o deslocamento é maior"*: 64 KB = 2¹⁶, então são 16 bits de deslocamento em vez de 12. Sobram menos bits para numerar páginas e o índice fica menor.

**9.2 TLB que identifica o processo**

- **PCID** é uma etiqueta em cada anotação da TLB dizendo "isto é do programa X". Com ela, a TLB não precisa ser apagada a cada troca de programa.
- **CR3** é o nome do registrador, nos processadores Intel e AMD, que aponta para a tabela de páginas do programa atual.
- **Meltdown** foi uma falha de segurança em processadores, divulgada em 2018, que permitia a um programa comum ler a memória do núcleo. A **PTI** é a correção: enquanto um programa comum roda, a memória do núcleo nem aparece no mapa dele.

**9.3 Páginas grandes**

- ***Transparent Hugepage Support*** é um recurso do Linux que usa páginas de 2 MB automaticamente.
- ***"não são pagináveis"*** quer dizer que essas páginas **nunca vão para o disco**.
- **A9** é o chip do iPhone 6s.

**9.4 *Working set*, *buffer* e substituição**

- ***Hard page fault*:** a página precisou ser lida do disco (lento).
- ***Soft page fault*:** a página ainda estava na RAM, só "fora do lugar", e voltou sem acessar o disco (rápido).
- **Página "em transição"** é a página que saiu da mesa da pessoa, mas ainda não foi jogada fora: está na "sala de espera" do FIFO com *buffer*.
- ***Multi-Gen LRU*** é uma versão moderna do LRU no Linux que separa as páginas em **gerações**, por idade de uso.
- **`page-cluster` = 3** significa ler 2³ = **8 páginas vizinhas** de uma vez quando for buscar algo no *swap*.

**9.5 Compressão antes do disco**

- **Comprimir uma página** é como "zipar" um arquivo: ela ocupa menos espaço e continua na RAM. Descomprimir é muito mais rápido do que ler do disco.
- *"troca ciclos de CPU por menos E/S de swap"*: o sistema gasta um pouco de processamento comprimindo para evitar usar o disco, que é bem mais lento.
- **zswap** e **zram** são as duas formas de fazer isso no Linux.
- **Limite de memória comprometida** é o total de memória que o Windows aceita "prometer" aos programas: a RAM mais o arquivo de paginação.
- **lmkd** é o motivo pelo qual, no Android, um app que ficou em segundo plano às vezes **reinicia do zero** quando você volta a ele: o sistema o fechou para liberar memória.
- **iOS sem arquivo de *swap*:** o iPhone não grava no disco os dados que os apps alteraram. Quando falta memória, ele encerra apps em segundo plano.

**9.6 *Thrashing* medido**

- **PSI** é um "medidor" do Linux que mostra quanto tempo os programas passam parados esperando memória.
- **Linha *full*** é o tempo em que **todos** os programas estão parados ao mesmo tempo, que é o sinal de *thrashing*.

**9.7 Permissão de execução**

- ***Heap*** é a área onde o programa guarda dados criados durante a execução, como uma lista de clientes carregada. A **pilha** foi explicada na seção 7.
- ***Buffer overflow*** é um ataque em que alguém envia mais dados do que o espaço reservado aguenta, por exemplo 500 caracteres num campo de 20. O excesso escreve por cima da memória vizinha, e o atacante tenta colocar ali um código malicioso. Com a **DEP**, essa área é marcada como "proibido executar", e o código não roda.

**9.8 *Copy-on-write***

- **`fork()`** é o comando do Linux que cria um processo "filho" igual ao processo "pai".
- ***Copy-on-write*** quer dizer "copiar só quando alguém escrever". Pense em duas pessoas lendo o mesmo documento impresso: só quando uma delas quer rabiscar é que se tira uma fotocópia para ela.

**9.9 Segmentação no x86-64**

- **CS, SS, DS, ES, FS e GS** são registradores de segmento dos processadores Intel e AMD: CS para o código, SS para a pilha (*stack*), DS para os dados, e ES, FS e GS como extras.
- ***Thread*** é uma linha de execução dentro de um programa; um navegador, por exemplo, tem várias. **Dados locais de cada *thread*** são variáveis das quais cada *thread* tem sua própria cópia.
- **Espaço de endereçamento plano** é uma única sequência contínua de endereços, sem divisão em segmentos.

**9.10 Virtualização**

- **Máquina virtual** é um computador "simulado" rodando dentro de outro, como no VirtualBox ou nos servidores em nuvem.
- Nela, a tradução acontece **duas vezes**: o sistema convidado traduz o endereço para o que ele acha que é a RAM, e o computador real traduz de novo para a RAM verdadeira. **EPT** (Intel) e **NPT** (AMD) fazem isso no hardware.
- *"um TLB miss custa ainda mais"*: quando a anotação não está no post-it, é preciso consultar **dois** índices em vez de um.

**9.11 Tabela-síntese**

Cada linha pega um conceito do livro e responde a duas perguntas: o conceito ainda existe? Onde ele aparece hoje?

---

## Glossário rápido

| Termo | Significado simples |
| --- | --- |
| Alocação fixa / variável | Se o número de espaços na RAM de cada programa é fixo ou pode mudar |
| Arquivo de paginação | Arquivo no disco onde vão as páginas modificadas que saíram da RAM (`pagefile.sys` no Windows) |
| Bit | Um sinal de sim ou não (1 ou 0) |
| Bit de modificação (*dirty bit*) | Marca se a página foi alterada desde que entrou na RAM |
| Bit de referência | Marca se a página foi usada recentemente |
| Bit de validade | Marca se a página está na RAM agora |
| Bloqueada (página) | Fixada na RAM: nunca é escolhida para sair |
| Cache | Memória pequena e rápida com cópias do que foi usado recentemente |
| Contíguo | Lado a lado, em sequência |
| Deslocamento | A posição de um endereço dentro da página |
| E/S | Entrada e saída: leitura ou gravação no disco, teclado, rede etc. |
| Endereço físico (real) | A posição verdadeira na RAM |
| Endereço virtual | O endereço que o programa usa, traduzido pela MMU |
| Fragmentação externa | Espaços livres entre blocos, pequenos demais para usar |
| Fragmentação interna | Espaço sobrando dentro de um bloco, como na última página |
| *Frame* | Espaço da RAM do tamanho exato de uma página |
| *Kernel* (núcleo) | A parte central do sistema operacional; não é o núcleo do processador |
| Localidade | A tendência do programa de usar poucas páginas por vez |
| MMU | Parte do processador que traduz endereços virtuais em físicos |
| NPV | Número da página virtual: diz em qual página está o endereço |
| *Overhead* | Trabalho extra do sistema só para se organizar |
| Página | Bloco de tamanho fixo em que o programa é dividido |
| *Page fault* | A página pedida não está na RAM (é normal, não é erro) |
| *Page in* / *page out* | Trazer uma página do disco para a RAM / gravar uma página modificada no disco |
| Processo | Programa em execução |
| Registrador | Memória minúscula dentro do processador |
| Segmento | Bloco de tamanho variável com significado (função, vetor, pilha) |
| *Swap in* / *swap out* | Trazer de volta / tirar da RAM um processo inteiro |
| Tabela de páginas | Índice que liga cada página virtual ao seu *frame* |
| Taxa de paginação | Quantos *page faults* acontecem por intervalo de tempo |
| *Thrashing* | O sistema passa mais tempo trocando páginas do que executando |
| TLB | Cache das traduções de endereço mais recentes |
| Troca de contexto | O processador deixa um programa e passa a executar outro |
| *Working set* | As páginas que o programa está usando agora |
