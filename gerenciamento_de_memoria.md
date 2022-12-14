# Gerenciamento de Memória

## Abstração de Memória

Inicialmente não havia abstração de memória, existindo apenas a memória física a qual o computador tinha acesso direto. Por esse motivo não era viável a multiprogramação (presença de inúmeros usuários, processos e terminais simultâneamente, acessando a memória e fazendo uso dos recursos do sistema operacional).
Mesmo assim era possível várias implementações do acesso à memória como pode ser visto na imagem abaixo

![](assets/acesso_a_memoria_fisica.png)


Figura A : Presente em computadores antigos, sistema operacional presente na memória de acesso aleatório

Figura B : Presente em sistemas embarcados e de menor porte, sistema operacional presente em memória apenas de leitura gravada em fábrica

Figura C : Presente nos primeiros computadores pessoais

Tanto os modelos A e C apresentam uma vulnerabilidade à integridade do sistema operacional por estar presente em memória RAM, sendo passível de alteração pelo usuário.

## Problema de Endereçamento - Memória Física

Um dos maiores problemas no endereçamento de memória quando o sistema faz acesso direto ao mesmo, é quando um processo aponta para um endereço a fim de realizar uma instrução neste, o sistema realiza um swapping e o novo processo carregado na memória principal (em uma posição diferente de 0) aponta para a memória física a partir do endereço 0 e não usando como base seu real endereço.

![](assets/problema-pos-swap.png)

Uma forma de resolver isso é através da realocação estática, em que ao ser carregado na memória, o programa terá uma constante referente à sua posição na memória, incrementada para cada endereço apontado em suas instruções. Apesar da solução resolver o problema, ela apresenta problemas de performance ao sistema.

## Espaços de Endereçamento

Surgiu-se então os espaços de endereçamento, uma abstração em que cada processo possa alocar endereços da memória de forma que estes estejam protegidos e que a visibilidade da memória em relação aos processos, seja limitada ao escopo local deste (com exceção de cirtunstâncias as quais dois ou mais processos utilizam de memória compartilhada) 

## Registradores Base e Registradores Limite

Uma solução que foi aplicada para resolver o problema de conflito de endereçamento foi através dos registradores base e limite, os quais são usados quando um programa é carregado na memória. O registrador base é carregado com o endereço físico onde o programa começa na memória e o registrador limite carrega o comprimento do programa, para cada programa a ser carregado na memória o registrador base apresentará um valor cada vez maior, supondo que um programa será carregado em endereços posteriores aos outros.

![](assets/registradores_base_limite.png)


A grande vantagem dessa implementação é que os registradores oferecem para cada processo, um espaço de endereçamento local, protegido e logicamente separado dos outros endereçamentos.

Toda vez que um processo referenciar a memória, a CPU irá adicionar o valor base ao endereço gerado para o processo antes de enviá-lo para o barramento de memória, assim como verificar se este valor é igual ou maior do que o valor do registrador limite.
Essas operações são uma desvantagem na implementação, pois tornam o sistema mais lento devido ao tempo de propagação de transporte presente na adição (carry-propagation time).

## Swapping (troca de processo)

A quantidade de processos que se encontra presente apenas para realizar funções de inicialização do sistema operacional e dos programas de usuários, é tão grande que não é possível alocar alocar todos esses processos na memória principal. Ou seja : a demanda de memória RAM é extremamente alta desde a inicialização do sistema.

Algumas estratégias foram desenvolvidas para solucionar este problema, uma delas é através do swapping, em que **um processo é carregado a partir do disco para a memória principal em sua totalidade, é executado por um tempo e então retornado ao disco, dando espaço para execução de outros.**

Outra forma de resolver esse problema é através da memória virtual, o qual os programas podem ser executados mesmo estando apenas parcialmente na memória principal.

![](/assets/troca_processos.png)

Ao realizar o swapping do mesmo processo muitas vezes, este muito provalvelmente não será alocado ao seu endereçamento anterior, sendo assim é trabalho do hardware durante a execução, realocar os endereçamentos conforme sua posição.

