# SmartIrrigate
Projeto da disciplina Objetos Inteligentes Conectados

Dhouglas Nogueira Saraiva 
Guilherme Heck Lara

Este projeto “SmartIrrigate” tem como objetivo a função de medir a umidade do solo e irrigar as plantas de maneira automatizada, utilizando um módulo NodeMCU ESP8266 juntamente com uma Sensor de Higrômetro que mede a umidade do solo, acionando quando necessário uma Bomba de Água através de um Relé que irá alimentar com uma mangueira o solo com água. Será controlado via protocolo MQTT, podendo acessar uma dashboard e verificar qual é o valor da umidade de solo no momento e irrigando se necessário.

# Hardware 
 - Módulo Wifi NodeMCU ESP8266
 - Sensor de Umidade do Solo Higrômetro
 - Protoboard 400 Pontos
 - Kit de Jumpers
 - Módulo Relé 5V 1 Canal
 - Mini Bomba de água submersível vertical
 - Mangueira Cristal
 - Cabos USB
 
# Código
Para o funcionamento adequado é necessário importar as bibliotecas:
```
// Importando as bibliotecas necessárias
# include <ESP8266WiFi.h>
# include <PubSubClient.h>
```
Definição de configurações do Broker, Tópico MQTT e pinos utilizados:
```
// Define configs do MQTT
# define ID_MQTT "smart_irrigate_mqtt"
# define TOPICO_PUBLISH_UMIDADE "topico_sensor_umidade"

// Endereço e a porta do broker
const char * BROKER = "test.mosquitto.org";
int PORTA = 1883;

// Definindo os pinos dos dispositivos
# define rele 2
# define sensor_umidade 17
```
Nas variáveis abaixo você deve colocar o Nome e Senha do seu WiFi:
```
// Informações da rede WiFi
char * SSID = "";
char * PASSWORD = "";

WiFiClient espClient;
PubSubClient MQTT(espClient);

```
