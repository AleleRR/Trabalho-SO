# Gerência de Memória Virtual — Conteúdo do Trabalho Final

**Disciplina:** 11781 – Sistemas Operacionais (CHASIS-04) – Unoesc
**Fonte principal:** MACHADO, F. B.; MAIA, L. P. *Arquitetura de Sistemas Operacionais*. 5. ed. LTC, 2013 — **Capítulo 10**
**Capítulo isolado:** `01-Conteudo-Isolado/Cap10-Gerencia-de-Memoria-Virtual.pdf` (49 páginas, pp. 212–260 do PDF do livro)
**Apresentação:** máx. 30 min · 3 apresentadores sorteados na hora · entregar PDF por e-mail antes de começar

> ⚠️ **Confirmar a data com o professor:** o enunciado diz "25/11/2026" na seção *Tarefa*, "26/11/2026" logo abaixo e "26/11/2025" na seção *Apresentação*.

**Integrantes:** Alexandre Reitemeyer · Angelo Pandolfo Antonini · Beatriz Aparecida Miranda · Eduardo de Oliveira da Silva · Felipe Samuel Janissk · Gabriel Nassinger · Gustavo Henrique de Marchi Meurer · Jessé Chaves · Lucas Santos Magro

---

## 0. Como este material atende à avaliação

| Critério (nota máx.) | Como atendemos |
|---|---|
| 1) Clareza e coesão (1,0) | Segue a ordem do Cap. 10; cada bloco termina com um "gancho" para o próximo; o slide 3 liga com o tema do grupo de Gerência de Memória (Cap. 9) |
| 2) Sequência (0,5) | 3 blocos: **Fundamentos → Paginação e políticas → Hardware, outras técnicas e problemas** |
| 3) Estrutura geral (1,0) | Capa, roteiro, desenvolvimento, conclusão e referências |
| 4) Domínio do tema (1,5) | "Fala" de cada slide + banco de perguntas (seção 5). Como o sorteio é na hora, **os 9 estudam os 3 blocos** |
| 5) Desenvoltura (1,0) | Exemplos numéricos que podem ser explicados apontando para o slide; ensaio cronometrado |
| 6) Qualidade dos slides (1,0) | Cores suaves, no máximo 5 tópicos curtos por slide, diagramas no lugar de parágrafos (seção 7) |
| 7) Pesquisa (4,0) | Cap. 10 inteiro coberto + exemplos resolvidos + validação em sistemas atuais (Windows, Linux, x86-64) |

## 1. Escopo — o que entra e o que fica de fora

- **Entra:** todo o Cap. 10, seções 10.1 a 10.8.
- **Fica de fora (são de outros grupos):** alocação contígua, overlay e partições (Cap. 9, grupo *Gerência de Memória*), sistema de arquivos e dispositivos de E/S. O Cap. 9 aparece em **um único slide**, só como motivação.
- **Swapping:** também aparece no Cap. 9. Nós tratamos apenas o *Swapping em Memória Virtual* (10.7).
- **Exercícios (10.9) e laboratório SOsim (10.10):** não viram slides. Servem para preparar a arguição (seções 4 e 5).

## 2. Divisão da apresentação

Os apresentadores são sorteados na hora, então os blocos têm duração parecida: cada sorteado assume um.

| Bloco | Slides | Tempo-alvo | Seções do livro |
|---|---|---|---|
| **1 – Fundamentos** | 1 a 9 | 8 min | 10.1, 10.2, 10.3, início de 10.4 |
| **2 – Paginação e suas políticas** | 10 a 17 | 9 min | 10.4 (page fault) até 10.4.5 |
| **3 – Hardware, outras técnicas e problemas** | 18 a 28 | 9 min | 10.4.6 até 10.8 |
| **Total** | 28 slides | **~26 min** | sobram ~4 min de folga |

Se o ensaio passar de 27 min, o primeiro corte é o slide 26 (validação), que pode virar uma frase dentro da conclusão.

**Sugestão para produzir o material:** 3 trios, um por bloco. Cada trio monta os slides do seu bloco e prepara as respostas da seção 5 sobre ele. Depois, todos estudam os três blocos.

---

## 3. Roteiro slide a slide

Legenda: **Na tela** = o que aparece no slide (só tópicos) · **Visual** = diagrama ou figura · **Fala** = o que o apresentador explica · **Livro** = seção e página do PDF isolado.