## Alocação de Memória e Tamanho dos Processos

Quando um processo é alocado na memória, o sistema operacional deve trabalhar para alocar a memória que for suficiente para este, levando em consideração alguns fatores.

Caso o processo seja criado com um tamanho fixo, satisfazer essa necessidade é o que deve ser feito sem maiores conflitos. 

No entanto, caso o processo apresente a possibilidade de seus dados crescerem a memória será alocada dinâmicamente, de forma que um espaço adjacente ao processo pode ser alocado e o processo poderá crescer. 

Por outro lado, caso o espaço adjacente já pertença à outro processo, o processo de tamanho dinâmico deve ser movido para um espaço grande o suficiente na memória, se isso não for possível então o processo deve ser temporáriamente suspenso até que o espaço seja liberado, ou mesmo morto. 

Como os processos tendem a crescer e utilizar de mais memória, é uma boa ideia alocarmos um pouco mais de memória para cada processo, na intenção de reduzir a sobrecarga e fluxo na troca de processos em espaços insuficientes.

Mas para que isso seja eficar, essa memória temporária não deve ser transferida junto com os processos ao disco. Apenas a memória realmente em uso deve ser transferida para o disco

### Compactação de Memória

Durante a troca de processos, múltiplos espaços na memória são ocupados e desocupados, com uma tendência de gerar lacunas ou pequenos espaços vazios entre os endereços ocupados. É possível acumular esses pequenos espaços vazios em um grande espaço vazio, movendo os processos para uma direção (para cima ou para baixo) o máximo possível. No entanto, **o processo de compactação de memória tende a exigir muito processamento e tempo de execução da CPU**, proporcional à largura da memória principal



## Gerenciamento da Memória Livre

Ao ser designada dinâmicamente, a memória principal passa a ser gerenciada pelo Sistema Operacional. Esse gerenciamento pode ser implementado de duas formas : mapas de bits e listas encadeadas.

### Mapas de bits

Através do mapa de bits, a memória é dividida em unidades muito pequenas que são mapeadas e identificadas como ocupadas ou não (1 ou 0).

Uma informação muito importante é o tamanho de cada unidade de alocação. Quanto menor for seu tamanho maior será o mapa de bits, sendo assim podemos também afirmar que quanto maior for a unidade de alocação, menor será o tamanho do mapa de bits, no entando uma quantidade considerável de memória será perdida caso o tamanho não seja múltiplo exato da unidade de alocação.

![](assets/mapas_bits.png) 

Uma desvantagem é que quando for carregar um processo na memória com tamanho de *x* unidades, o gerenciador de memória irá buscar no mapa por uma sequẽncia de *x* bits 0 consecutivos, no entanto essa busca de um comprimento determinado é lenta.


### Listas Encadeadas

Outra forma de controlar o uso da memória é através de listas encadeadas que estarão guardando informações referente aquele endereçamento de memória.

Através da lista encadeadas, é possível armazenar se o endereço se encontra ocupado por um processo ou se está livre, assim como onde ele começa e o seu comprimento, sem mencionar que cada endereçamento passa a ter uma referência do próximo, mesmo ele estando livre ou ocupado 

![](assets/lista_encadeada.png)

Ao implementar uma lista encadeada para gerenciar a memória, é possível determinar critérios de ordenação. Caso os processos e espaços livres sejam ordenados por seus endereços, é possível implementar algoritmos para alocação de memória.

Para os algoritmos mencionados abaixo, leve em consideração que a MMU possui a informação de quanto de memória deve ser alocada

### First Fit (primeiro encaixe)

A MMU examina a lista de segmentos buscando por um espaço livre que seja grande o suficiente para o processo ser alocado. Após encontrado, a menos que o tamanho seja exatamente o mesmo necessário pelo processo (algo muito incomum), o espaço livre então é dividido entre 2 partes, uma que será composta pelo processo e a outra que permanecerá livre.
 
### Next Fit 

