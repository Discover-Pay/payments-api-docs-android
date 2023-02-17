# Integração com Estorno

Uma das formas de se integrar com a aplicação de pagamentos  da DiscoverPay é via [**IPC**](https://developer.android.com/guide/components/aidl.html). Para isto, é fornecida uma biblioteca, a [**payments-api-x.x.x.x.aar**](https://github.com/Discover-Pay/payments-api-demo-android/tree/main/app/aars), contendo todo o código necessário a ser usado para tais chamadas.

Usando esta API, é possível realizar todo o fluxo de pagamento, ou seja, a confirmação (ou o desfazimento) e o estorno. O pagamento pode ser realizado com um valor pré-definido ou com um valor em aberto, a ser digitado pelo operador do terminal. Além disso, pode-se informar uma lista de tipos de pagamentos (débito, crédito à vista, crédito parcelado, etc.) permitidos.

Ainda que esta integração se dê através de uma API, a aplicação de pagamentos pode exibir informações na interface do terminal, tais como mensagens (e.g., "Insira ou passe o cartão"), ou mesmo solicitar informações do operador (e.g., CVV). Assim sendo, durante a realização de qualquer operação, a aplicação que solicitou a operação não deve interagir com a interface do dispositivo até que a operação seja concluída.

A seguir, temos a especificação detalhada das operações disponíveis.

Para integração com a API de pagamentos, é fornecida a interface **PaymentClient**.


## Fluxo de Estorno


![Default](https://github.com/Discover-Pay/payments-api-docs-android/blob/main/docs/api/assets/fluxo_estorno.png)

| Passos | Sucesso | Erro |
| --- | --- | --- |
| 1.Solicitação de estorno do pagamento | O estorno do pagamento foi realizado e seu status é `Estornado` | O estorno do pagamento não foi realizado. A resposta contém informações do erro. |
| 2.Resposta solicitação de estorno do pagamento | A resposta contém informações do estorno do pagamento realizado. | A resposta contém informações do erro da solicitação. |
| 3.Solicitação de desfazimento do estorno | Desfazimento realizado, seu status é `Desfeita`. | A resposta contém informações do erro da solicitação. |
| 4.Resposta do desfazimento do estorno | A resposta contém informações do desfazimento realizado. | A resposta contém informações do erro da solicitação. |


O estorno só é finalizado quando existe uma confirmação ou um desfazimento. Em caso de confirmação, o comprovante será impresso.

> :warning: O método PaymentClient.Bind(_callback) deve ser chamado, obrigatoriamente, antes de chamar qualquer método da Integração de Pagamento. O **bind é assíncrono**, ou seja, a próxima linha após o bind() será executada antes de receber a sua resposta, por isso garanta que, antes de chamar os métodos de integração, o bind esteja **conectado**.

## Métodos

| Assinatura | Descrição |
| --- | --- |
| [`void reversePaymentV2(ReversePaymentRequestV2 paymentRequest, PaymentCallback paymentCallback)`](#reversepaymentV2) | Realiza o processo de estorno de pagamento. |
| [`void cancelReversePayment(String paymentId, PaymentCallback paymentCallback)`](#cancelReversepayment) | Desfaz uma solicitação de estorno de pagamento. |


## reversePaymentV2()
Este método deve ser chamado quando se deseja fazer uma solicitação de estorno de pagamento. Durante sua execução, os dados do estorno serão validados, informações adicionais serão solicitadas ao operador (e.g. cartão) e a autorização junto à adquirente será feita.

**Note que a transação de estorno não possui confirmação, mas apenas desfazimento. Assim, a confirmação ocorrerá naturalmente com o não envio do desfazimento**, a depender do comportamento de cada adquirente. Caso haja impressão do comprovante do estorno, quando for passado algum dos parâmetros printMerchantReceipt ou printCustomerReceipt com valor true, o estorno será confirmado automaticamente. Neste caso, o desfazimento não será permitido.

Também a depender do comportamento de cada adquirente, é possível que não haja desfazimento para a transação de estorno. Neste caso, estornos aprovados retornarão o valor false no campo "ReversePayment.cancelable". Além disto, caso seja chamado o método cancelReversePayment(), um erro específico será retornado informando que não é possível executar tal operação vide [Códigos de Resposta](./codigo_resposta.md).


### Parâmetros

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| `request` | `ReversePaymentRequestV2` | Sim | Objeto de transferência de dados que conterá as informações da requisição do estorno do pagamento. Note que nem todos os parâmetros são obrigatórios. |
| `callback` | `ReversePaymentCallback` | Sim | Interface que será executada para notificações de sucesso ou erro do processo de estorno. |


### Detalhe dos parâmetros

#### request (ReversePaymentRequestV2)

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| `value` | `BigDecimal` | Não | Valor da transação a ser estornada. Caso não seja preenchido (null), a interface solicitará o valor ao operador. Esta informação é utilizada para validar a integridade da transação que está sendo estornada. |
| `paymentId` | `String` | Sim | Identificador da transação que será estornada. O identificador referido é aquele utilizado na aplicação de pagamentos. |
| `appTransactionId` | `String` | Sim | Identificador da transação integrada. O identificador referidoé o da aplicação que originou a solicitação de estorno. Deve ser o mesmo valor enviado na transação de pagamento. |
| `ApplicationInfo.credentials` | `Credentials` | Sim | Credenciais da aplicação que está solicitando a operação, conforme cadastro na PayStore. Basicamente, trata-se da identificação da aplicação e o token de acesso. |
| `ApplicationInfo.softwareVersion` | `String` | Sim | Versão da aplicação que está solicitando o pagamento. |
| `showReceiptView` (DEPRECATED) | `Boolean` | Não | A Solução irá utilizar o valor dos parâmetros `printMerchantReceipt` e `printCustomerReceipt` para executar a impressão. |
| `printMerchantReceipt` | `Boolean` | Não | Indica se o comprovante do estabelecimento deve ser impresso ou não. O valor padrão é _false_, isto é, o comprovante não é impresso. |
| `printCustomerReceipt` | `Boolean` | Não | Indica se o comprovante do cliente deve ser impresso ou não. O valor padrão é _false_, isto é, o comprovante não é impresso. |


#### callback (ReversePaymentCallback)

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| **`onSuccess`** |     |     | Método para notificação em caso de sucesso |
| `ReversePayment.paymentId` | `String` | Sim | Identificador da transação de estorno. O Identificador referido é aquele utilizado na aplicação de pagamentos. Esta é a informação a ser usada para a confirmação e desfazimento. |
| `ReversePayment.acquirerId` | `String` | Sim | Identificador da transação de estorno para a adquirente. Este é o identificador que consta no arquivo que a adquirente fornece (EDI). Desta forma, é possível realizar, a conciliação do estorno com a transação integrada. |
| `ReversePayment.cancelable` | `Boolean` | Sim | _True_, caso esta transação possa ser desfeita; _False_, caso contrário. |
| `ReversePayment.acquirerResponseCode` | `String` | Sim | Código de resposta da adquirente. |
| `ReversePayment.acquirerResponseDate` | `String` | Sim | Data/hora retornada pela adquirente. |
| `ReversePayment.acquirerAuthorizationNumber` | `String` | Sim | Número da autorização fornecido pela adquirente (consta no comprovante do cliente Portador do Cartão). |
| `ReversePayment.Receipt.clientVia` | `String` | Não | Conteúdo do comprovante - via do cliente. |
| `ReversePayment.Receipt.merchantVia` | `String` | Não | Conteúdo do comprovante - via do estabelecimento. |
| `ReversePayment.acquirerAdditionalMessage` | `String` | Não | Mensagem adicional enviada pela adquirente na resposta da transação. |
| `ReversePayment.ticketNumber` | `String` | Não | Número do cupom gerado pelo terminal para a transação. |
| `ReversePayment.batchNumber` | `String` | Sim | Número do Lote. |
| `ReversePayment.nsuTerminal` | `String` | Sim | NSU gerado pelo terminal para a transação. |
| `ReversePayment.cardholderName` | `String` | Não | Nome do portador do cartão. |
| `ReversePayment.cardBin` | `String` | Sim | Seis primeiros dígitos do cartão. |
| `ReversePayment.panLast4Digits` | `String` | Sim | Quatro últimos dígitos do cartão. |
| `ReversePayment.terminalId` | `String` | Sim | Identificação do terminal. |
| **`onError`** |     |     | Método para notificação em caso de erro. |
| `ErrorData.paymentsResponseCode` | `String` | Sim | Código de resposta para o erro ocorrido. Vide [Códigos de Resposta](./codigo_resposta.md) |
| `ErrorData.acquirerResponseCode` | `String` | Não | Código de resposta para o erro ocorrido retornado pela adquirente. Note que este erro só será retornado se a transação não for autorizada pela adquirente. |
| `ErrorData.acquirerAdditionalMessage` | `String` | Não | Mensagem adicional enviada pela adquirente na resposta da transação. |
| `ErrorData.responseMessage` | `String` | Sim | Mensagem descritiva da causa da não autorização. Caso a transação tenha sido negada pela adquirente, conterá a mensagem retornada pela adquirente. |


## cancelReversePayment()
Este método deve ser chamado para desfazer uma transação de estorno anteriormente autorizada. Esta transação deve não ter sido desfeita ainda e deve ter sido autorizada (não negada) previamente.

Como dito na descrição de **reversePayment()**, é possível que não haja desfazimento para a transação de estorno para uma determinada adquirente. Assim, o método **cancelReversePayment()** pode retornar um erro específico informando que não é possível executar tal operação (vide Códigos de Resposta).

### Parâmetros

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| `paymentId` | `String` | Sim | Identificador da transação que será desfeita. O Identificador referido é aquele utilizado na aplicação de pagamentos. |
| `callback` | `PaymentCallback` | Sim | Interface que será executada para notificações de sucesso ou erro. |

## Detalhe dos parâmetros

### callback

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| **`onSuccess`** |     |     | Método para notificação em caso de sucesso |
| **`onError`** |     |     | Método para notificação em caso de erro. |
| `ErrorData.paymentsResponseCode` | `String` | Sim | Código de resposta para o erro ocorrido. Vide [Códigos de Resposta](../codigo_resposta/) |
| `ErrorData.acquirerResponseCode` | `String` | Não | Código de resposta para o erro ocorrido retornado pela adquirente. Note que este erro só será retornado se a transação não for autorizada pela adquirente. |
| `ErrorData.responseMessage` | `String` | Sim | Mensagem descritiva da causa da não autorização. Caso a transação tenha sido negada pela adquirente, conterá a mensagem retornada pela adquirente. |

### Exemplo do fluxo de Estorno

```java
import androidx.appcompat.app.AppCompatActivity;

import android.content.Intent;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import java.math.BigDecimal;

import br.com.phoebus.android.payments.api.Credentials;
import br.com.phoebus.android.payments.api.ErrorData;
import br.com.phoebus.android.payments.api.PaymentClient;
import br.com.phoebus.android.payments.api.ReversePayment;
import br.com.phoebus.android.payments.api.ReversePaymentRequestV2;
import br.com.phoebus.android.payments.api.exception.ClientException;

public class MainActivity extends AppCompatActivity implements View.OnClickListener, PaymentClient.PaymentCallback<ReversePayment> {

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
        ReversePaymentRequestV2 request = new ReversePaymentRequestV2();
        request.setValue(new BigDecimal(50));
        request.setAppTransactionId("123456");

        request.setPaymentId("999999");
        request.setPrintCustomerReceipt(true);
        request.setPrintMerchantReceipt(true);

        Credentials credentials = new Credentials();
        credentials.setApplicationId(TEST_APPLICATION_ID);
        credentials.setSecretToken(TEST_SECRET_TOKEN);

        request.setCredentials(credentials);

        try {
            paymentClient.reversePaymentV2(request, this);
        } catch (ClientException e) {
            Log.e(TAG, "Error reversePaymentV2", e);
        }
    }


    @Override
    public void onSuccess(ReversePayment  reversePayment) {
        Log.i(TAG, reversePayment.toString());

        /*
          Se, na sua regra de negócio, for preciso desfazer a
          transação por algum motivo, chame o método
          cancelReversePayment()
        **/
        //doCancelReversePayment(reversePayment);

    }

    @Override
    public void onError(ErrorData errorData) {
        Log.e(TAG, errorData.getResponseMessage());
        Toast.makeText(this, errorData.getResponseMessage(), Toast.LENGTH_LONG).show();
    }

    private void doCancelReversePayment(ReversePayment reversePayment) {
        try {
            paymentClient.cancelReversePayment(reversePayment.getPaymentId(),
                    new PaymentClient.PaymentCallback<ReversePayment>() {

                        @Override
                        public void onError(ErrorData errorData) {
                            Log.e(TAG, errorData.getResponseMessage());
                            Toast.makeText(MainActivity.this, errorData.getResponseMessage(), Toast.LENGTH_LONG).show();
                        }

                        @Override
                        public void onSuccess(ReversePayment reversePayment) {
                            Log.i(TAG, reversePayment.toString());
                        }


                    });
        } catch (ClientException e) {
            Log.e(TAG, "Error cancelReversePayment", e);
        }
    }
}
```

