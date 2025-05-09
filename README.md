# Pong-FPGA
nesse projeto, experimento com a altera DE2-115 em uma tentativa de recriar o jogo "pong" dentro de uma FPGA, na linguagem Verilog. Aqui terá um guia compreensivo sobre como utilizar 
na placa mencionada o cabo VGA, junto de alguns erros que eu tive na época em que criei esse projeto. Esse projeto já está disponível em meu GitHub pessoal, e pode ser acessado pelo link:
https://github.com/SillyGo/Pong.Verilog

Enfim, vamos às explicações sobre os principais tópicos!

## 2. PROTOCOLO DE COMUNICAÇÃO VGA:
Esse é um dos protocolos de comunicação mais simples que tem, e é muito bom para projetos menores como esse. Enfim, vamos direto ao ponto e explicar como ele funciona

![image](https://github.com/user-attachments/assets/93da0790-8487-4593-800a-aa32f6bdb395)

(imagem tirada de https://forum.digikey.com/t/vga-controller-vhdl/12794)
como é possível ver na imagem acima, temos que nos preocupar com algumas coisas, que serão descritas a seguir:
### VSYNC E HSYNC:
No VGA, as imagens são desenhadas que nem se lê um livro (considerando o sentido de leitura da língua portuguesa), ou seja, da esquerda para a direita, de cima para baixo. Portanto,
necessitamos de algo que indique à linha sendo desenhada que ela acabou, e que devemos prosseguir para a próxima linha da imagem, e de algo que indique que uma imagem acabou, ou seja,
que já desenhamos a quantidade máxima de linhas permitidas pela resolução atual. Essa função é desempenhada por esses dois sinais, com o HSYNC indicando o limite horizontal da imagem (ou
seja, que uma linha acabou, e que devemos prosseguir para a próxima linha) e o VSYNC indicando que chegamos no limite vertical da imagem (e que desenhamos o número máximo de linhas da 
imagem).
Para indicar oque eles devem indicar, basta criar um pulso de '0' em cada um deles, enquanto no caso do display, eles devem estar no nível lógico '1'. 
Essa lógica será mostrada em uma imagem mais a frente, tirada do user manual da altera DE2-115. Ilustrando um diagrama de tempo desses sinais.
### FRONT PORCH E BACK PORCH:
é como se fosse o tempo de reação, os sinais de VSYNC e de HSYNC permanecem no nível lógico '1', porém, a imagem não está sendo mais desenhada, portanto, antecedem os pulsos do HSYNC e do 
VSYNC (no caso do front porch) e o display da imagem / da nova linha criada (no caso do back porch)

![image](https://github.com/user-attachments/assets/a744fc56-b5f6-425e-a972-e62957b541ce)

^imagem ilustrando o pulso dos sinais de HSYNC e VSYNC, aqui, o 'Sync(a)' significa o intervalo de pulso dos VSYNC e HSYNC.
Isso é basicamente tudo sobre o protocolo! 

## 3. Mas a vida não é um morango )>:
Aqui, eu vou detalhar um erro que eu estava tendo no começo da minha aventura nesse tópico:
Basicamente, oque estava acontecendo: eu configurei o protocolo VGA em Verilog, conectei os pinos no pin planner do Quartus, e ainda assim, era como se o monitor não reconhecesse nada,
quando já era para ele indicar a existência de um dispositivo conectado. Porque isso aconteceu?
basicamente, meu erro (assim creio eu) era não estar configurando também os pinos do DAC (digital to analog converter) da placa. (a importância do DAC surge no fato que os pixels da imagem
tem suas cores dadas em RGB, que são sinais analógicos. Mas a FPGA só gera sinais digitais, como resolver isso? por meio de um DAC, que já existe na placa, e converte informação digital,
como, por exemplo, um registrador contendo um número, em um sinal analógico, que é enviado para o monitor) O dac da altera é o ADV7123, e seu datasheet pode ser acessado por uma simples
pesquisa no google. Em um deles, encontramos a seguinte tabela:

![image](https://github.com/user-attachments/assets/19644837-b952-4ad7-8ec6-689d6c3911b5)

que descreve bem todos os sinais que também constam na tabela de pinos do user manual da DE2-115:

![image](https://github.com/user-attachments/assets/0e23f37e-e26c-4090-8944-386695917386)

desses pinos, os que são importantes para o DAC são os pinos de VGA_SYNC_N, VGA_BLANK_N, e VGA_CLK, e são apresentados na tabela do datasheet do DAC como simplesmente "Sync", "Blank", e
"CLOCK", configure eles e, caso não hajam outros problemas, o monitor já deve exibir que existe um dispositivo ligado a ele via VGA e, acessando as configurações do monitor, é possível
também ver a resolução escolhida (detalhe, a tabela com as resoluções e suas respectivas frequências de pulso do HSYNC e VSYNC também estão disponíveis no user manual da DE2-115). Nesse
ponto, se estiver tudo certo, podemos prosseguir para a próxima etapa, que também é a mais fácil, que é a tarefa de associar a cada pixel uma cor.

## 4. CORES, FINALMENTE:
rfrfrfrf




