Uma pequena variação do First Fit. Invés de percorrer toda a lista, o algoritmo passa a percorrer a partir de onde havia parado anteriomente. A partir de simulações realizadas por Bays(1977), foi demonstrado que o Next Fit tem um desempenho ligeiramente pior do que o First Fit


### Best Fit 

O Best Fit faz uma busca total na lista para escolher o menor espaço livre que seja adequado para alocar o processo. Invés de selecionar o primeiro espaço livre que encontrar, é selecionado o espaço livre mais próximo do tamanho do processo, de forma que evite a ocupar grupos de espaços maiores, permitindo que esses sejam usados em situações propícias

Este algoritmo é mais lento do que o First Fit pois pra cada chamada deve percorrer a lista inteira, e surpreendentemente também é o que resulta em maior desperdício, pois passa a preencher espaços minúsculos e irrelevantes na memória

### Worst Fit

Este algoritmo busca utilizar os espaços em que se sobrarão maior espaço livre, apesar de aparentar um péssimo critério, isso favorece para que se mantenha frequente os espaços maiores na memória em que outros processos irão utilizar, diminuindo a fragmentação

### Quick Fit 

Este algoritmo diferente dos outros, implementa uma lista em cada um de seus nós as quais armazena os endereçamento referente à cada comprimento que for solicitado, de forma que se torna extremamente rápido a busca entre os espaços disponíveis para alocação

![](assets/quick_fit.png)

## Memória Virtual

Com o passar do tempo uma alta demanda por memória foi surgindo, por consequência do crescimento dos softwares e a multiprogramação, se tornou fácil que um único processo pudesse ocupar toda a memória ou até que isso seja insuficiente. Uma forma de solucionar isso foi através da divisão dos programas em módulos pequenhos, conhecidos como sobreposições.

Através de um gerenciador de sobreposições, os programas eram gerenciados e carregados do disco para a memória.

Mas essa solução não era totalmente efetiva, pois a divisão do programa em módulos devia ser feita pelo programador e era propensa a muitos erros.

Através da memória virtual esse problema pôde ser solucionado com efetividade. Cada programa tem seu próprio espaço de endereçamento que é dividido em blocos chamados de páginas, que nada mais é do que uma série de endereçamentos. 

As páginas são mapeadas na memória física, porém não precisam se encontrar todas na memória ao mesmo tempo para o programa executar. Quando o programa referencia um endereço que está na memória física, o hardware realiza o mapeamento necessário com agilidade, caso *não* esteja presente na memória (page fault), o sistema operacional é alertado e é realizado uma busca pelo quadro de página que faltou, após isso é reexecutado a instrução que falhou. A memória virtual funciona muito bem quando implementada em sistemas de multiprogramação, pois pequenos espaços de endereçamento são mapeados e podem ser alocados simultâneamente conforme a necessidade dos processos que se encontram em execução 


### Paginação

O espaço de endereçamento virtual consiste em unidades de tamanho fixo chamadas de páginas. Unidades na memória física são chamadas de quadros de páginas. Geralmente ambos são do mesmo tamanho.

Os sistemas operacionais atuais, oferecem a possibilidade de utilizar combinações de tamanhos variados para cada necessidade do sistema 
Ex : 12 MB para aplicações do usuário e 4 GB para o núcleo.  

Quando um processo realiza a instrução ``` MOV REG,0 ```, o endereço virtual 0 é enviado para a MMU e está detecta a página em que situa o processo em execução e então retorna o endereço físico

Ex : ``` MOV REG,0 ``` executado na página 3. Levando em consideração que cada página possui o comprimento exato de 4096 endereços, podemos concluir que o processo se encontra entre os endereços físicos 12288 à 16324. Logo a instrução ```MOV REG,0``` será alterada para ```MOV REG,12288```, que é o valor referente ao espaço de endereçamento alocado para o processo, seu endereçamento local/virtual índice 0

Os endereços virtuais (presente no espaço de endereçamento virtual) não é direcionado ao barramento da memória, eles referenciam à MMU que mapeia estes endereços virtuais e retorna o endereço físico absoluto. 

