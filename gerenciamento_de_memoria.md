# Gerenciamento de Memória

## Abstração de Memória

Inicialmente não se existia abstração de memória, existindo apenas a memória física a qual o computador tinha acesso direto. Por esse motivo não era viável a multiprogramação (presença de inúmeros usuários, processos e terminais simultâneamente, acessando a memória e fazendo uso dos recursos do sistema operacional).
Mesmo assim era possível várias implementações do acesso à memória como pode ser visto na imagem abaixo

![](assets/acesso_a_memoria_fisica.png)


Figura A : Presente em computadores antigos, sistema operacional presente na memória de acesso aleatório

Figura B : Presente em sistemas embarcados e de menor porte, sistema operacional presente em memória apenas de leitura gravada em fábrica

Figura C : Presente nos primeiros computadores pessoais

Tanto os modelos A e C apresentam uma vulnerabilidade à integridade do sistema operacional por estar presente em memória RAM, sendo passível de alteração pelo usuário.

## Problema de Endereçamento pós-Swapping

Um dos maiores problemas no endereçamento de memória quando o sistema faz acesso direto ao mesmo, é quando um processo aponta para um endereço a fim de realizar uma instrução neste, o sistema realiza um swapping e o novo processo carregado na memória principal (em uma posição diferente de 0) aponta para a memória física a partir do endereço 0 e não usando como base seu real endereço.

![](assets/problema-pos-swap.png)

Uma forma de resolver isso é através da realocação estática, em que ao ser carregado na memória, o programa terá uma constante referente à sua posição na memória, incrementada para cada endereço apontado em suas instruções. Apesar da solução resolver o problema, ela apresenta problemas de performance ao sistema.

## Espaços de Endereçamento

Surgiu-se então os espaços de endereçamento, uma abstração em que cada processo possa alocar endereços da memória de forma que estes estejam protegidos e que a visibilidade da memória em relação aos processos, seja limitada ao escopo local deste (com exceção de cirtunstâncias as quais dois ou mais processos utilizam de memória compartilhada) 

## Registradores Base e Registradores Limite

Uma solução que foi aplicada para resolver o problema de conflito de endereçamento foi através dos registradores base e limite, os quais são usados quando um programa é carregado na memória. O registrador base é carregado com o endereço físico onde o programa começa na memória e o registrador limite carrega o comprimento do programa, para cada programa a ser carregado na memória o registrador base apresentará um valor cada vez maior, supondo que um programa será carregado em endereços posteriores aos outros.

A grande vantagem dessa implementação é que os registradores oferecem para cada processo, um espaço de endereçamento local, protegido e logicamente separado dos outros endereçamentos.

Toda vez que um processo referenciar a memória, a CPU irá adicionar o valor base ao endereço gerado para o processo antes de enviá-lo para o barramento de memória, assim como verificar se este valor é igual ou maior do que o valor do registrador limite.
Essas operações são uma desvantagem na implementação, pois tornam o sistema mais lento devido ao tempo de propagação de transporte presente na adição (carry-propagation time).

## Swapping (troca de processo) e Memória Virtual

A quantidade de processos que se encontra presente apenas para realizar funções de inicialização do sistema operacional e dos programas de usuários, é tão grande que não é possível alocar alocar todos esses processos na memória principal. Ou seja : a demanda de memória RAM é extremamente alta desde a inicialização do sistema.

Algumas estratégias foram desenvolvidas para solucionar este problema, uma delas é através do swapping, em que um processo é carregado a partir do disco para a memória principal em sua totalidade, é executado por um tempo e então retornado ao disco, dando espaço para execução de outros.

Outra forma de resolver esse problema é através da memória virtual, o qual os programas podem ser executados mesmo estando parcialmente na memória principal.