### BLOCO 1 — FUNDAMENTOS

#### Slide 1 — Capa
- **Na tela:** "Gerência de Memória Virtual" · Sistemas Operacionais – Unoesc · nomes dos 9 integrantes · data.
- **Fala:** "Nosso tema é como o sistema operacional faz cada programa acreditar que tem uma memória maior do que a RAM que existe de verdade."

#### Slide 2 — Roteiro
- **Na tela:** 1. Fundamentos · 2. Paginação e suas políticas · 3. Hardware, outras técnicas e problemas
- **Fala:** seguimos a ordem do Cap. 10 de Machado e Maia.

#### Slide 3 — Por que memória virtual?
- **Na tela:**
  - Programa limitado ao tamanho da RAM
  - Overlay: difícil e sem garantia
  - Fragmentação da memória
  - Poucos processos na memória
- **Visual:** um programa grande que não cabe em uma RAM pequena.
- **Fala:** "As técnicas de gerência de memória real, como partições e overlay, exigem que o programa caiba na memória e desperdiçam espaço com fragmentação. A memória virtual surge para resolver isso."
- **Livro:** §10.1, p. 1

#### Slide 4 — O que é memória virtual
- **Na tela:**
  - RAM + disco combinados
  - Ilusão de uma memória maior que a real
  - Endereço do programa ≠ endereço físico
  - Mais processos na RAM → CPU mais bem aproveitada
  - Atlas (Manchester, 1962) · IBM System/370 (1972)
- **Fala:** definição do livro: as memórias principal e secundária são combinadas e o programa não fica preso ao endereço físico. Vantagens: programas maiores que a RAM, mais processos ao mesmo tempo e menos fragmentação. Hardware e SO trabalham juntos.
- **Livro:** §10.1, p. 1

#### Slide 5 — Espaço de endereçamento virtual × real
- **Na tela:**
  - Virtual: endereços que o processo usa
  - Real: endereços da memória principal
  - Só parte do programa fica na RAM; o resto fica no disco
  - Transparente ao programador
- **Visual:** à esquerda, `vetor[42]` (o programador não sabe onde o dado está fisicamente); à direita, um espaço virtual grande ligado por setas a uma RAM pequena e ao disco.
- **Fala:** a analogia do vetor (Fig. 10.1): ao usar `vetor[42]`, ninguém pensa no endereço físico. Com a memória virtual vale o mesmo para o programa inteiro. Compilador e linker geram código para o espaço virtual, e o SO cuida do resto durante a execução.
- **Livro:** §10.2, pp. 2–3

#### Slide 6 — Mapeamento e MMU
- **Na tela:**
  - Mapeamento: tradução de endereço virtual para real
  - Feito pela MMU (hardware) junto com o SO
  - Uma tabela de mapeamento por processo
  - Na troca de contexto, um registrador passa a apontar para a tabela do novo processo
  - O programa não precisa estar contíguo na RAM
- **Visual:** CPU → (end. virtual) → **MMU** → (end. real) → RAM, com a tabela do processo ao lado.
- **Livro:** §10.3, pp. 4–5

#### Slide 7 — Por que mapear em blocos?
- **Na tela:** Tabela 10.1 reduzida

  | Espaço virtual | Tamanho do bloco | Entradas na tabela |
  |---|---|---|
  | 2³² | 512 | 2²³ |
  | 2³² | 4 K | 2²⁰ |
  | 2⁶⁴ | 4 K | 2⁵² |
  | 2⁶⁴ | 64 K | 2⁴⁸ |

  - Blocos fixos → **paginação** · blocos variáveis → **segmentação** · os dois → **segmentação com paginação**
- **Fala:** se cada endereço tivesse sua própria entrada, a tabela seria do tamanho do espaço virtual, o que é inviável. Por isso o mapeamento é feito em blocos: quanto maior o bloco, menor a tabela.
- **Livro:** §10.3, pp. 5–6

#### Slide 8 — Paginação: páginas, frames e tabela de páginas
- **Na tela:**
  - Espaços virtual e real divididos em blocos do mesmo tamanho
  - Página virtual ↔ página real (**frame**)
  - Cada processo tem sua tabela de páginas
  - Entrada da tabela (ETP): frame + bit de validade + bits de controle
- **Visual:** Fig. 10.6 redesenhada (páginas virtuais → tabela → frames, fora de ordem).
- **Livro:** §10.4, pp. 7–8