No entanto ainda temos um problema a ser resolvido : a memória virtual continua sendo maior que a memória física, e o hardware como forma de identificar quais estão presente de fato na memória física, utiliza de um bit sinalizando Presente/Ausente.

### Tabelas de Páginas

A tabelas de páginas é a estrutura responsável pra realizar o mapeamento das páginas virtuais nos quadros físicos. Podemos associar a tabela de página à uma função matemática que recebe o índice da página virtual como um parâmetro e seu resultado é o índice do quadro físico. Dessa forma, qualquer endereço virtual poderá ser convertido em um endereçamento físico.

### Estrutura de  <mark> Entrada da Tabela de Páginas

Normalmente 32 bits é o valor referente ao tamanho de uma entrada na tabela de página, no entanto esse número costuma variar. Outras informações mais relevantes são os componentes presentes na entrada da tabela : o número do quadro da página é o mais importante pois é usado para realizar o mapeamento. Também é informado um bit de presente/ausente o qual caso for 1 indica que o endereço está presente na memória, mas caso seja 0, indica que a página virtual não se encontra na memória e realizar uma entrada com essa página de tabela resultará em um page fault. Também é presente o campo de permissão, o qual possui 2 bits que informam se é permitido acesso de leitura e escrita, ou mesmo 3 bits para incluir o acesso de execução.

Sempre que uma página receber uma alteração em seu conteúdo o bit de modificada será configurado pelo hardware, esse bit é muito importante pois indica se a página foi alterada e tem alguma diferença para sua versão em disco. Quando o sistema for buscar por outro quadro na memória, irá validar se a página a qual for retirada no endereçamento virtual foi modificada ou não (é suja ou não). Caso sim, o sistema registra sua alteração no disco, caso não então a página apenas será abandonada, pois no disco já está presente seu conteúdo e nenhuma alteração deve ser feita. 

O bit referenciada é usado para indicar quais páginas são referenciadas para operações de leitura ou escrita, isso auxilia o Sistema Operacional a escolher qual página deve ser retirada da memória virtual em virtude de outra, de forma em que o Sistema Operacional priorize retirar páginas não referenciadas, a fim de evitar um possível page fault.

O último bit determina se a cache deve estar habilitada ou não. Alguns serviços ou programas podem necessitar a coleta de dados de E/S, de forma que utilizar a cache (um dado relativamente antigo e não recém informado pelo usuário) possa ser prejudicial por passar dados inválidos ou incoerentes. A validação da cache é um dos problemas computacionais mais presentes nos sistemas hoje em dia, envolvendo ambientes desktop com o cenário dos sistemas operacionais e até mesmo em páginas e servidores web.

Obs : O endereço de disco o qual contém a página quando está não se encontra na memória não é referenciado na tabela de páginas, pois a tabela de páginas armazena apenas os endereços virtuais para que a MMU possa realizar o mapeamento correto entre a memória virtual e a memória física.

## Implementação da Memória Virtual

Durante a implementação da memória virtual, alguns requisitos devem ser atentidos, tais como rapidez no mapeamento do endereço virtual para o endereço físico, assim como o gerenciamento eficaz das dimensões do espaço de endereçamento virtual, pois caso esse seja grande então a tabela também será grande.
Em situações da execução de instruções armazenadas na memória, caso o tempo de mapeamento seja lento, isso irá ocasionar um gargalo na exeução de cada instrução. 


### TLB (Translation Lookaside Buffer)

Uma forma de solucionar o problema de performance ocasionado pelo mapeamento e dimensão dos endereçamentos, é através do TLB, um hardware implementado dentro da MMU que realiza o mapeamento dos endereços virtuais para os endereços físicos sem o uso das tabelas de páginas. Sua entrada é composta por dados referente à cada página, tais como o número da página, bit de alteração da página, código de permissões e o quadro físico referente à página. 

