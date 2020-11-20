# SmartIrrigate
Projeto da disciplina Objetos Inteligentes Conectados

Dhouglas Nogueira Saraiva e
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
Métodos que conectam ao MQTT e WiFi:
```
//Criando as váriaveis globais
int umidade = 1024;
char * msg = "";

// Função que se conecta ao MQTT
void ReconectMQTT(void) {
  if (!MQTT.connected()){
    while (!MQTT.connected()) {
      Serial.print("* Conectando-se ao MQTT: ");
      Serial.println(BROKER);
      if (MQTT.connect(ID_MQTT)) {
        Serial.println("Conectado com sucesso");
      } else {
        Serial1.println("Falha ao se conectar.");
        Serial.println("Reiniciando em 2s");
        delay(2000);
      }
    }
  }
}

// Iniciando o MQTT
void StartMQTT(void) {
  MQTT.setServer(BROKER, PORTA);
}

// Função que reconecta o serviço de WiFi
void ReconectWIFI(void) {
  if (WiFi.status() == WL_CONNECTED)
    return;
  WiFi.begin(SSID, PASSWORD);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.print("Conectado na rede ");
}

void ConsultaConexoes(void) {
  ReconectWIFI(); 
  ReconectMQTT(); 
}
```
Setup do projeto:
```
void setup() {

  Serial.begin(115200);

  pinMode(sensor_umidade, INPUT);
  pinMode(rele, OUTPUT);

  StartMQTT();
}

```
Fluxo principal do projeto:
```
void loop() {
  ConsultaConexoes();
  umidade = analogRead(sensor_umidade);
  sprintf(msg, "%d", umidade);
  
  
  Serial.println("Valor da umidade");
  Serial.println(umidade);

  MQTT.publish(TOPICO_PUBLISH_UMIDADE, msg);
  MQTT.loop();
  delay(10000);
  
  // Caso a umidade seja baixa
  if (umidade>=0 && umidade<400 ) {
    Serial.println("UMIDADE BAIXA");
    digitalWrite(rele, HIGH);
    delay (2000);
    digitalWrite(rele, LOW);
  }

  else if (umidade>=400 && umidade<1025 ) {
    Serial.println("UMIDADE BOA");
    digitalWrite(rele, HIGH);
    delay (2000);
    digitalWrite(rele, LOW);
  }
  
  delay(10000);
}

```
# Comunicação MQTT
Na parte de comunicação com o MQTT, utilizamos o broker test.mosquitto.org apontando para a porta 1883. Optamos por um broker público por conta de ser simples a execução dos testes. Na implementação do sistema criamos o seguinte tópico: 

- topico_sensor_umidade: neste tópico, fazemos a inscrição no ESP8266 que busca e retorna o valor atual da umidade do solo, sendo mostrado na dashboard do broker.