#### Slide 9 — Tradução de endereço: exemplo
- **Na tela:**
  - Endereço virtual = **NPV** (nº da página virtual) + **deslocamento**
  - Página de 4 KB (4096 endereços)
  - Endereço virtual 8200 → NPV 2, deslocamento 8
  - Na tabela: página 2 → frame 5
  - Endereço físico = 5 × 4096 + 8 = **20488**
- **Visual:** endereço dividido em `[ NPV | deslocamento ]`, com uma seta do NPV para a tabela e outra da tabela para o frame.
- **Fala:** o NPV é o índice na tabela; o deslocamento é copiado sem mudança. Com endereços de 32 bits e páginas de 4 KB são 20 bits de NPV e 12 de deslocamento (2¹² = 4096).
- **Gancho para o bloco 2:** "E se a página 2 não estiver na memória? Aí acontece um *page fault*."
- **Livro:** §10.4, pp. 7–9

### BLOCO 2 — PAGINAÇÃO E SUAS POLÍTICAS

#### Slide 10 — Page fault
- **Na tela:**
  - Bit de validade: 1 = na RAM · 0 = no disco
  - Bit 0 → **page fault**
  - Processo: execução → espera
  - **Page in:** página vai do disco para a RAM
  - Processo: espera → pronto
  - Taxa de paginação = page faults por intervalo de tempo
- **Visual:** fluxograma da Fig. 10.8.
- **Fala:** a MMU detecta pelo bit de validade e o SO trata. Algum page fault é normal; uma taxa alta significa muitas operações de E/S e derruba o desempenho.
- **Livro:** §10.4, pp. 9–10

#### Slide 11 — Políticas de busca e de alocação
- **Na tela (duas colunas):**
  - **Busca: quando carregar?**
    - Por demanda: só quando a página é referenciada
    - Antecipada (*prepaging*): carrega páginas extras
  - **Alocação: quantos frames?**
    - Fixa: limite definido na criação do processo
    - Variável: limite muda conforme a taxa de paginação
- **Fala:**
  - Demanda: rotinas de tratamento de erro podem nunca ser carregadas. Antecipada: ler páginas em sequência no disco é mais rápido, mas pode desperdiçar tempo e memória.
  - Fixa: simples, mas limite pequeno gera muitos page faults e limite grande deixa menos processos na memória. Variável: mais flexível, porém o SO precisa monitorar os processos (mais overhead).
- **Livro:** §10.4.1 e §10.4.2, pp. 11–12

#### Slide 12 — Políticas de substituição
- **Na tela:**
  - Sem frame livre → escolher uma página para sair
  - Página modificada → **page out** (vai para o arquivo de paginação)
  - **Bit de modificação** (*dirty bit*) indica se ela mudou
  - **Local:** só páginas do próprio processo
  - **Global:** qualquer página (menos as do núcleo, que ficam bloqueadas)

  | Alocação | Substituição possível |
  |---|---|
  | Fixa | só local |
  | Variável | local ou global |
- **Fala:** página de código não precisa de page out, porque existe cópia no executável. Página de dados modificada precisa ser gravada antes de sair, senão os dados se perdem.
- **Livro:** §10.4.3, pp. 12–14

#### Slide 13 — Princípio da localidade
- **Na tela:**
  - **Espacial:** depois de um acesso, vêm acessos a endereços próximos
  - **Temporal:** o mesmo endereço volta a ser acessado logo
  - Ex.: um laço em 3 páginas usa essas 3 páginas o tempo todo
  - É o que faz a memória virtual funcionar
- **Visual:** gráfico de page faults × tempo: picos no início de cada fase do programa, depois estabilidade (Fig. 10.10).
- **Fala:** como o programa concentra os acessos em poucas páginas, basta manter essas páginas na RAM. Programas estruturados (laços, sub-rotinas) têm boa localidade.
- **Livro:** §10.4.4, pp. 14–15

#### Slide 14 — Working set
- **Na tela:**
  - Modelo de Peter Denning (1968)
  - Conjunto de páginas usadas numa janela de tempo Δt
  - Working set na RAM → evita o thrashing
  - O limite de frames acompanha o working set
  - Implementação: estratégia de frequência de page fault