Quando um endereço virtual é informado para a MMU, esta verifica se o endereço virtual já se encontra em uma das entradas da TLB. Caso encontre e os bits de proteção sejam respeitados, é retornado o quadro da página através da TLB, sem que haja necessidade de acessar a tabela de páginas. Caso haja um código de proteção permitindo leitura e/ou execução, porém o sistema busca realizar uma operação de escrita, é gerado uma exception. No caso da página não estar presente na TLB, a MMU realiza a busca na tabela de páginas e retira uma das páginas armazenadas na TLB, substituindo-á pela página buscada. 

### TLB Por softwares

Todo o mapeamento da TLB é feito através do hardware dos computadores, com exceção de máquinas RISC (Computador com conjunto de instruções reduzidos) o qual o gerenciamento é feito em software, as entradas da TLB são carregadas pelo SO e no caso de uma ausência da TLB, é gerado uma falha e o problema é conduzido ao SO, que deve buscar pela página, substituir uma entrada da TLB pela página e então reexecutar a instrução que falhou.

Uma TLB relativamente grande apresenta de forma surpreendente um bom desempenho, gerando um ganho através da simplificação da MMU, ofertando mais recursos da CPU para quaisquer outros requisitos que podem melhorar a performance do sistema. Além disso, foram implementadas várias formas de otimizar a TLB por software, um exemplo disso é a busca antecipada que o SO realiza por uma página que em breve será usada, carregando na TLB permitindo sua disponibilidade antes que ocorra uma ausência. Outra implementação é uma cache de entradas para a TLB, evitando novamente a busca por páginas na tabela de páginas.


### Ausência na TLB

Existem várias formas de ocorrer uma ausência de TLB e suas classificações, uma delas é a **soft miss** (ausência leve), a qual a página se encontra na memória mas não na TLB, sendo necessário apenas que a TLB seja atualizada, levando aproximadamente 10 à 20 instruções em alguns nanossegundos. Já o **hard miss** (ausência completa) ocorre quando a página não se encontra na memória, sendo necessário realizar uma busca no disco, levando alguns milisegundos (milhares de vezes mais lento do que o soft miss).


## Algoritmos de Substituição de Páginas

Para que o SO apresente uma performance satisfatória, é necessário implementar algoritmos de substituição de páginas visando um principal critério : quais páginas devem ser removidas para que novas sejam inseridas na memória ? Nos exemplos abaixo.


### Algoritmo Ótimo

Fácil de descrever mas difícil de ser implementado, esse algoritmo consiste na ideia de que cada página será rotulada com um número referente às instruções que serão executadas antes dela, de forma que o SO irá retirar a página com maior rótulo, adiando o evento de page fault o mais tardar possível. 

A maior dificuldade no entanto é conseguir prever os rótulos referente a cada página, sendo necessário uma primeira exeução do programa para guardar estes dados e então assim na segunda, realizar uma otimização com base na substituição correta das páginas que serão usadas mais tarde pelas páginas já solicitadas. 


### Algoritmo de Páginas Não Usadas Recentemente (NRU)

O Sistema Operacional faz uso dos Bits R e M (Referenciado e Modificado, presente em cada entrada de uma página) para que em uma falta de página, inspecione as páginas e as divide em categorias baseada em seus valores atuais, sendo as categorias definidas como :

Classe 0: não referenciada, não modificada.
Classe 1: não referenciada, modificada.
Classe 2: referenciada, não modificada.
Classe 3: referenciada, modificada.

O NRU dará prioridade na remoção para as classes mais baixas, ou seja, ele prioriza páginas não referenciadas do que as páginas modificadas (no caso de serem páginas modificadas, é necessário realizar que estas sejam escritas no disco). O NRU é fácil de compreender, implementar e oferece um bom desempenho.

### First In First Out (FIFO / Fila) - Segunda Chance

Outro algoritmo também de fácil compreensão é o FIFO. O SO possui uma lista encadeada das páginas armazenadas na memória, em uma sequência das mais antigas para as mais recentes, de forma que quando necessário remover uma página será removida a primeira da fila, e a nova página a ser incluida, será incluida no final da fila. No entanto sua forma mais pura de implementação apresenta um problema no desempenho do sistema, pois apesar de antiga, uma página no começo da fila pode estar sendo muito referenciada.

