# Integração com Pagamentos V2 


ma das formas de se integrar com a aplicação de pagamentos da DiscoverPay é via [**IPC**]. Para isto, é fornecida uma biblioteca, a  [**payments-api-x.x.x.x.aar**](https://github.com/Discover-Pay/payments-api-demo-android/tree/main/app/aars), contendo todo o código necessário a ser usado para tais chamadas.

Usando esta API, é possível realizar todo o fluxo de pagamento, ou seja, a confirmação (ou o desfazimento) e o estorno. O pagamento pode ser realizado com um valor pré-definido ou com um valor em aberto, a ser digitado pelo operador do terminal. Além disso, pode-se informar uma lista de tipos de pagamentos (débito, crédito à vista, crédito parcelado, etc.) permitidos.

Ainda que esta integração se dê através de uma API, a aplicação de pagamentos da DiscoverPay pode exibir informações na interface do terminal, tais como mensagens (e.g., "Insira ou passe o cartão"), ou mesmo solicitar informações do operador (e.g., CVV). Assim sendo, durante a realização de qualquer operação, a aplicação que solicitou a operação não deve interagir com a interface do dispositivo até que a operação seja concluída.

A seguir, temos a especificação detalhada das operações disponíveis.

Para integração com a API de pagam


![Default](https://github.com/Discover-Pay/payments-api-docs-android/blob/main/docs/api/assets/fluxo_pagamento.png)

Passos | Sucesso | Erro
------------ | ------------- | -------------
**1.Solicitação de pagamento** | O pagamento foi realizado e seu status é Pendente | O pagamento não foi realizado. A resposta contém informações do erro. | [optional] 
**2.Resposta solicitação de pagamento** | A resposta contém informações do pagamento realizado. | A resposta contém informações do erro da solicitação. | [optional] 
**3.Solicitação de confirmação** | Confirmação realizada, seu status é Confirmada, não pode ser desfeita.| Confirmação não realizada. A resposta contém informações do erro. | [optional] 
**4.Resposta da confirmação** | A resposta contém informações da confirmação realizada. Impressão do Comprovante | A resposta contém informações do erro da solicitação. Nesse ponto pode ser enviado o desfazimento. | [optional] 
**5.Solicitação de desfazimento** | Desfazimento realizado, seu status é Desfeita. Não pode ser confirmada. | A resposta contém informações do erro da solicitação. O status continua Pendente. | [optional] 
**6.Resposta do desfazimento** | A resposta contém informações do desfazimento realizado.** | A resposta contém informações do erro da solicitação. | [optional] 

O pagamento só é finalizado quando existe uma confirmação ou um cancelamento (desfazimento). Em caso de confirmação, o comprovante estará disponível para impressão ou envio por SMS/e-mail, dependendo das funcionalidades do terminal


## Métodos
Assinatura | Descrição
------------------------------------------------------------------------ | ------------------------------------------------------------------------------
**void startPaymentV2(PaymentRequestV2 paymentRequest, PaymentCallback paymentCallback)** | Realiza o processo de autorização de pagamento. | [optional] 
**void confirmPayment(String paymentId, PaymentCallback paymentCallback)** | Confirma uma autorização de pagamento realizada anteriormente. | [optional] 
**void cancelPayment(String paymentId, PaymentCallback paymentCallback)** | Desfaz uma autorização de pagamento realizada anteriormente. | [optional] 

## startPaymentV2()
Este método deve ser chamado quando se deseja fazer uma solicitação de autorização de pagamento. Durante sua execução, os dados do pagamento serão validados, informações adicionais serão solicitadas ao operador (e.g. senha e CVV), e a autorização junto à adquirente será feita.

# Parâmetros
Nome | Tipo | Obrigatório | Descrição 
-----|------|-------------|----------|
**request** |PaymentRequestV2|Sim|Objeto de transferência de dados que conterá as informações da requisição do pagamento. Note que nem todos os parâmetros são obrigatórios.
**callback** |PaymentCallback|Sim|Interface que será executada para notificações de sucesso ou erro do processo de pagamento.

### Detalhe dos Parâmetros
#### request (PaymentRequestV2)

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| `value` | `BigDecimal` | Não | Valor do pagamento solicitado. Caso não seja preenchido (null), a interface solicitará o valor ao operador. |
| `additionalValueType` | `AdditionalValueType` | Não | Tipo de valor adicional (Cashback, TIP, etc.). Se não estiver preenchido (nulo), deve-se ignorar o campo "additionalValue". ‘AdditionalValueType’ deve admitir, para FastTrack, apenas o valor CASHBACK. Depois de ler o cartão, se o AdditionalValueType informado não for compatível com produto banner do Cartão, o terminal exibe um erro na tela e finaliza a transação. Para evitar que esse erro ocorra, é recomendado usar este campo apenas junto com um "productShortName", que deve ser preenchido cmo um produto que suporta o uso do tipo de valor adicional em questão. Para pagamentos de QRCode estático não considerar este parâmetro quando `operationMethodAllowed` = 1 |
| `additionalValue` | `BigDecimal` | Não | Valor adicionado ao valor da transação. Se "additionalValueType" for relatado e "additionalValue" não foi preenchido (nulo) ou é igual a 0 (zero), a interface irá pedir ao operador o valor adicional. Se "additionalValueType" for preenchido e "additionalValue" tiver um valor mais alto do que 0 (zero), a interface não pedirá ao operador o valor adicional. Para pagamentos de QRCode estático não considerar este parâmetro quando `operationMethodAllowed` = 1 |
| `paymentTypes` | `List<PaymentType>` | Não | Tipos de pagamentos (Débito, Crédito, Voucher, etc.) permitidos para este pagamento. Caso seja vazio, ou seja, null, significa que todos os tipos são permitidos. Caso contenha apenas um, este tipo será o utilizado (se possível) e não será perguntado nada ao operador. Para pagamentos de QRCode estático não considerar este parâmetro quando `operationMethodAllowed` = 1 |
| `installments` | `Integer` | Não | Quantidade de parcelas. Usado apenas para tipos de pagamentos que suportem parcelamento e neste caso é obrigatório. Valor deve ser entre 2 e 99. |
| `accountTypeId` | `String` | Não | Tipo de conta. Se não for preenchido (nulo), a interface pode perguntar ao operador o tipo de conta, dependendo da configuração do produto principal associado ao cartão usado na transação. Depois de ler o cartão, se o accountTypeId inserido não existir na cnofiguração do produto de bandeira do Cartão, o terminal exibe um erro na tela e finaliza a transação. Para evitar que esse erro ocorra, é recomendado usar este campo apenas junto com um "productShortName", onde deve constar um produto que suporta o uso do tipo de conta. Para pagamentos de QRCode estático não considerar este parâmetro quando `operationMethodAllowed` = 1 |
| `planId` | `String` | Não | Identificação do plano de pagamento. Pode ter um ou dois caracteres, a depender da regra da adquirente selecionada. Se não for preenchido (nulo), a interface pode solicitar o plano para a operadora, de acordo com a configuração do produto de bandeira associado ao cartão usado na transação. Depois de ler o cartão, se o planId relatado não for compatível com o número de parcelas (capturado no terminal ou informado no parâmetro "Parcelas") e com a configuração do produto de bandeira do cartão (observando as configurações planCondition, planType e planList), o terminal mostra um erro no tela e finaliza a transação. A fim de evitar que esse erro ocorra, é recomendado usar este campo apenas junto com um "productShortName", onde deve constar um produto que apóia o plano referido. Para pagamentos de QRCode estático não considerar este parâmetro quando `operationMethodAllowed` = 1 |
| `appTransactionId` | `String` | Sim | Identificador da transação integrada para o software. O Identificador referido é aquele utilizado na aplicação que originou a solicitação de pagamento. Não deve se repetir. |
| `ApplicationInfo.credentials` | `Credentials` | Sim | Credenciais da aplicação que está solicitando a operação, conforme cadastro na PayStore. Basicamente, trata-se da identificação da aplicação e o token de acesso. |
| `ApplicationInfo.softwareVersion` | `String` | Sim | Versão da aplicação que está solicitando o pagamento. |
| `showReceiptView` (DEPRECATED) | `Boolean` | Não | A Solução irá utilizar o valor dos parâmetros `printMerchantReceipt` e `printCustomerReceipt` para executar a impressão depois que a [`confirmação`](#confirmpayment) for executada. |
| `printMerchantReceipt` | `Boolean` | Não | Indica se o comprovante do estabelecimento deve ser impresso ou não depois da [`confirmação`](#confirmpayment) da transação. O valor padrão é _true_, isto é, o comprovante é impresso. |
| `printCustomerReceipt` | `Boolean` | Não | Indica se o comprovante do cliente deve ser impresso ou não depois da [`confirmação`](#confirmpayment) da transação. O valor padrão é _true_, isto é, o comprovante é impresso. |
| `tokenizeCard` | `Boolean` | Não | Indica se deve ser feita ou não a tokenização do cartão após a aprovação do pagamento ou não. O valor padrão é false, isto é, não será feita a tokenização. |
| `tokenizeEmail` | `String` | Se tokenizeCard for true, sim, caso contrário, não. | E-mail do portador do cartão. Se “tokenizeCard” for false, este parâmetro é ignorado. |
| `tokenizeNationalDocument` | `String` | Não | CPF ou CNPJ do portador do cartão. Se “tokenizeCard” for false, este parâmetro é ignorado. Se for true, mas não for informado esse parâmetro, então a chamada à API de criação de token no e-commerce também não o utilizará. |
| `productShortName` | `String` | Não | Identificação resumida do produto de bandeira do cartão. Depois de ler o cartão e identificar o produto de bandeira, se ele não corresponder ao productShortName referido, o terminal exibe um erro na tela e finaliza a transação. Para pagamentos de QRCode estático não considerar este parâmetro quando `operationMethodAllowed` = 1, o terminal exibe um erro caso o campo seja informado e este valor corresponda a um produto cujo campo `acquirerParams.allowQRCode` seja false; Ou o campo `acquirerParams.qrPaymentMethodId` não esteja configurado; Ou se o produto existir, mas não estiver habilitado no terminal. |
| `note` | `String` | Não | Texto adicional que é inserido como Nota. (pode ser o número da fatura) |
| `dni` | `String` | Não | Número do Documento. Para pagamentos de QRCode estático não considerar este parâmetro quando `operationMethodAllowed` = 1 |
| `operationMethodAllowed` | `Integer` | Sim | Indica o método de operação de pagamento, anulação e devolução. Admita os seguintes valores: 0 - Apenas com cartão físico (lido ou digitado); 1 - Somente com QRCode. |
| `allowBenefit` (OBSOLETO) | `Boolean` | Não | Indica se o QRCode deve ser gerado com as opções do produto associadas aos benefícios. O valor padrão é 'verdadeiro', ou seja, os benefícios serão adicionados. |

## callback (PaymentCallback)

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| **`onSuccess`** |     |     | Método para notificação em caso de sucesso |
| `Payment.value` | `BigDecimal` | Sim | Valor do pagamento. Este é o valor que foi aprovado pela adquirente. Deve ser validado sempre na resposta, ainda que tenha sido passado como parâmetro, pois há adquirentes que, para algumas situações, aprovam valores diferentes dos solicitados. |
| `Payment.additionalValueType` | `AdditionalValueType` | Não | Presente apenas quando existe um valor adicional no contexto da transação executada. |
| `Payment.additionalValue` | `BigDecimal` | Não | Presente apenas quando existe um valor adicional no contexto da transação executada. |
| `Payment.paymentType` | `PaymentType` | Sim | Tipo de pagamento (Débito, Crédito, Voucher, etc.). |
| `Payment.installments` | `Integer` | Não | Quantidade de parcelas do pagamento. |
| `Payment.accountTypeId` | `String` | Não | Presente apenas quando existe um tipo de conta no contexto da transação executada. |
| `Payment.planId` | `String` | Não | Presente apenas quando existe um plano no contexto da transação executada. |
| `Payment.productShortName` | `String` | Sim | Corresponde ao productShortName correspondente ao produto principal no contexto da transação. |
| `Payment.ticketNumber` | `String` | Não | ticketNumber gerado pelo terminal para a transação. |
| `Payment.batchNumber` | `String` | Sim | Número de lote. |
| `Payment.nsuTerminal` | `String` | Sim | NSU gerado pelo terminal para a transação. |
| `Payment.acquirer` | `String` | Sim | Adquirente que autorizou o pagamento. |
| `Payment.paymentId` | `String` | Sim | Identificador da transação para a aplicação de pagamentos. Esta é a informação a ser usada para a confirmação e desfazimento. |
| `Payment.brand` | `String` | Sim | Bandeira do cartão. |
| `Payment.bin` | `String` | Sim | BIN do cartão. |
| `Payment.panLast4Digits` | `String` | Sim | Últimos 4 dígitos do PAN do cartão. |
| `Payment.captureType` | `CaptureType` | Sim | Forma de captura do cartão. |
| `Payment.paymentStatus` | `PaymentStatus` | Sim | Situação do pagamento. No caso de solicitações retornadas com sucesso, esta informação sempre será _PENDING_, requerendo uma confirmação ou desfazimento para a sua conclusão definitiva. |
| `Payment.paymentDate` | `Date` | Sim | Data/hora do pagamento para a aplicação de pagamentos. |
| `Payment.acquirerId` | `String` | Sim | Identificador da transação para a adquirente. Este é o identificador que consta no arquivo que a adquirente fornece (EDI). Desta forma,é possível realizar a conciliação do pagamento com a transação integrada. |
| `Payment.acquirerResponseCode` | `String` | Sim | Código de resposta da adquirente. |
| `Payment.acquirerResponseDate` | `String` | Sim | Data/hora retornada pela adquirente. |
| `Payment.acquirerAdditionalMessage` | `String` | Não | Mensagem adicional enviada pela adquirente na resposta da transação |
| `Payment.acquirerAuthorizationNumber` | `String` | Sim | Número da autorização fornecido pela adquirente (consta no comprovante do cliente Portador do Cartão). |
| `Payment.Receipt.clientVia` | `String` | Não | Conteúdo do comprovante - via do cliente. |
| `Payment.Receipt.merchantVia` | `String` | Não | Conteúdo do comprovante - via do estabelecimento. |
| `Payment.cardToken` | `String` | Não | Token do cartão utilizado na transação. |
| `Payment.cardholderName` | `String` | Não | Nome do portador do cartão. |
| `Payment.terminalId` | `String` | Sim | Identificação do terminal. |
| `Payment.note` | `String` | Sim | Valor adicional que é inserido como Nota. (pode ser o número da fatura) Este campo só virá na resposta caso tenha sido preenchido na requisição do pagamento pela api; Caso seja capturado pelo comprovante, não é possível retornar, pois como o comprovante é exibido depois da confirmação, nessa altura a resposta do pagamento já tem sido enviada para o app. |
| `Payment.dni` | `String` | Sim | Número do Documento. Este campo só virá na resposta caso tenha sido preenchido na requisição do pagamento pela api; Caso seja capturado pelo comprovante, não é possível retornar, pois como o comprovante é exibido depois da confirmação, nessa altura a resposta do pagamento já tem sido enviada para o app. |
| `Payment.qrId` | `String` | Não | Identificador QrCode gerado pelo terminal de captura. |
| `Payment.originalValue` | `BigDecimal` | Não | Valor orginal da venda. Presente em pagamentos com QRCode, cujo beneficio foi aplicado ao valor da venda. |
| **`onError`** |     |     | Método para notificação em caso de erro. |
| `ErrorData.paymentsResponseCode` | `String` | Sim | Código de resposta para o erro ocorrido. Vide [Códigos de Resposta](./codigo_resposta.md) |
| `ErrorData.acquirerResponseCode` | `String` | Não | Código de resposta para o erro ocorrido retornado pela adquirente. Note que este erro só será retornado se a transação não for autorizada pela adquirente. |
| `ErrorData.responseMessage` | `String` | Sim | Mensagem descritiva da causa da não autorização. Caso a transação tenha sido negada pela adquirente, conterá a mensagem retornada pela adquirente. |
| `ErrorData.acquirerAdditionalMessage` | `String` | Não | Mensagem adicional enviada pela adquirente na resposta da transação. |


## confirmPayment()

Este método deve ser chamado para confirmar uma transação que o terminal conseguiu processar completamente a perna de autorização enviada pelo Autorizador.

Este método **não** deve ser chamado para uma transação já confirmada, ou seja, em que já se executou o método **confirmPayment()** anteriormente.

Este método **não** deve ser chamado para uma transação já desfeita, ou seja, em que já se executou o método **cancelPayment()** anteriormente.

Este método **não** deve ser chamado para uma transação que foi negada pelo Autorizador, ou seja, a transação precisa ter sido autorizada pelo Autorizador.

Após a execução desta confirmação, a transação só poderá ser cancelada através de uma operação de estorno (o estorno é a operação executada pelo menu CANCELAMENTO do terminal).

Caso o App consumidor desta API tenha finalizado o seu processo de negócio com êxito, porém não tenha chamado o método **confirmPayment()**, a transação permanecerá com o seguinte status: Situação PayStore = "Pendente". Resolução no Adquirente = "Pendente".

Como resultado, poderemos ter uma inconsistência transacional, visto que, na virada do dia, algumas redes adquirentes confirmam automaticamente as transações que não receberam a perna de confirmação. Outras redes adquirentes trabalham apenas com duas pernas, sem a necessidade de perna de confirmação. Neste caso, se houver algum problema na conclusão da transação no lado do terminal, é imperativo que a solução de captura execute o método **cancelPayment()**, a fim de desfazer a transação no adquirente e evitar cobrança para o cliente Portador do Cartão.

## Parâmetros

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| `paymentId` | `String` | Sim | Identificador da transação que será confirmada. O Identificador referido é aquele utilizado na aplicação de pagamentos. |
| `callback` | `PaymentCallback` | Sim | Interface que será executada para notificações de sucesso ou erro. |

## Detalhe dos parâmetros
#### callback

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| **`onSuccess`** |     |     | Método para notificação em caso de sucesso |
| **`onError`** |     |     | Método para notificação em caso de erro. |
| `ErrorData.paymentsResponseCode` | `String` | Sim | Código de resposta para o erro ocorrido. Vide [Códigos de Resposta](./codigo_resposta.md) |
| `ErrorData.acquirerResponseCode` | `String` | Não | Código de resposta para o erro ocorrido retornado pela adquirente. Note que este erro só será retornado se a transação não for autorizada pela adquirente. |
| `ErrorData.responseMessage` | `String` | Sim | Mensagem descritiva da causa da não autorização. Caso a transação tenha sido negada pela adquirente, conterá a mensagem retornada pela adquirente. |


## cancelPayment()

Este método deve ser sempre chamado para desfazer uma transação que o terminal não conseguiu processar completamente a perna de autorização enviada pelo Autorizador.

Este método não deve ser chamado para uma transação já confirmada, ou seja, em que já se executou o método **confirmPayment()** anteriormente.

Este método não deve ser chamado para desfazer uma transação já desfeita, ou seja, em que já se executou o método **cancelPayment()** anteriormente

Este método não deve ser chamado para uma transação que foi negada pelo Autorizador.

Este método não é um estorno. O estorno é a operação executada pelo menu CANCELAMENTO do terminal. O estorno é executado em transações que foram concluídas com êxito, ou seja, foram confirmadas.

Após a execução do desfazimento, **cancelPayment()**, a transação não poderá ser mais confirmada pela aplicação do terminal, ou seja, não se pode mais executar o método **confirmPayment()**.

Caso o App consumidor desta API não tenha finalizado o seu processo de negócio com êxito, é imprescindível a chamada do método **cancelPayment()**. A consequência de não cancelar uma transação que não teve seu processo de negócio concluído é semelhante à consequência de não confirmar. Porém, nesse caso, com um agravante, pois provavelmente o cliente não levará o produto/serviço associado à transação financeira, ou uma nova tentativa de venda poderá ser feita, resultando em uma cobrança em duplicidade para o cliente Portador do Cartão.

## Parâmetros

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| `paymentId` | `String` | Sim | Identificador da transação que será desfeita. O Identificador referido é aquele utilizado na aplicação de pagamentos. |
| `callback` | `PaymentCallback` | Sim | Interface que será executada para notificações de sucesso ou erro. |

## Detalhe dos parâmetros

#### callback

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| **`onSuccess`** |     |     | Método para notificação em caso de sucesso. |
| **`onError`** |     |     | Método para notificação em caso de erro. |
| `ErrorData.paymentsResponseCode` | `String` | Sim | Código de resposta para o erro ocorrido. Vide [Códigos de Resposta](./codigo_resposta.md) |
| `ErrorData.acquirerResponseCode` | `String` | Não | Código de resposta para o erro ocorrido retornado pela adquirente. Note que este erro só será retornado se a transação não for autorizada pela adquirente. |
| `ErrorData.responseMessage` | `String` | Sim | Mensagem descritiva da causa da não autorização. Caso a transação tenha sido negada pela adquirente, conterá a mensagem retornada pela adquirente. 

### Campo adicional api_pending

Além dos métodos para resolução de pagamentos (confirmPayment e cancelPayment), há a possibilidade de se configurar um **campo adicional** no Portal Paystore para realizar esta resolução automaticamente, em determinado tempo. Para isto, deve ser criado um campo adicional no Portal Paystore com o tipo JSON e a chave api_pending.

### Valor do campo

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| `confirmationTime` | `String` | Sim | Intervalo, em minutos, para resolução das pendências. |
| `transactionConfirmation` | `String` | Sim | Ação a ser tomada. Permite os valores `CONFIRM` (Confirma a transação) ou `UNDO` (Desfaz a transação). |


```java
{
    "confirmationTime": "10",
    "transactionConfirmation": "CONFIRM"
}

```
#### EXEMPLO DO FLUXO DE PAGAMENTO
```java
import androidx.appcompat.app.AppCompatActivity;

import android.content.Intent;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import java.math.BigDecimal;

import br.com.phoebus.android.payments.api.ApplicationInfo;
import br.com.phoebus.android.payments.api.Credentials;
import br.com.phoebus.android.payments.api.ErrorData;
import br.com.phoebus.android.payments.api.PaymentClient;
import br.com.phoebus.android.payments.api.PaymentRequestV2;
import br.com.phoebus.android.payments.api.PaymentV2;
import br.com.phoebus.android.payments.api.exception.ClientException;

public class MainActivity extends AppCompatActivity implements View.OnClickListener, PaymentClient.PaymentCallback<PaymentV2> {

    Button bt_start;
    private PaymentClient paymentClient;
    public static final String TEST_APPLICATION_ID = "0";
    public static final String TEST_SECRET_TOKEN = "000000000000000000000000";
    public static final String TAG = "TAG_DEMO";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        bt_start = (Button) this.findViewById(R.id.button);
        bt_start.setOnClickListener(this);
        paymentClient = new PaymentClient();

    }


    @Override
    public void onClick(View view) {
        doExecute();
    }

    @Override
    protected void onResume() {
        super.onResume();
        paymentClient.bind(this);
    }

    @Override
    protected void onDestroy() {
        try {
            paymentClient.unbind(this);
        } catch (Exception e) {
            Log.e(TAG, e.getMessage());
        }
        super.onDestroy();
    }

    public void doExecute(){
        PaymentRequestV2 request = new PaymentRequestV2();
        request.setValue(new BigDecimal(50));
        request.setAppTransactionId("123456");

        Credentials credentials = new Credentials();
        credentials.setApplicationId(TEST_APPLICATION_ID);
        credentials.setSecretToken(TEST_SECRET_TOKEN);

        ApplicationInfo applicationInfo = new ApplicationInfo();
        applicationInfo.setCredentials(credentials);
        applicationInfo.setSoftwareVersion("1.0");

        request.setAppInfo(applicationInfo);

        try {
            paymentClient.startPaymentV2(request, this);
        } catch (ClientException e) {
            Log.e(TAG, "Error starting payment", e);
        }
    }


    @Override
    public void onSuccess(PaymentV2 paymentV2) {
        Log.i(TAG, paymentV2.toString());

        doConfirmPayment(paymentV2);
        /*
          Se, na sua regra de negócio, for preciso desfazer a transação por algum motivo,
          chame o método doCancelPayment(paymentV2)
        **/
    }

    @Override
    public void onError(ErrorData errorData) {
        Log.e(TAG, errorData.getResponseMessage());
        Toast.makeText(this, errorData.getResponseMessage(), Toast.LENGTH_LONG).show();
    }

    private void doConfirmPayment(PaymentV2 paymentV2) {
        try {
            paymentClient.confirmPayment(paymentV2.getPaymentId(),
                    new PaymentClient.PaymentCallback<PaymentV2>() {

                        @Override
                        public void onError(ErrorData errorData) {
                            Log.e(TAG, errorData.getResponseMessage());
                            Toast.makeText(MainActivity.this, errorData.getResponseMessage(), Toast.LENGTH_LONG).show();

                        }

                        @Override
                        public void onSuccess(PaymentV2 payment) {
                            Log.i(TAG, payment.toString());
                        }


                    });
        } catch (ClientException e) {
            Log.e(TAG, "Error confirmPayment", e);
        }

    }

    private void doCancelPayment(PaymentV2 paymentV2) {
        try {
            paymentClient.cancelPayment(paymentV2.getPaymentId(),
                    new PaymentClient.PaymentCallback<PaymentV2>() {

                        @Override
                        public void onError(ErrorData errorData) {
                            Log.e(TAG, errorData.getResponseMessage());
                            Toast.makeText(MainActivity.this, errorData.getResponseMessage(), Toast.LENGTH_LONG).show();

                        }

                        @Override
                        public void onSuccess(PaymentV2 payment) {
                            Log.i(TAG, payment.toString());
                        }


                    });
        } catch (ClientException e) {
            Log.e(TAG, "Error cancelPayment", e);
        }

    }


}
```

[[Voltar]](./README.md)

