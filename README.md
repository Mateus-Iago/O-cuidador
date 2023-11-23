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

## Contribuição
Contribuições são bem-vindas! Sinta-se à vontade para abrir issues relatando problemas ou sugerindo melhorias. Pull requests também são apreciados.

## Autor
[Mateus Iago]

