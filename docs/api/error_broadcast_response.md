# Resposta Broadcast de Erros

Toda vez que ocorrer um erro no terminal o SDK Android envia um broadcast com as informações, o APP Diagnóstico processa e envia para o backend. O acesso a esses dados será realizado pelo servico de mensagens kafka pelo Tópico **terminal-errors**. Os endereços de brokers pode variar entre os ambientes, solicitar url para setor de operações.

## Parâmetros

| Nome | Tipo | Obrigatório | Descrição | Tamanho máximo dos campos |
| --- | --- | --- | --- | --- |
| **`paymentsAppVersion`** | `String` | Sim | Versão do app suite de pagamentos. | 10 Caracteres |
| **`timestamp`** | `Date` | Sim | Data e hora do erro. |     |
| **`errorType`** | `ErrorType` | Sim | Tipo de erro (ErrorType). |     |
| **`serial`** | `string` | Sim | Número de serie do terminal. |     |
| **`errorCode`** | `String` | Não | Código de erro (prefixo + identificador). | 6 Caracteres |
| **`errorMessage`** | `String` | Não | Mensagem do erro. | 255 Caracteres |
| **`breadcrumbs`** | `String` | Não | Caminho de ações acionadas pelo usuário da tela inicial até o momento do erro. | 4096 Caracteres |
| **`subAcquirerId`** | `String` | Não | Código do facilitador Paystore ao qual o terminal está associado. | 32 Caracteres |
| **`merchantPaystore`** | `String` | Não | Lojista na PayStore. | 32 Caracteres |
| **`terminalID`** | `String` | Não | Número lógico do terminal. | 8 Caracteres |
| **`bCReturnCode`** | `String` | Não | Código de erro da BC. | 4 Caracteres |
| **`stackTrace`** | `String` | Não | Lista as chamadas de método que levam ao lançamento da exceção, junto com os nomes de arquivo e números de linha em que as chamadas ocorreram. | 255 Caracteres |
| **`bcCommands`** | `List<BCCommand>` | Não | Lista os Comandos da BC. |     |
| **`bin`** | `String` | Não | BIN do cartão. | 6 Caracteres |
| **`aid`** | `String` | Não | Identificador da Aplicação do Cartão. | 32 Caracteres |
| **`value`** | `BigDecimal` | Não | Valor da transação. |     |
| **`installments`** | `Integer` | Não | Quantidade de parcelas do pagamento. |     |
| **`captureType`** | `CaptureType` | Não | Forma de captura do cartão. |     |
| **`cardServiceCode`** | `String` | Não | Código de Serviço do Cartão. |     |
| **`productId`** | `Integer` | Não | Identificador do produto. |     |
| **`paymentType`** | `PaymentType` | Não | Tipo de pagamento (Débito, Crédito, Voucher, etc.) (vide [documentação da classe](../tipos_pagamentos/)). |     |
| **`ticketNumber`** | `String` | Não | Número do cupom gerado pelo terminal para a transação. | 4 Caracteres |
| **`batchNumber`** | `String` | Não | Número do lote. | 3 Caracteres |
| **`endpoint`** | `String` | Não | Último endpoint utilizado pelo terminal antes do erro ocorrer. | 2048 Caracteres |
| **`sdkMethod`** | `String` | Não | Método do SDK utilizado quando o erro ocorreu. | 255 Caracteres |
| **`applicationName`** | `String` | Não | Nome da aplicação que usou o SDK. | 255 Caracteres |
| **`httpStatusCode`** | `String` | Não | Código de erro HTTP. | 3 Caracteres |
| **`transactionUuid`** | `String` | Não | UUID da transação. | 36 Caracteres |
| **`devicdeName`** | `String` | Não | Modelo terminal. |     |
| **`lastBootDate`** | `String` | Não | Hora (timestamp) em que o terminal foi ligado. |     |
| **`batteryLevel`** | `String` | Não | Nível atual da bateria. |     |
| **`connectionType`** | `String` | Não | Tipo de conexão com a internet. |     |
| **`wifiLevel`** | `String` | Não | Intensidade do sinal Wi-fi. |     |
| **`isCharging`** | `boolean` | Não | Informa se o disponsitivo esta conectado e carregando. |     |
| **`osVersion`** | `String` | Não | Versão do sistema operacional. |     |
| **`kernelVersion`** | `String` | Não | Versão do kernel. |     |


## ErrorType

| Código | Nome | Descrição |
| --- | --- | --- |
| 1   | SCREEN_ERROR | Erros de tela |
| 2   | FATAL_EXCEPTION | FatalException |
| 3   | COMM\_SERVER\_ERROR | Falhas de comunicação ou servidor |


## BCCommandType

| Código | Nome | Descrição |
| --- | --- | --- |
| 1   | GET_CARD | Retorno do método getCard da BC |
| 2   | GO\_ON\_CHIP | Retorno do método getOnChip da BC |
| 3   | FINISH_CHIP | Retorno do método finishChip da BC |

## BCCommand
| Nome | Tipo | Obrigatório | Descrição | Tamanho Máximo dos campos |
| --- | --- | --- | --- | --- |
| **`type`** | `BCCommandType` | Não | Tipo de comando (getCard, goOnChip ou finishChip) da BC. |     |
| **`input`** | `String` | Não | Entrada do comando (getCard, goOnChip ou finishChip) da BC. | 2048 Caracteres |
| **`output`** | `String` | Não | Saída do comando (getCard, goOnChip ou finishChip) da BC. | -   |

[[Voltar APIs]](./README.md)
