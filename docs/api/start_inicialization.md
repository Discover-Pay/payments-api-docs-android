# Inicia o processo de inicialização com a DiscoverPayStore e com a Adquirente

Esse método deve ser chamado para fazer a inicialização do terminal com a Paystore e com a(s) adquirente(s) instalada(s).

## Métodos

| Assinatura | Descrição |
| --- | --- |
| `void startInitialization(String activityAction, PaymentCallback paymentCallback)` | Inicia o processo de inicialização com a Paystore e com a Adquirente. |
| `void startInitialization(InitializationRequest initializationRequest, PaymentCallback paymentCallback)` | Inicia o processo de inicialização com a Paystore e com a Adquirente, utilizando `InitializationRequest`. |

## Parâmetros

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| `activityAction` | `String` | Sim | Action da activity da aplicação externa que deve ser chamada após a inicialização ser concluída. |
| `callback` | `PaymentCallback` | Sim | Interface que será executada para notificações de sucesso ou erro. |
| `initializationRequest` | `InitializationRequest` | Sim | Objeto de transferência de dados que conterá as informações da requisição da inicialização. |
| `paymentCallback` | `PaymentCallback` | Sim | Interface que será executada para notificações de sucesso ou erro. |


## Detalhe dos parâmetros
### callback
| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| **`onSuccess`** |     |     | Método para notificação em caso de sucesso |
| **`onError`** |     |     | Método para notificação em caso de erro. |
| `ErrorData.paymentsResponseCode` | `String` | Sim | Código de resposta para o erro ocorrido. Vide [Códigos de Resposta](../codigo_resposta/) |
| `ErrorData.responseMessage` | `String` | Sim | Mensagem descritiva da causa da não autorização. Caso a transação tenha sido negada pela adquirente, conterá a mensagem retornada pela adquirente. |

### request (InitializationRequest)

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| `activityAction` | `String` | Não | Action da aplicação externa que deve ser chamada após a inicialização ser concluída. |
| `installToken` | `String` | Não | Token de Instalação para credenciamento do terminal na Paystore. Este parâmetro só será considerado na primeira inicialização do terminal. |

## Exemplo
```java
public class MyActivity extends Activity implements PaymentClient.PaymentCallback {

    private PaymentClient paymentClient;
    private String action = "br.com.phoebus.payments.demo.ACTION_INITIALIZE";

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
        InitializationRequest request = new InitializationRequest();
        request.setInstallToken("1a2b3cdef");
        request.setActivityAction("123456abcde");

        ApplicationInfo appInfo = new ApplicationInfo();
        appInfo.setCredentials(new Credentials("demo-app", "TOKEN-KEY-DEMO"));
        appInfo.setSoftwareVersion("1.0.0.0");

        try {
            paymentClient.startInitialization(request, this);
        } catch (ClientException e) {
            Log.e(TAG, "Error while initialization.", e);
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