Uma modificação para o algoritmo FIFO é o algoritmo de Segunda Chance, o qual antes do ato de remover uma página antiga X presente no índice 0, é analisado o bit R de X. Caso o bit R seja 0 então X é pouco referenciada e deve ser removida da memória (caso X esteja suja, será necessário que seja escrita no disco), caso seja 1 então X é movida para o final da fila, pois foi recentemente referenciada e ainda pode ser requisitado seu uso. A busca por uma página não referenciada será continuada a partir do elemento antes no índice 1, agora no índice 0. Caso todas as páginas tenham o bit R como 1, então o algoritmo fará uso do conceito FIFO em sua forma mais pura. No entanto essa modificação é extremamente ineficiente pois realiza inúmeras instruções em sua lista no que diz respeito à mover as páginas de posição.

### Algoritmo do Relógio

Outra alternativa ao Segunda Chance é o algoritmo de relógio, semelhante ao citado anteriormente, este também analisa se as páginas estão sendo referenciadas através do bit R, no entanto esse algoritmo não realiza instruções para movimentação dos elementos da lista. 
Conforme descrito pelo nome, os quadros de páginas permanecem em uma lista circular no formato de um relógio, e um ponteiro é usado para analisar cada elemento, sendo então evitado o uso da CPU nas instruções de movimentação de cada página.


### Algoritmo de Páginas Usadas Menos Recentemente (LRU)

Uma boa estratégia (relativamente próxima do algoritmo ótimo), é a LRU, o qual consiste da premissa em que páginas que foram usadas com alta frequência recentemente, continuarão sendo usadas futuramente, assim como páginas que não usadas há um bom tempo, continuarão sendo não usadas. Dessa forma o sistema deve priorizar a retirada de páginas que não são usadas pelo maior tempo registrado.

No entanto, esse algoritmo apresenta um custo caro para sua implementação, sendo necessário o uso de uma lista encadeada para todas as páginas da memória, de forma que a primeira página da lista seja a página mais recente a ser referenciada, e a não referenciada há mais tempo no final da lista.
Como a cada referencia de memória é necessário que a lista seja atualizada e muito provalvelmente sua ordem seja alterada, este algoritmo apresenta um custo muito alto de recursos do hardware, mesmo utilizando de hardwares especiais para isso (um exemplo seria um contador para cada página da lista, a qual iria incrementar a cada referência daquela página. Assim que fosse necessário remover uma página para adicionar outra, o sistema analisa os contadores em busca da página menos recente a ser referenciada).

###  <mark> Algoritmo do Conjunto de Trabalho 

Durante a execução de um processo, este passa a necessitar de determinadas páginas referentes à dados locais, bibliotecas, informações de execução e muitas outras informações.
A ideia do conjunto de trabalho, é que seja importado as páginas referentes à este processo na memória, antes que o processo seja colocado para executar, de forma que minimize o tempo de paginação do processo (paginar um processo antes que este seja colocado para executar, é conhecido como pré-paginação)

### Algoritmo WSClock

O WSClock é um algoritmo bem performático que se baseia no algoritmo de relógio e usa informações do conjunto de trabalho, sendo de fácil implementação e alto uso nos sistemas modernos.

Inicialmente uma lista circular de quadros de páginas se encontra vazia, conforme as páginas são carregadas na memória, elas são adicionadas na lista formando um círculo.

Cada página possui o instante de seu último uso assim como o bit R e M. 
A verificação de qual página será substituida se dá pelo ponteiro, este que já se encontra apontando para uma página, verifica se o bit R desta página é 1, caso seja então não é uma página ideal para ser substituida, o SO altera o bit R para 0 e passa a verificar as outras páginas da lista até encontrar uma com bit R = 0. 
Ao encontrar uma página com bit R = 0, verifica se sua idade é maior do que o tempo de execução do processo e se a página se encontra limpa, caso verdadeiro então essa página não faz parte do conjunto de trabalho e uma cópia se encontra em disco, sendo assim o SO realiza a substituição da página. Caso a página esteja suja então o processo de escrita em disco é escalonado e o ponteiro segue a busca com a página seguinte, a fim de evitar um chaveamento de processo. Caso todas as páginas se encontrem limpas e ambas tenham idade menor do que o tempo de execução do processo, então todas fazem parte do conjunto de trabalho e qualquer uma deve ser selecionada para a substituição, tendo em vista que não é possível otimizar a paginação sob essas cirtunstâncias. 


