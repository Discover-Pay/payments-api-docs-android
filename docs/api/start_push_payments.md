# Faz o envio dos pagamentos para a Paystore
Esse método deve ser chamado para enviar os pagamentos do terminal para a DiscoverPaystore.

## Métodos
| Assinatura | Descrição |
| --- | --- |
| `void startPushPayments(PaymentCallback paymentCallback)` | Faz o envio dos pagamentos para a Paystore. |

## Parâmetros
| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| `callback` | `PaymentCallback` | Sim | Interface que será executada para notificações de sucesso ou erro. |

## Detalhe dos parâmetros
### callback

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| **`onSuccess`** |     |     | Método para notificação em caso de sucesso |
| **`onError`** |     |     | Método para notificação em caso de erro. |
| `ErrorData.paymentsResponseCode` | `String` | Sim | Código de resposta para o erro ocorrido. Vide [Códigos de Resposta](../codigo_resposta/) |
| `ErrorData.responseMessage` | `String` | Sim | Mensagem descritiva da causa da não autorização. Caso a transação tenha sido negada pela adquirente, conterá a mensagem retornada pela adquirente. |


## Exemplo
```java
public class MyActivity extends Activity implements PaymentClient.PaymentCallback {

    private PaymentClient paymentClient;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_payment);

        paymentClient = new PaymentClientImpl();
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
        ApplicationInfo appInfo = new ApplicationInfo();
        appInfo.setCredentials(new Credentials("demo-app", "TOKEN-KEY-DEMO"));
        appInfo.setSoftwareVersion("1.0.0.0");

        try {
            paymentClient.startPushPayments(this);
        } catch (ClientException e) {
            Log.e(TAG, "Error while sending payments to Paystore.", e);
        }
    }

    @Override
    public void onError(ErrorData errorData) {
        Log.e(TAG, "Error: " + errorData.getResponseMessage());
    }

    @Override
    public void onSuccess(Object data) {
        Log.i(TAG, "Success!");
    }
}
```

[[Voltar]](./README.md)