- **Visual:** linha do tempo de referências com a janela Δt destacada (Fig. 10.11) + curva "taxa de page fault × nº de frames" (Fig. 10.13).
- **Fala:** no exemplo do livro, W(t2, Δt) = {P2, P3, P8}. A curva mostra que, a partir de certo ponto, dar mais frames quase não reduz os page faults, e esse ponto chega muito antes de o programa inteiro estar na RAM. O modelo só funciona com alocação variável. Na estratégia de frequência de page fault (usada no OpenVMS), o processo com taxa acima do limite ganha frames e o que fica abaixo perde.
- **Livro:** §10.4.4, pp. 15–17

#### Slide 15 — Algoritmos de substituição (1): os clássicos
- **Na tela:**

  | Algoritmo | Escolhe para sair | Problema |
  |---|---|---|
  | Ótimo | a que não será mais usada (ou a mais distante) | impossível prever o futuro; serve só de referência |
  | Aleatório | qualquer uma | barato, mas pouco eficiente |
  | FIFO | a mais antiga na memória | pode tirar uma página muito usada |
  | LFU | a menos referenciada | penaliza a página que acabou de chegar |
  | LRU | a que está há mais tempo sem uso | ótimo resultado, mas custo alto |
- **Livro:** §10.4.5, pp. 17–19

#### Slide 16 — Exemplo: FIFO × LRU × Ótimo
- **Na tela:** sequência **1 2 3 1 4 1 3 2 3 3** com 3 frames (Exercício 23b do livro) → **FIFO: 7 page faults · LRU: 5 · Ótimo: 5**
- **Visual:** as tabelas da seção 4.2, com os page faults destacados.
- **Fala:** o LRU empata com o ótimo porque o programa volta sempre às páginas 1 e 3 (localidade). O FIFO tirou a página 1, que era muito usada, só por ser a mais antiga.
- **Para a arguição:** na sequência do Exercício 23a o resultado se inverte (FIFO 5, LRU 8). Nenhum algoritmo é o melhor em todos os casos; depende da localidade do programa.
- **Livro:** §10.4.5 e Exercício 23, p. 42

#### Slide 17 — Algoritmos de substituição (2): os usados na prática
- **Na tela:**
  - **NRU:** bit de referência (BR) zerado de tempos em tempos; com o bit de modificação (BM), as páginas ficam em 4 categorias
  - **FIFO com buffer de páginas:** a página retirada vai para uma lista de livres e pode voltar sem acessar o disco (Mach, OpenVMS, Windows)
  - **FIFO circular (clock):** BR = 1 → zera o bit e avança ("segunda chance"); BR = 0 → substitui. Usado na maioria dos Unix
- **Visual:** relógio com ponteiro (Fig. 10.16) + quadro do NRU:

  | Categoria | BR | BM | Situação |
  |---|---|---|---|
  | 1 (sai primeiro) | 0 | 0 | não referenciada, não modificada |
  | 2 | 0 | 1 | não referenciada, modificada |
  | 3 | 1 | 0 | referenciada, não modificada |
  | 4 (sai por último) | 1 | 1 | referenciada, modificada |
- **Gancho para o bloco 3:** "Até aqui vimos o que o SO decide. Agora, como o hardware ajuda e quais problemas aparecem."
- **Livro:** §10.4.5, pp. 19–22

### BLOCO 3 — HARDWARE, OUTRAS TÉCNICAS E PROBLEMAS

#### Slide 18 — Tamanho de página
- **Na tela (duas colunas):**
  - **Páginas pequenas:** ✔ menos fragmentação interna · ✔ só o necessário na RAM · ✘ tabelas maiores · ✘ mais page faults e mais E/S
  - **Páginas grandes:** ✔ tabelas menores · ✔ E/S mais eficiente · ✘ fragmentação interna na última página
  - Depende do hardware: de 512 a 16 M endereços
  - Tendência: páginas maiores
- **Fala:** com 32 bits e páginas de 4 KB são 2²⁰ entradas × 4 bytes = **4 MB de tabela por processo**. Ler duas páginas de 512 bytes custa muito mais do que ler uma de 1024.
- **Livro:** §10.4.6, pp. 22–23

#### Slide 19 — Paginação em múltiplos níveis
- **Na tela:**
  - Problema: tabela de 4 MB por processo
  - Solução: tabela-diretório que aponta para tabelas de páginas
  - 32 bits = 10 (NPV1) + 10 (NPV2) + 12 (deslocamento)
  - Só as tabelas necessárias ficam na RAM
  - 64 bits → 3, 4 ou mais níveis
  - Custo: um acesso à memória a mais por nível
