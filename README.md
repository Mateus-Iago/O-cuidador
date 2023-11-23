# O Cuidador, dispositivo visual e auditivo para controle de medicação para idosos

## Descrição do Problema
Muitos idosos que vivem sozinhos enfrentam dificuldades para tomar suas medicações nos horários corretos. Essa situação pode levar a problemas de saúde sérios devido a esquecimentos ou erros na administração dos medicamentos.

## Visão Geral da Solução
Este projeto propõe um sistema baseado na plataforma ESP32 para auxiliar os idosos na administração correta de suas medicações. O sistema é montado utilizando um buzzer e um display, com a intenção de integrá-lo na carcaça de um despertador. Funciona exibindo a hora e a data de forma semelhante a um relógio comum. Quando chega a hora de tomar um determinado remédio programado, o display muda para mostrar o nome do medicamento e o buzzer emite um som alto repetidamente para alertar o usuário. Além disso, as informações são enviadas para um servidor MQTT para registro e monitoramento.

## Funcionalidades Principais
- Exibição de hora e data no display estilo relógio.
- Programação de horários para a administração de medicamentos.
- Alerta sonoro com o buzzer quando chega a hora do medicamento.
- Registro das informações no servidor MQTT para acompanhamento remoto.

## Componentes Necessários
- ESP32
- Buzzer
- Display
- Servidor MQTT

## Instruções de Uso
1. Abra o link da simulação
2. Certifique se de que todas as bibliotecas estão devidamente instaladas (Time, PubSubClient e LiquidCrystal I2C)
3. Configure no código (o codigo está todo comentado para facilitar na configuração) a hora da medicação e o nome
4. inicialize a simulação 
5. Espere até a hora configurada

## Link para a simulação
https://wokwi.com/projects/382226065289090049

## Código Fonte

#include <WiFi.h>
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <PubSubClient.h>

LiquidCrystal_I2C LCD = LiquidCrystal_I2C(0x27, 16, 2);

#define NTP_SERVER     "pool.ntp.org"
#define UTC_OFFSET     0
#define UTC_OFFSET_DST 0

const char* ssid = "Wokwi-GUEST";
const char* password = "";
const char* mqtt_server = "46.17.108.113";
const int mqtt_port = 1883;
const char* firmware_id = "fiware_160";  // Identificador MQTT de firmware

const int buzzerPin = 26;  // Pino do buzzer

struct Medication {
  const char* name;
  int hour;
  int minute;
};

Medication medications[] = {
  {"Dipirona", 8, 0},    // Exemplo de medicamento 1 às 8:00
  {"Omeprazol", 23, 30},  // Exemplo de medicamento 2 às 22:30
  // Adicione mais medicamentos conforme necessário
};

WiFiClient espClient;
PubSubClient client(espClient);

void spinner() {
  static int8_t counter = 0;
  const char* glyphs = "\xa1\xa5\xdb";
  LCD.setCursor(15, 1);
  LCD.print(glyphs[counter++]);
  if (counter == strlen(glyphs)) {
    counter = 0;
  }
}

void printLocalTime() {
  struct tm timeinfo;

  // Tenta obter a hora do NTP
  if (!getLocalTime(&timeinfo)) {
    LCD.setCursor(0, 1);
    LCD.println("Connection Err");
    return;
  }

  LCD.setCursor(8, 0);
  LCD.println(&timeinfo, "%H:%M:%S");

  LCD.setCursor(0, 1);
  LCD.println(&timeinfo, "%d/%m/%Y   %Z");

  // Verifica se há medicamentos agendados para este horário
  for (const Medication &med : medications) {
    if (med.hour == timeinfo.tm_hour && med.minute == timeinfo.tm_min) {
      // Exibe o nome do medicamento no LCD
      LCD.clear();
      LCD.setCursor(0, 0);
      LCD.print("Tomar ");
      LCD.print(med.name);

      // Ativa o buzzer
      tone(buzzerPin, 1000, 1000);  // Frequência e duração do som

      // Publica a mensagem MQTT no tópico "medicine"
      if (client.connect(firmware_id)) {
        String topic = "medicine";
        String payload = med.name;
        client.publish(topic.c_str(), payload.c_str());
        client.disconnect();
      }

      // Publica a mensagem MQTT no tópico "MyMQTT" com o ID_MQTT e o conteúdo do LCD
      if (client.connect(firmware_id)) {
        String topic = "MyMQTT/" + String(firmware_id);
        String payload = "Tomar " + String(med.name);
        
        client.publish(topic.c_str(), payload.c_str());
        client.disconnect();
      }

      // Limpa o LCD
      LCD.clear();
    }
  }
}

void setup() {
  Serial.begin(115200);

  LCD.init();
  LCD.backlight();
  LCD.setCursor(0, 0);
  LCD.print("Connecting to ");
  LCD.setCursor(0, 1);
  LCD.print("WiFi ");

  WiFi.begin(ssid, password, 6);
  while (WiFi.status() != WL_CONNECTED) {
    delay(250);
    spinner();
  }

  Serial.println("");
  Serial.println("WiFi connected");
  Serial.print("IP address: ");
  Serial.println(WiFi.localIP());

  LCD.clear();
  LCD.setCursor(0, 0);
  LCD.println("Online");
  LCD.setCursor(0, 1);
  LCD.println("Updating time...");

  configTime(UTC_OFFSET, UTC_OFFSET_DST, NTP_SERVER);

  // Configura a conexão MQTT
  client.setServer(mqtt_server, mqtt_port);
}

void loop() {
  // Conecta-se ao servidor MQTT
  if (!client.connected()) {
    if (client.connect(firmware_id)) {
      Serial.println("Connected to MQTT server");
    } else {
      Serial.println("Failed to connect to MQTT server");
    }
  }

  // Verifica a conexão MQTT a cada 5 segundos
  client.loop();

  // Atualiza a hora e verifica se há medicamentos a serem tomados
  printLocalTime();
  delay(250);
}

## Contribuição
Contribuições são bem-vindas! Sinta-se à vontade para abrir issues relatando problemas ou sugerindo melhorias. Pull requests também são apreciados.

## Autor
[Mateus Iago]

