### README

# Projeto Caronte: Boias de Análise, Monitoramento e Gestão Sustentável dos Oceanos

## Descrição do Projeto

O Projeto Caronte visa desenvolver uma solução tecnológica abrangente para mitigar os principais problemas enfrentados pelos oceanos, como a poluição marinha, a destruição de habitats marinhos e as mudanças climáticas. Utilizando uma rede global de boias equipadas com sensores, drones marítimos e câmeras, o sistema Caronte monitorará em tempo real diversos parâmetros ambientais, fornecerá dados essenciais para a formulação de políticas sustentáveis e ajudará na limpeza dos oceanos.

## Objetivos

1. Monitorar em tempo real a saúde dos oceanos.
2. Coletar dados para a formulação de políticas ambientais sustentáveis.
3. Ajudar na limpeza dos oceanos através de drones marítimos autônomos.
4. Promover a conscientização pública sobre a importância da preservação dos oceanos.

## Componentes do Sistema

1. **Boias de Monitoramento**: Equipadas com sensores para medir temperatura, pH, concentração de plásticos e outros produtos químicos.
2. **Drones Marítimos**: Autônomos e autossustentáveis, equipados com painéis solares para ajudar na limpeza dos oceanos e coleta de dados.
3. **Processamento de Dados**: Utilização de inteligência artificial para processar dados e fornecer insights.
4. **Energia Renovável**: Sistema de geração de energia a partir das ondas e painéis solares.

## Instruções de Uso

### Requisitos

- Placa Arduino (modelo compatível)
- Sensor de Temperatura e Umidade (DHT22)
- Servomotor
- Display LCD 16x2
- LEDs
- Resistores
- Fios de conexão
- Fonte de alimentação (bateria ou adaptador AC)
- Software Arduino IDE

### Dependências

- Biblioteca Servo.h
- Biblioteca LiquidCrystal.h
- Biblioteca DHT.h

### Montagem do Circuito

1. **Sensor DHT22**: Conectar o pino de dados ao pino digital 2 do Arduino.
2. **Servomotor**: Conectar ao pino digital 9.
3. **Display LCD**: Conectar os pinos RS, Enable, D4, D5, D6 e D7 aos pinos digitais 12, 11, 10, 5, 8 e 7, respectivamente.
4. **LEDs**: Conectar os LEDs aos pinos digitais 3, 4 e 6.
5. **Fonte de Alimentação**: Conectar a fonte de alimentação apropriada para o Arduino.

### Código Fonte