- **Visual:** Fig. 10.19/10.20 redesenhadas.
- **Fala:** é o princípio da localidade aplicado às próprias tabelas. Exemplos do livro: VAX (2 níveis), SPARC (3), Motorola 68030 (4).
- **Gancho:** "Cada nível custa mais um acesso à memória. Como evitar? Com a TLB."
- **Livro:** §10.4.7, pp. 23–26

#### Slide 20 — TLB (Translation Lookaside Buffer)
- **Na tela:**
  - Cache das traduções mais recentes
  - Busca associativa: compara todas as entradas ao mesmo tempo
  - **TLB hit:** não precisa consultar a tabela de páginas
  - **TLB miss:** consulta a tabela (e pode haver page fault)
  - Poucas entradas, taxa de acerto alta (localidade)
- **Visual:** CPU → TLB → (hit) RAM | (miss) tabela de páginas → RAM.
- **Fala:** campos: tag, modificação, referência, proteção e endereço físico (Tabela 10.3). Na troca de contexto a TLB é reinicializada, a não ser que cada entrada guarde a identificação do processo. A substituição de entradas costuma ser aleatória.
- **Livro:** §10.4.8, pp. 26–28

#### Slide 21 — Proteção e compartilhamento
- **Na tela:**
  - **Proteção:** cada processo só enxerga a própria tabela · bits de leitura e gravação por página · gravar em página só de leitura gera erro
  - **Compartilhamento:** tabelas de processos diferentes apontam para o mesmo frame · código reentrante com uma única cópia na RAM · dados compartilhados exigem sincronização
- **Visual:** dois processos com tabelas apontando para o mesmo frame (Fig. 10.24) + quadro "sem acesso / leitura / leitura e gravação" (Fig. 10.23).
- **Livro:** §10.4.9 e §10.4.10, pp. 28–30

#### Slide 22 — Segmentação
- **Na tela:**
  - Blocos de tamanhos diferentes: **segmentos**
  - Segue a lógica do programa (função, vetor, pilha)
  - Endereço = NSV + deslocamento → tabela de segmentos
  - Entrada da tabela: tamanho, validade, modificação, referência, proteção
  - Boa para estruturas de dados dinâmicas
  - Problema: **fragmentação externa**
- **Fala:** os segmentos são alocados com best-fit, worst-fit ou first-fit (como no Cap. 9). Exige programas bem modularizados.
- **Livro:** §10.5, pp. 30–34

#### Slide 23 — Paginação × segmentação → segmentação com paginação
- **Na tela:** Tabela 10.5

  | Característica | Paginação | Segmentação |
  |---|---|---|
  | Tamanho dos blocos | Iguais | Diferentes |
  | Proteção | Complexa | Mais simples |
  | Compartilhamento | Complexo | Mais simples |
  | Estruturas de dados dinâmicas | Complexo | Mais simples |
  | Fragmentação interna | Pode existir | Não existe |
  | Fragmentação externa | Não existe | Pode existir |
  | Programação modular | Dispensável | Indispensável |
  | Alteração do programa | Mais trabalhosa | Mais simples |

  - **Segmentação com paginação:** endereço = NSV + NPV + deslocamento · o programador vê segmentos e o sistema vê páginas · sem fragmentação externa
- **Livro:** §10.5 e §10.6, pp. 34–35

#### Slide 24 — Swapping em memória virtual
- **Na tela:**
  - Falta memória para novos processos
  - **Swap out:** o processo inteiro vai para o arquivo de swap
  - **Swap in:** depois ele volta para a RAM
  - Critério: primeiro processos em espera; depois prontos de menor prioridade
  - Arquivo de swap compartilhado (às vezes é o mesmo da paginação)
- **Visual:** Fig. 10.28.
- **Fala:** diferença importante: *page out* retira **uma página**; *swap out* retira **o processo inteiro**.
- **Livro:** §10.7, pp. 35–37

#### Slide 25 — Thrashing
- **Na tela:**
  - Transferência excessiva de páginas/segmentos entre RAM e disco
  - **No processo:** limite de frames menor que o working set ou falta de localidade
  - **No sistema:** mais processos do que memória → swapping excessivo
  - O sistema passa mais tempo paginando do que executando
  - Solução definitiva: mais memória principal