## Questões de Projeto

### Tamanho de Página 

Conforme as páginas são alocadas, ocorre um desperdício devido ao espaço alocado resultar em um espaço de sobra que não é utilizado pelo processo, isso ocorre porque dados, algoritmos e processos, nunca ocupam um número inteiro de páginas, ocorrendo o que chamamos de **fragmentação interna**.

Por dois motivos o ideal é termos tamanho de páginas relativamente pequenos : um deles é a fragmentação interna citada anteriormente, outro motivo é que devido a um alto fracionamento do tamanho de páginas, a memória RAM pode ter melhor aproveitamento, pois cada processo irá alocar somente as páginas mais necessárias para seu funcionamento, do contrário haveria um desperdício de memória para alocar mais informações do que necessária.
Se um processo precisa de dados referente à 4KB mas as páginas tem tamanho de 8KB, então será alocado 8KB na memória.

Em compensação as páginas pequenas também trazem prejuízos em performance em algums detalhes : Quando um programa possui páginas pequenas suas tabelas de páginas serão maiores, sem mencionar que o tempo para realizar a transferência de uma página do disco para a memória é em maior parte devido ao posicionamento das partes móveis do HD. Um processo que precisa alocar muitas páginas irá decorrer de um longo período para essa alocação, pois só é possível alocar uma página por vez, caso as páginas fossem maiores e em menores quantidades, o tempo total para alocar as páginas seriam efetivamente menores.

Para que o SO tenha um bom desempenho, é equilibrado o tamanho das páginas de acordo com uma série de fatores, de forma que minimize os danos à performance.


### Espaços Separados de Instruções e dados

Normalmente os espaços de endereçamentos eram usados para armazenar instruções e códigos, assim como dados de execução do programa. Ao longo do tempo isso foi se tornando um problema pois caso o espaço de endereçamento não fosse grande o suficiente, os programadores deveriam buscar saídas alternativas para atender à essa necessidade. 
Uma solução que foi implementada e é usada hoje (em especial na cache do processador L1, pois essa apresenta grande escassez e é de extrema utilidade para a velocidade da CPU), é armazenar os dados em endereços separados das instruções, cada um com sua tabela de paginação independente dos outros e endereços físicos distintos. O SO não apresenta dificuldade na implementação dessa solução e por isso é aplicado até hoje.


## Segmentação 

Durante o trabalho exercido por um compilador, inúmeras tabelas que são alocadas no mesmo endereço virtual vão aumentando seu tamanho de acordo com a necessidade do programa a ser compilado (a tabela de símbolos ou a pilha de chamada das funções, cada uma pode crescer independentemente das outras), podendo ultrapassar seu tamanho pré-determinado caso este programa apresente uma quantidade de entradas de alguma tabela maior do que o normal, ao mesmo tempo que outras tabelas podem não precisar de tanto espaço disponível para alocação. 
Para resolvermos essa situação podemos fazer uso dos segmentos, que são espaços de endereçamento independentes uns dos outros e que podem crescer ou diminuir de acordo com a necessidade do programa a ser compilado, assim como apresentam comprimentos variados.

Os segmentos são alocados em endereços separados uns dos outros, o que significa que vários segmentos podem crescer ao mesmo tempo sem gerar um conflito entre eles.
Outro benefício do uso de segmentos é para a situação de bibliotecas compartilhadas entre vários processos. É possível alocar determinada biblioteca a ser compartilhada, para um determinado segmento que será acessado por vários processos, sendo dispensado a necessidade de armazenar a biblioteca nos endereços, em que os processos que farão uso desta, se encontram. 


