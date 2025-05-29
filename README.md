### Descrição do problema

As enchentes são tragédias anunciadas. Quando a água invade ruas, casas e vidas, ela expõe não apenas a força implacável da natureza, mas também, as falhas humanas: o desmatamento, a impermeabilização do solo, falta de planejamento urbano e por último e um dos mais importantes o lixo descartado incorretamente. Cada enchente não destrói apenas estruturas, mas sonhos, memórias e futuros. É preciso entender que enquanto nós como sociedade continuarmos ignorando problemas como esse, acidentes como esse que muitas vezes acabando sendo fatais vão continuar acontecendo, e como sempre, quem mais sofre com esses acontecimentos são os mais vulneráveis.

### Nossa solução

Visto que esta problemática ainda permanece com extrema frequência, eu e minha equipe desenvolvemos um sistema inovador que realiza a medição em tempo real do nível de água de rios e bueiros. Esses dados são atualizados automaticamente em uma plataforma web acessível a toda a população, permitindo que civis possam consultar, especialmente em dias chuvosos ou de risco, a situação hídrica em pontos próximos de sua região(situação dos rios e bueiros).

Além disso, todas as informações coletadas são enviadas diretamente para os órgãos públicos municipais e estaduais, oferecendo suporte técnico para que possam tomar as medidas necessárias, como a limpeza preventiva de bueiros ou a formulação de novas estratégias para melhorar o escoamento dos rios.

Nosso objetivo é unir tecnologia, cidadania e gestão pública para minimizar os impactos das enchentes e proteger vidas.

### Integrantes
Alexandre Lucas  RM: 561732
Vitor Carvalho   RM: 562298
Murilo Pina      RM: 563397

### Simulação no Wokwi

Você pode acessar a simulação do projeto através do seguinte link:  
[👉 Acesse aqui a simulação no Wokwi](https://wokwi.com/projects/432203265631815681)

### Descrição instrucional

Basicamente o nosso projeto consiste em um sensor ultrassônico que mede a distância da água que está no bueiro até a tampa dele, no caso do rio, mede a distância da água do rio até o ponto  definido. Com base nos dados coletados, Ele exibe uma mensagem no LCD L2C, dizendo se está com risco de enchente, se o nível do bueiro/rio está médio ou se ele está seguro. Dependo das informações ele acendo o led verde, amarelo ou vermelho, juntamente a isso também pode vir o disparo da buzina ou um leve "BIP".

### Vídeo da simulação

[![Assista ao vídeo no YouTube](https://img.youtube.com/vi/LH-CQEw5kNE/0.jpg)](https://www.youtube.com/watch?v=LH-CQEw5kNE)




### CÓDIGO DO PROJETO

#include <Wire.h>
#include <LiquidCrystal_I2C.h>

// Configuração do LCD I2C
LiquidCrystal_I2C lcd(0x27, 16, 2);

// Pinos do sensor ultrassônico HC-SR04
const int TRIG_PIN = 9; // Pino Trigger do ultrassônico
const int ECHO_PIN = 8; // Pino Echo do ultrassônico

// Pino do Buzzer
const int BUZZER_PIN = A0; 

// Pinos dos LEDs
const int GREEN_LED_PIN = 12;
const int YELLOW_LED_PIN = 11;
const int RED_LED_PIN = 10; 

// Variáveis para o cálculo da distância
long duration; // Armazena a duração do pulso ultrassônico
float distance; // Armazena a distância calculada em cm

void setup() {
  // Inicializa o LCD I2C
  lcd.init(); 
  lcd.backlight(); 

  Serial.begin(9600);

  // Define os pinos do sensor ultrassônico
  pinMode(TRIG_PIN, OUTPUT); // TRIG_PIN envia o pulso
  pinMode(ECHO_PIN, INPUT);  // ECHO_PIN recebe o pulso refletido
  pinMode(BUZZER_PIN, OUTPUT);
  pinMode(GREEN_LED_PIN, OUTPUT);
  pinMode(YELLOW_LED_PIN, OUTPUT);
  pinMode(RED_LED_PIN, OUTPUT);

  // Garante que todos os LEDs e o buzzer estejam desligados ao iniciar
  digitalWrite(GREEN_LED_PIN, LOW);
  digitalWrite(YELLOW_LED_PIN, LOW);
  digitalWrite(RED_LED_PIN, LOW);
  digitalWrite(BUZZER_PIN, LOW); 

  // Mensagem inicial no LCD
  lcd.setCursor(0,0); // Define o cursor na coluna 0, linha 0
  lcd.print("Monitor de");
  lcd.setCursor(0,1);
  lcd.print("Nivel de Agua");
  delay(2000); 
  lcd.clear();
}

void loop() {
  // Limpa o TRIG_PIN para garantir um pulso limpo
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);

  // Envia um pulso HIGH de 10 microssegundos no TRIG_PIN
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  // Mede a duração do pulso no ECHO_PIN (tempo que o som leva para ir e voltar)
  duration = pulseIn(ECHO_PIN, HIGH);

  // Calcula a distância em centímetros
  // A velocidade do som no ar é de aproximadamente 0.034 cm/microsegundo
  // Dividimos por 2 porque a duração inclui a ida e a volta do som
  distance = duration * 0.034 / 2;

  Serial.print("Distancia: ");
  Serial.print(distance);
  Serial.println(" cm");

  // Limpa o LCD e exibe a distância atual
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Dist: ");
  lcd.print(distance);
  lcd.print(" cm");

  lcd.setCursor(0,1); 

  if (distance >= 150) {
    lcd.print("Nivel Seguro");
    digitalWrite(BUZZER_PIN, LOW); 
    digitalWrite(GREEN_LED_PIN, HIGH);  
    digitalWrite(YELLOW_LED_PIN, LOW); 
    digitalWrite(RED_LED_PIN, LOW);     
  } else if (distance < 150 && distance >= 100) {
    // Atenção: Nível Médio
    lcd.print("Atencao: Medio");
    // Emite um bipe curto no buzzer
    digitalWrite(BUZZER_PIN, HIGH);
    delay(200);                         
    digitalWrite(BUZZER_PIN, LOW);
    digitalWrite(GREEN_LED_PIN, LOW);   
    digitalWrite(YELLOW_LED_PIN, HIGH); 
    digitalWrite(RED_LED_PIN, LOW);     
  } else { 
    lcd.print("Perigo! Enchente");
    digitalWrite(BUZZER_PIN, HIGH);     
    digitalWrite(GREEN_LED_PIN, LOW);   
    digitalWrite(YELLOW_LED_PIN, LOW);  
    digitalWrite(RED_LED_PIN, HIGH);    
  }

  delay(1000); // Espera 1 segundo antes da próxima medição
}

### CIRCUITO

![alt text](./circuito.png)