- **Visual:** muitas setas indo e voltando entre RAM e disco.
- **Fala:** liga com o working set (slide 14). Na segmentação, o thrashing do processo vem da modularização extrema. O problema não é exclusivo da memória virtual.
- **Livro:** §10.8, pp. 38–39

#### Slide 26 — Memória virtual hoje (validação) *(opcional se o tempo apertar)*
- **Na tela:**
  - **Windows:** arquivo `pagefile.sys`; listas de páginas livres e modificadas (o "FIFO com buffer" do livro)
  - **Linux:** partição ou arquivo de swap; listas *active/inactive* (aproximação do LRU)
  - **x86-64:** páginas de 4 KB, tabelas de 4 níveis (48 bits) e 5 níveis (57 bits); páginas grandes de 2 MB e 1 GB
  - Todo processador moderno tem MMU e TLB
- **Fala:** "Os conceitos do livro estão em uso nos sistemas de hoje: múltiplos níveis, TLB, buffer de páginas e a tendência de páginas maiores."
- **Demonstração possível (30 s):** no Windows, Monitor de Recursos → aba *Memória* → coluna de *hard faults* (falhas de página que exigem leitura do disco). No Linux, `vmstat 1` mostra as colunas `si`/`so` (swap in / swap out).
- **Fontes:** seção 6

#### Slide 27 — Conclusão
- **Na tela:**
  - Endereço lógico separado do físico
  - Paginação é a técnica dominante
  - A localidade é o que faz tudo funcionar
  - Desempenho = boas políticas + hardware (MMU, TLB)
  - Maior risco: thrashing

#### Slide 28 — Referências
- Ver seção 8.

---

## 4. Exemplos resolvidos (para estudar)

### 4.1 Tradução de endereço (slide 9)
- Página de 4096 endereços = 2¹² → **12 bits** de deslocamento.
- Endereço virtual 8200 = 2 × 4096 + 8 → **NPV = 2**, **deslocamento = 8**.
- Em hexadecimal: 8200 = `0x2008` → NPV `0x2` e deslocamento `0x008`.
- Página 2 está no frame 5 → físico = 5 × 4096 + 8 = **20488** (`0x5008`).

### 4.2 Substituição de páginas — Exercício 23b (slide 16)
Sequência: **1 2 3 1 4 1 3 2 3 3** · 3 frames · **negrito** = página que acabou de entrar.

**FIFO — 7 page faults**

| Referência | 1 | 2 | 3 | 1 | 4 | 1 | 3 | 2 | 3 | 3 |
|---|---|---|---|---|---|---|---|---|---|---|
| Frame A | **1** | 1 | 1 | 1 | **4** | 4 | 4 | 4 | **3** | 3 |
| Frame B | – | **2** | 2 | 2 | 2 | **1** | 1 | 1 | 1 | 1 |
| Frame C | – | – | **3** | 3 | 3 | 3 | 3 | **2** | 2 | 2 |
| Page fault | F | F | F | | F | F | | F | F | |

**LRU — 5 page faults**

| Referência | 1 | 2 | 3 | 1 | 4 | 1 | 3 | 2 | 3 | 3 |
|---|---|---|---|---|---|---|---|---|---|---|
| Frame A | **1** | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| Frame B | – | **2** | 2 | 2 | **4** | 4 | 4 | **2** | 2 | 2 |
| Frame C | – | – | **3** | 3 | 3 | 3 | 3 | 3 | 3 | 3 |
| Page fault | F | F | F | | F | | | F | | |

**Ótimo — 5 page faults**

| Referência | 1 | 2 | 3 | 1 | 4 | 1 | 3 | 2 | 3 | 3 |
|---|---|---|---|---|---|---|---|---|---|---|
| Frame A | **1** | 1 | 1 | 1 | 1 | 1 | 1 | **2** | 2 | 2 |
| Frame B | – | **2** | 2 | 2 | **4** | 4 | 4 | 4 | 4 | 4 |
| Frame C | – | – | **3** | 3 | 3 | 3 | 3 | 3 | 3 | 3 |
| Page fault | F | F | F | | F | | | F | | |

### 4.3 Gabarito de outros exercícios úteis na arguição
Os resultados de 23 e 28 foram conferidos por simulação.

