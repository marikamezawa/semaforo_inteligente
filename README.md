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

## 1. Objetivo

&nbsp;&nbsp;&nbsp;&nbsp;Neste projeto, será desenvolvido um semáforo inteligente capaz de detectar veículos por meio de um sensor de luminosidade (LDR) e ajustar seu funcionamento automaticamente em resposta às condições ambientais, como o modo noturno. Este sistema irá buscar simular um ambiente urbano no qual dois semáforos interconectados podem comunicar-se para otimizar o fluxo de tráfego. Dividido em duas etapas principais, o projeto abrange a montagem física e programação do semáforo com o LDR, seguida pela criação de uma interface online de controle. Um desafio extra inclui a implementação de dois ESP32 para conectar cada semáforo a uma central Ubidots, melhorando o gerenciamento e monitoramento dos dados em uma rede interconectada.

## 2. Especificações Técnicas e Componentes

Para o desenvolvimento do projeto, foram utilizados os seguintes materiais:

**Microcontroladores:**

- 1 ESP32

**Atuadores:**

- 2 LEDs vermelhos
- 2 LEDs amarelos
- 2 LEDs verdes

**Sensores:**

- 2 Sensores de luminosidade (LDR)

**Conexões e Resistores:**

- Jumpers
- 8 Resistores

## 3. Configuração e Programação do Semáforo com Sensores

&nbsp;&nbsp;&nbsp;&nbsp;O sistema é controlado por uma lógica programada para gerenciar as condições de operação. As principais condições implementadas são:

**1. Intertravamento entre semáforos:**

- Um semáforo nunca estará verde ao mesmo tempo que o outro.

**2. Operação baseada em luz ambiente (LDR):**

- Um LDR determina se é dia ou noite. Se estiver escuro, ambos os semáforos entram no modo noturno, piscando amarelo intermitentemente.
Prioridade para ruas estreitas:

- O segundo LDR, posicionado na rua estreita, detecta veículos parados.
- Caso detecte um veículo, o semáforo da avenida será fechado e o da rua estreita será aberto.

**3. Abertura da avenida:**

- O semáforo da avenida só abre quando o LDR na rua estreita não detecta mais veículos.

## 4. Representação da Configuração do Semáforo com Sensores

&nbsp;&nbsp;&nbsp;&nbsp;Nesta seção, ficará demonstrado através de imagens, o sistema do semáforo inteligente.

&nbsp;&nbsp;&nbsp;&nbsp;O sensor LDR, identifica se há veículo no semáforo secundário (rua), através da baixa luminosidade (sombra) que o transporte faz ao passar por cima do sensor.

<div align="center">
  <p><strong>Figura 1:</strong> Sistema de detecção de veículo </p>
  <img src="assets\Situação1.png" widht = 80% />
  <p><em>Créditos: material produzido pelo autor (2024)</em></p>
</div>

&nbsp;&nbsp;&nbsp;&nbsp;Quando um véiculo é detectado, o semáforo principal (avenida) é fechado, e o semáforo secundário (rua) é aberto para que os veículos passem.

<div align="center">
  <p><strong>Figura 2:</strong> Abertura do sinal da avenida </p>
  <img src="assets\Situação2.png" widht = 80% />
  <p><em>Créditos: material produzido pelo autor (2024)</em></p>
</div>

&nbsp;&nbsp;&nbsp;&nbsp; Quando não há mais nenhum veículo detectado pelo sensor LDR, o semáforo da avenida volta a ficar verde.

<div align="center">
  <p><strong>Figura 3:</strong> Fechamento do sinal da avenida </p>
  <img src="assets\Situação3.png" widht = 80% />
  <p><em>Créditos: material produzido pelo autor (2024)</em></p>
</div>

&nbsp;&nbsp;&nbsp;&nbsp;Quando há um veículo detectado, o semáforo da rua avre para o veículo passar.

