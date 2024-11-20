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

## 1. Especificações Técnicas e Componentes

Os materiais utilizados no desenvolvimento do projeto foram:

**Microcontroladores:**

- 2 ESP32

**Atuadores:**

- 2 LEDs vermelhos
- 2 LEDs amarelos
- 2 LEDs verdes

**Sensores:**

- 2 Sensores de luminosidade (LDR)
- 1 Sensor ultrassônico (HCSR04)

**Conexões e Resistores:**

- Jumpers
- 8 Resistores

## 2. Configuração e Programação do Semáforo com Sensores

&nbsp;&nbsp;&nbsp;&nbsp;O sistema é controlado por uma lógica programada para gerenciar as condições de operação. As principais condições implementadas são:

1. Intertravamento entre semáforos:

- Um semáforo nunca estará verde ao mesmo tempo que o outro.

2. Operação baseada em luz ambiente (LDR):

- Um LDR determina se é dia ou noite. Se estiver escuro, ambos os semáforos entram no modo noturno, piscando amarelo intermitentemente.
Prioridade para ruas estreitas:

- O segundo LDR, posicionado na rua estreita, detecta veículos parados.
- Caso detecte um veículo, o semáforo da avenida será fechado e o da rua estreita será aberto.

3. Abertura da avenida:

- O semáforo da avenida só abre quando o LDR na rua estreita não detecta mais veículos.

4. Controle de pedestres:

- O sensor ultrassônico detecta pedestres na faixa da avenida (distância < 4,5 cm).
- Quando isso ocorre, o semáforo da avenida é fechado e o da rua estreita é aberto.

## 3. Código do Projeto

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
 
## 4. Código-Fonte e Lógica de Funcionamento

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