| Exercício | Situação | Resultado |
|---|---|---|
| 23a | 1 2 3 1 4 2 5 3 4 3 · 3 frames | FIFO **5** · LRU **8** · Ótimo 5 → aqui o FIFO é melhor |
| 23b | 1 2 3 1 4 1 3 2 3 3 · 3 frames | FIFO **7** · LRU **5** · Ótimo 5 → aqui o LRU é melhor |
| 28 | 0 1 7 2 3 2 7 1 0 3 · 4 frames | FIFO **6** · LRU **7** |
| 27 | Mesma tabela de 4 frames, três algoritmos | NRU → frame 0 (BR=0, BM=0) · FIFO → frame 2 (carga mais antiga, 120) · LRU → frame 1 (último acesso mais antigo, 260) |

O Exercício 27 é um bom exemplo rápido de que **cada algoritmo escolhe uma vítima diferente** para a mesma situação.

---

## 5. Banco de perguntas para a arguição

Respostas curtas, todas baseadas no Cap. 10 (a maioria vem da seção 10.9 – Exercícios).

1. **O que é memória virtual e quais os benefícios?** Combina RAM e disco para dar a ilusão de uma memória maior que a real. Permite programas maiores que a RAM, mais processos na memória ao mesmo tempo, melhor uso da CPU e menos fragmentação.
2. **Como o endereço virtual vira real?** A MMU separa NPV e deslocamento, usa o NPV como índice na tabela de páginas do processo, pega o endereço do frame e soma o deslocamento.
3. **Por que mapear em blocos e não por endereço?** A tabela teria o tamanho do próprio espaço virtual. Ex.: 2³² endereços com blocos de 4 K resultam em só 2²⁰ entradas.
4. **Página virtual × página real?** A virtual é um bloco do espaço de endereçamento do processo; a real (frame) é um bloco da memória física do mesmo tamanho.
5. **Para que serve o bit de validade?** Indica se a página está na RAM (1) ou não (0). Se for 0, ocorre page fault.
6. **O que é page fault e quem controla?** É a referência a uma página que não está na RAM. A MMU detecta pelo bit de validade e o SO trata (page in). Uma taxa alta gera muita E/S e derruba o desempenho.
7. **A rotina que trata page faults pode sair da memória?** Não. Se ela estivesse no disco, não haveria quem tratasse o page fault que a traria de volta. Páginas do núcleo ficam bloqueadas e nunca são substituídas.
8. **Que fragmentação existe na memória virtual?** Paginação: **interna** (sobra espaço na última página). Segmentação: **externa** (áreas livres pequenas demais entre segmentos).
9. **Paginação por demanda × antecipada?** Demanda carrega só o que é referenciado. Antecipada carrega páginas extras: economiza leituras em disco, mas pode desperdiçar tempo e memória.
10. **Alocação fixa × variável?** Fixa é simples, mas o limite pode ser pequeno demais (muitos page faults) ou grande demais (poucos processos). Variável se adapta à taxa de paginação, mas o SO precisa monitorar os processos.
11. **Por que existe política de substituição? Local × global?** Quando não há frame livre, alguma página tem que sair. Local: só páginas do próprio processo. Global: qualquer página, exceto as bloqueadas do núcleo.
12. **Para que serve o bit de modificação?** Indica se a página mudou. Se não mudou, pode ser descartada sem page out, o que economiza uma gravação em disco.
13. **Como a localidade viabiliza a memória virtual?** O programa concentra os acessos em poucas páginas por vez, então basta manter essas páginas na RAM.
14. **Por que programas não estruturados paginam mais?** Têm pouca localidade: saltam para regiões distantes e precisam de páginas que não estão na memória.
15. **O que é working set?** É o conjunto de páginas referenciadas numa janela de tempo Δt (Denning). Mantê-lo na RAM evita o thrashing.
16. **FIFO × LRU?** FIFO: simples e barato, mas pode tirar páginas antigas que ainda são muito usadas. LRU: resultado próximo do ótimo, mas caro (precisa registrar o momento de cada acesso).
17. **Por que o algoritmo ótimo não é implementado?** O SO não conhece o comportamento futuro do programa. O ótimo serve só para comparar os outros algoritmos.
18. **Qual a vantagem do buffer de páginas modificadas (OpenVMS/Windows)?** A página retirada continua na memória por um tempo: se for referenciada de novo, volta ao processo sem acessar o disco. Além disso, a gravação no disco pode ser adiada.
19. **Por que páginas pequenas aumentam a taxa de paginação?** Cada página traz menos código, então são necessários mais page faults para cobrir a mesma região do programa.
20. **Vantagem das tabelas em múltiplos níveis?** Só as tabelas realmente usadas ficam na RAM. Um processo pequeno ocupa pouquíssimas tabelas, em vez de uma tabela única de 4 MB.
21. **O que é a TLB e por que funciona com poucas entradas?** É uma cache de traduções recentes. Por causa da localidade, poucas páginas são usadas por vez, então a taxa de acerto é alta.
22. **Como funciona a tradução na segmentação com paginação?** O NSV indica a entrada na tabela de segmentos, que aponta para a tabela de páginas do segmento. O NPV indica o frame, e o deslocamento é somado ao endereço do frame.
23. **Page out × swap out?** Page out grava uma página modificada. Swap out tira o processo inteiro da memória.
24. **Que processos o SO escolhe para swap out?** Primeiro os que estão em espera; se não bastar, os prontos de menor prioridade.
25. **O que é thrashing?** Transferência excessiva de páginas ou segmentos entre RAM e disco. Acontece no processo (limite menor que o working set ou falta de localidade) e no sistema (mais processos do que memória). A solução definitiva é aumentar a memória principal.

