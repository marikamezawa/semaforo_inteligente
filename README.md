# Semaforo Inteligente - G01 T11

# Tropicália
## Integrantes do grupo

- Caio Alcantara
- Eduardo Fidelis
- João Preto
- Pablo Azevedo
- Mariana de Paula
- Kaio Vitor
- Mariella Kamezawa

&nbsp;&nbsp;&nbsp;&nbsp;Neste projeto, será desenvolvido um semáforo inteligente capaz de detectar veículos por meio de um sensor de luminosidade (LDR) e ajustar seu funcionamento automaticamente em resposta às condições ambientais, como o modo noturno. Este sistema irá buscar simular um ambiente urbano no qual dois semáforos interconectados podem comunicar-se para otimizar o fluxo de tráfego. Dividido em duas etapas principais, o projeto abrange a montagem física e programação do semáforo com o LDR, seguida pela criação de uma interface online de controle. Um desafio extra inclui a implementação de dois ESP32 para conectar cada semáforo a uma central Ubidots, melhorando o gerenciamento e monitoramento dos dados em uma rede interconectada.

## 1. Objetivos e Visão Geral do Projeto

Explicação do conceito de semáforo inteligente e as vantagens da adaptação ao modo noturno.

## 2. Especificações Técnicas e Componentes

&nbsp;&nbsp;&nbsp;&nbsp;Para o desenvolvimento do semáforo inteligente, utilizamos os seguintes materiais:

- 2 ESP32
- 2 Leds vermelhos
- 2 Leds Amarelos
- 2 Leds verdes
- 2 Sensor de luminosidade LDL
- 1 Sensor Ultrasônico HCSR04
- Jumpers
- 8 Resistores

## 3. Configuração e Programação do Semáforo com sensores

&nbsp;&nbsp;&nbsp;&nbsp;A seguir, estão as condições que ocorre no nosso sistema inteligente:

1. Um farol não estará verde enquanto o outro estiver simultaneamente

2. Dois sensores LDR que todos os outros componentes irão ditar se os semafóros estarão ligados ou não inclusive eles entre si

3. O sensor LDR que tá perto do sensor ultrasonico irá identificar se está de dia ou de noite, se ele identificar que está de noite, todos os farois ficarão amarelo piscando

4. O segundo LDR está na rua mais estreita, ele serve para identificar se há algum carro parado naquela rua. Se o carro estiver em cima dele irá baixar a luminosidade. Se ele identificar que tem um carro em cima, ele vai fechar o semáforo da avenida e abrir o da rua estreita

5. O semáforo da avenida só irá abrir quando o segundo LDR identificar que não há carro na rua estreita

6. Assim que o semáforo da rua estreita ficar vermelho, o da avenida irá abrir.

7. Se o semáforo da rua estiver fechado automaticamente o da avenida abre, com ressalva se o sensor ultrasonico identificar que tem um usuário na faixa de pedestre de 4,5cm, o da avenida fecha e o da rua abre

8. O ultrasonico vai identificar se tem alguém na faixa de pedestre da avenida



Detalhes da integração do sensor de luminosidade com o código do semáforo.
Explicação sobre como o sensor detecta a presença de veículos simulados.
Descrição do modo noturno e as condições para sua ativação.

## 4. Testes e Ajustes do Sistema Físico

Procedimentos para testar a detecção de luz e o comportamento em modo noturno.

Soluções de ajustes e calibragem para o sensor LDR em diferentes ambientes.

## 5. Desenvolvimento da Interface Online

Requisitos e funcionalidades principais da interface.
Passo a passo para criar uma interface simples e intuitiva para controlar o semáforo.

## 6. Funções de Controle e Monitoramento

Explicação sobre os controles do modo noturno e ajustes de semáforo via interface.
Visualização dos dados do LDR em tempo real.

## Extra: Conexão com ESP32 e Integração com Ubidots

Procedimentos para configurar cada ESP32 e conectar os semáforos à central Ubidots.
Configuração do dashboard Ubidots para monitorar e gerenciar os semáforos.
Benefícios da interconexão para o controle do tráfego.

## Implementação e Testes Finais

Testes de funcionalidade da interface e sincronização entre semáforos conectados.
Depuração e ajustes de comunicação entre ESP32 e Ubidots.