<div align="center">
  <p><strong>Figura 4:</strong> Abertura do sinal da rua </p>
  <img src="assets\Situação4.png" widht = 80% />
  <p><em>Créditos: material produzido pelo autor (2024)</em></p>
</div>

&nbsp;&nbsp;&nbsp;&nbsp;Nessa imagem está o funcionamento geral do semáforo da rua, no qual quando haver um veículo, o sinal abre, e quando não haver um veículo, o sinal fecha.

<div align="center">
  <p><strong>Figura 5:</strong> Sistema de sinal da rua </p>
  <img src="assets\Situação5.png" widht = 80% />
  <p><em>Créditos: material produzido pelo autor (2024)</em></p>
</div>

&nbsp;&nbsp;&nbsp;&nbsp;Para o modo noturno, há um outro sensor LDR, que ao identificar que está noite, pela baixa luminosidade, o modo noturno é ativado.

<div align="center">
  <p><strong>Figura 6:</strong> Modo noturno </p>
  <img src="assets\Situação6.png" widht = 80% />
  <p><em>Créditos: material produzido pelo autor (2024)</em></p>
</div>

&nbsp;&nbsp;&nbsp;&nbsp;No modo noturno, o sinal amarelo fica piscando, com o objetivo de alertar o motorista em horários de baixa visibilidade, e assim aumentando a segurança.

<div align="center">
  <p><strong>Figura 7:</strong> Modo norturno </p>
  <img src="assets\Situação7.png" widht = 80% />
  <p><em>Créditos: material produzido pelo autor (2024)</em></p>
</div>

## 5. Código do Projeto

```cpp
#include "UbidotsEsp32Mqtt.h"

#define TRIG_PIN              19
#define ECHO_PIN              18
#define LDR_RUA_PIN           35
#define LDR_CALCADA_PIN       32
#define LED_GREEN_PIN_1       26
#define LED_YELLOW_PIN_1      25
#define LED_RED_PIN_1         33
#define LED_GREEN_PIN_2       23
#define LED_YELLOW_PIN_2      22
#define LED_RED_PIN_2         21

const char *UBIDOTS_TOKEN = "BBUS-0KKB8VTHx1jUYwZ3rUZJ659aEkq4bq";
const char *WIFI_SSID = "Inteli.Iot";
const char *WIFI_PASS = "@Intelix10T#";
const char *DEVICE_LABEL = "esp32_t11_g01";
const char *VARIABLE_LABEL_RED_1 = "luz_vermelha_1";
const char *VARIABLE_LABEL_YELLOW_1 = "luz_amarela_1";
const char *VARIABLE_LABEL_GREEN_1 = "luz_verde_1";
const char *VARIABLE_LABEL_LDR_RUA = "ldr_rua";
const char *VARIABLE_LABEL_LDR_CALCADA = "ldr_calcada";
const char *VARIABLE_LABEL_DISTANCE = "distance";
const int PUBLISH_FREQUENCY = 5000;

float duration_us, distance_cm;
int luz_calcada, luz_rua;
long int timer;

unsigned long greenStartTime = 0;
unsigned long yellowStartTime = 0;
bool isGreen = false;
bool isYellow = false;

Ubidots ubidots(UBIDOTS_TOKEN);

void callback(char *topic, byte *payload, unsigned int length) {
  Serial.print("Message arrived [");
  Serial.print(topic);
  Serial.print("] ");
 
  String message = "";
  for (int i = 0; i < length; i++) {
    message += (char)payload[i];
  }
 
  Serial.println(message);
}

void setup() {
  Serial.begin(115200);
  ubidots.setDebug(true);
  ubidots.connectToWifi(WIFI_SSID, WIFI_PASS);
  ubidots.setCallback(callback);
  ubidots.setup();
  ubidots.reconnect();
  pinMode(LED_GREEN_PIN_1, OUTPUT);
  pinMode(LED_YELLOW_PIN_1, OUTPUT);
  pinMode(LED_RED_PIN_1, OUTPUT);
  pinMode(LED_GREEN_PIN_2, OUTPUT);
  pinMode(LED_YELLOW_PIN_2, OUTPUT);
  pinMode(LED_RED_PIN_2, OUTPUT);

  timer = millis();

  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);
  pinMode(LDR_CALCADA_PIN, INPUT);
  pinMode(LDR_RUA_PIN, INPUT);
}

void loop() {  
  readDistance();
  readLight();
  delay(100);

  if (!ubidots.connected()) {
    ubidots.reconnect();
  }

  controlTrafficLight();

  if (millis() - timer >= PUBLISH_FREQUENCY) {
    publishData();
    timer = millis();
  }

  ubidots.loop();
}

```
 