---

## 6. Validação na internet (só para confirmar o livro, como pede o professor)

| O que confirma | Fonte |
|---|---|
| O Windows usa o arquivo de paginação `pagefile.sys` | [Microsoft Tech Community — "What is the page file for anyway?"](https://techcommunity.microsoft.com/blog/askperf/what-is-the-page-file-for-anyway/372608) |
| O Linux mantém listas *active/inactive* de páginas para decidir o que sai da memória | [LWN.net — artigo 495543](https://lwn.net/Articles/495543/) |
| x86-64 com paginação de 4 e 5 níveis (48 e 57 bits) | [The Linux Kernel — x86_64 Support (5-level paging)](https://docs.kernel.org/6.0/x86/x86_64/index.html) |

Antes de colocar esses links no slide de referências, alguém do grupo precisa abrir cada um e anotar título, autor e data de acesso.

---

## 7. Diretrizes visuais (critério 6: "cores suaves, pouco texto, somente tópicos")

- **Paleta suave:** fundo off-white `#F7F7F5`, texto cinza-escuro `#2E3440`, cor principal azul-acinzentado `#4C6A92` e um único destaque suave, como verde-sálvia `#8FB996` (para "hit") ou terracota claro `#D08770` (para "page fault").
- **No máximo 5 tópicos por slide**, cerca de 6 palavras cada. O detalhe fica na fala.
- **Diagramas no lugar de texto:** redesenhar as figuras do livro com a legenda "Adaptado de Machado e Maia (2013), Fig. 10.x".
- Fonte sem serifa, com **24 pt ou mais** no corpo.
- Rodapé com o número do slide e o nome do bloco (ajuda quem assume na troca de apresentador).

---

## 8. Referências (ABNT)

MACHADO, Francis Berenger; MAIA, Luiz Paulo. **Arquitetura de sistemas operacionais**. 5. ed. Rio de Janeiro: LTC, 2013.

DENNING, Peter J. The working set model for program behavior. **Communications of the ACM**, v. 11, n. 5, p. 323–333, 1968. *(citado pelo livro na seção 10.4.4)*

Links de validação: completar conforme a seção 6, com "Disponível em: <URL>. Acesso em: dd mmm. 2026."

*Outras obras impressas (só citar se o grupo consultar de fato):* TANENBAUM, A. S.; BOS, H. **Sistemas operacionais modernos**. 4. ed. São Paulo: Pearson, 2016. · SILBERSCHATZ, A.; GALVIN, P. B.; GAGNE, G. **Fundamentos de sistemas operacionais**. 9. ed. Rio de Janeiro: LTC, 2015.

---

## 9. Próximos passos

1. O grupo revisa este roteiro e ajusta o que quiser.
2. Montar os slides seguindo as seções 3 e 7.
3. Fazer um ensaio cronometrado (meta: 26 min) com o sorteio simulado de 3 pessoas.
4. Exportar em PDF e enviar ao professor por e-mail **antes** do início da apresentação.
5. Confirmar a data (25 ou 26/11).