```cpp
#include <Servo.h>
#include <LiquidCrystal.h>
#include <DHT.h>

// Definindo os pinos dos sensores, do servomotor, do LCD e dos LEDs
const int pinDHT = 2;         // Pino de dados do sensor DHT22
const int pinServo = 9;       // Servomotor conectado ao pino digital 9
const int pinLcdRS = 12;      // Pino RS do LCD conectado ao pino digital 12
const int pinLcdEnable = 11;  // Pino Enable do LCD conectado ao pino digital 11
const int pinLcdD4 = 10;      // Pino D4 do LCD conectado ao pino digital 10
const int pinLcdD5 = 5;       // Pino D5 do LCD conectado ao pino digital 5
const int pinLcdD6 = 8;       // Pino D6 do LCD conectado ao pino digital 8
const int pinLcdD7 = 7;       // Pino D7 do LCD conectado ao pino digital 7
const int pinLedAcido = 3;    // Pino do LED indicando pH ácido
const int pinLedNeutro = 4;   // Pino do LED indicando pH neutro
const int pinLedBasico = 6;   // Pino do LED indicando pH básico

// Criando objetos dos sensores, do servomotor, do LCD e dos LEDs
Servo servoMotor;
LiquidCrystal lcd(pinLcdRS, pinLcdEnable, pinLcdD4, pinLcdD5, pinLcdD6, pinLcdD7);
DHT dht(pinDHT, DHT22); // Inicializando o sensor DHT22

// Definindo os limites de temperatura para a escala de mapeamento
const int TEMPERATURA_MIN = 0;   // Temperatura mínima (em graus Celsius)
const int TEMPERATURA_MAX = 100; // Temperatura máxima (em graus Celsius)

// Definindo os limiares de pH para acender os LEDs
const float PH_ACIDO = 6.0;   // Limiar de pH para indicar ácido
const float PH_NEUTRO = 7.0;  // Limiar de pH para indicar neutro
const float PH_BASICO = 8.0;  // Limiar de pH para indicar básico

float phManual = 7.0; // Valor inicial do pH
// Inicializando uma variável para armazenar o último valor de pH válido
float lastPhManual = phManual;

void setup() {
  // Inicializando o servo motor
  servoMotor.attach(pinServo);

  // Inicializando o LCD
  lcd.begin(16, 2); // Inicializando o LCD com 16 colunas e 2 linhas

  // Exibindo uma mensagem inicial no LCD
  lcd.print("Loading Caronte");
  delay(1000);
  lcd.clear();

  // Inicializando os LEDs
  pinMode(pinLedAcido, OUTPUT);
  pinMode(pinLedNeutro, OUTPUT);
  pinMode(pinLedBasico, OUTPUT);

  // Inicializando o sensor DHT22
  dht.begin();

  // Inicializando a comunicação serial
  Serial.begin(9600);
}

void loop() {
  // Lendo os valores do sensor DHT22
  float temperaturaCelsius = dht.readTemperature();

  // Exibindo os valores no monitor serial
  Serial.print("Temperatura: ");
  Serial.print(temperaturaCelsius);
  Serial.println(" °C");
  Serial.println(phManual); // Exibindo o valor do pH manualmente inserido

  // Exibindo os valores no LCD
  lcd.setCursor(0, 0); // Definindo o cursor para a primeira linha
  lcd.print("Temp: ");
  lcd.print(temperaturaCelsius);
  lcd.print(" C");

  lcd.setCursor(0, 1); // Definindo o cursor para a segunda linha
  lcd.print("pH: ");
  lcd.print(phManual); // Exibindo o valor do pH manualmente inserido

  // Controlando os LEDs com base no pH
  if (phManual < PH_ACIDO) {
    digitalWrite(pinLedAcido, HIGH);
    digitalWrite(pinLedNeutro, LOW);
    digitalWrite(pinLedBasico, LOW);
  } else if (phManual >= PH_ACIDO && phManual <= PH_NEUTRO) {
    digitalWrite(pinLedAcido, LOW);
    digitalWrite(pinLedNeutro, HIGH);
    digitalWrite(pinLedBasico, LOW);
  } else if (phManual >= PH_NEUTRO && phManual < PH_BASICO) {
    digitalWrite(pinLedAcido, LOW);
    digitalWrite(pinLedNeutro, LOW);
    digitalWrite(pinLedBasico, HIGH);
  } else {
    digitalWrite(pinLedAcido, LOW);
    digitalWrite(pinLedNeutro, LOW);
    digitalWrite(pinLedBasico, LOW);
  }

  // Verificando se a temperatura está acima de um limite (simulação de condições inadequadas)
  if (temperaturaCelsius > 25) {
    // Se a temperatura estiver acima do limite, acionar o servo motor (simulação de limpeza)
    servoMotor.write(90); // Movendo o servo motor para uma posição específica (90 graus)
    delay(1000); // Aguardando 1 segundo
    servoMotor.write(0); // Movendo o servo motor de volta para a posição inicial (0 graus)
    delay(1000); // Aguardando 1 segundo
  }

  // Verificando se há dados disponíveis para leitura no buffer serial
  if (Serial.available() > 0) {
    // Lendo o valor inserido pelo usuário no monitor serial
    float newPh = Serial.parseFloat();
    // Verificando se o novo valor de pH é válido
    if (!isnan(newPh)) {
      // Atribuindo o novo valor de pH apenas se for válido
      phManual = newPh;
      // Armazenando o último valor de pH válido
      lastPhManual = phManual;
    }
  } else {
    // Se não houver novos dados no buffer serial, atribuir o último valor de pH válido
    phManual = lastPhManual;
  }

  // Aguardando um intervalo de tempo antes de realizar a próxima leitura
  delay(3000); // Aguardando 3000 milissegundos (aproximadamente 3 segundos)
}
```

## Resultados Esperados e Impacto

### Resultados Esperados

1. **Monitoramento Eficiente**: Dados em tempo real sobre as condições dos oceanos.
2. **Limpeza Automática**: Drones marítimos autônomos para coleta de resíduos plásticos e outros detritos