## 6. Código-Fonte e Lógica de Funcionamento

&nbsp;&nbsp;&nbsp;&nbsp;O código desenvolvido segue uma lógica de operação clara, com funções dedicadas para:

1. Leitura de sensores (luminosidade e distância).
2. Controle dos semáforos com base nas condições configuradas.
3. Comunicação com a plataforma Ubidots para envio de dados.

- Destaques do código:

1. Modo noturno:
- Função ```setTrafficLightYellowBlinking``` para operação intermitente.

2. Prioridade de tráfego:
- Condições implementadas na função ```controlTrafficLight```, priorizando ruas estreitas ou pedestres conforme necessário.

3. Publicação no Ubidots:
- Função ```publishData``` que organiza e envia os dados coletados para a central de controle.

## 7. Vídeo do semáforo em funcionamento

&nbsp;&nbsp;&nbsp;&nbsp;O vídeo a seguir demonstra o funcionamento do semáforo inteligente, ilustrando as principais funcionalidades do sistema:

**1. Sinal verde para a avenida e vermelho para a rua:** No início, o semáforo da avenida está com o sinal verde, enquanto o da rua está com o sinal vermelho, priorizando o tráfego da via principal.

**2. Demonstração de detecção de veículo:** Ao colocar o dedo no sensor LDR, simulando a presença de um veículo na rua, o sistema inverte os sinais: o semáforo da avenida vai para o vermelho e o da rua para o verde, permitindo o fluxo na rua menor.

**3. Modo noturno:** O vídeo então demonstra o modo noturno. Ao colocar o dedo no segundo LDR, simulando a detecção de baixa luminosidade (noite), o sistema ativa o sinal amarelo piscante em ambos os semáforos, alertando os motoristas para maior atenção.

**4. Integração com a plataforma Ubidots:** Por fim, o vídeo exibe a interface da plataforma Ubidots, onde os dados dos sensores estão sendo enviados e monitorados em tempo real.


https://github.com/user-attachments/assets/512854a5-14a3-4e67-a08a-d513ebea79b2


Caso o vídeo não rode, você pode acessar clicando [aqui](https://drive.google.com/file/d/1fUYqFsOQIWyczJHH6Nx0I-bn7eCLLSmF/view?usp=sharing) e em seguide baixe o vídeo para ser possível assistir.

## 8. Conclusão

&nbsp;&nbsp;&nbsp;&nbsp;Em conclusão, O sistema desenvolvido utilizou uma estratégia que prioriza o tráfego na avenida principal, mantendo o sinal verde ativo por padrão. Essa configuração só é alterada quando os sensores detectam a presença de veículos na rua menor, garantindo fluidez no trânsito sem comprometer a eficiência das vias secundárias.

&nbsp;&nbsp;&nbsp;&nbsp;Além disso, o modo noturno foi projetado para aumentar a segurança ao configurar ambos os semáforos no padrão de luz amarela piscante, alertando os motoristas em horários de baixa visibilidade. Essa abordagem simples e eficaz equilibra mobilidade e segurança, destacando a viabilidade do sistema em cenários urbanos.

