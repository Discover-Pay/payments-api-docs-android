
# Integração com Aplicação de Pagamentos via Broadcast
Diferente das outras integrações, na integração via broadcast, outras aplicações podem receber notificações de que um pagamento ou um estorno foi efetuado. Esta informação é enviada pela aplicação de pagamentos via Broadcasts que podem ser recebidos por quaisquer aplicações que tenham interesse em saber da ocorrência de pagamentos ou estornos.

## Actions
| Action | Extra |
| --- | --- |
| `Intents.action.ACTION_AFTER_PAYMENT` (`br.com.phoebus.android.payments.AFTER_PAYMENT_FINISHED`) | `Intents.extra.EXTRA_PAYMENT_RETURN`: `Payment` (vide [startPaymentV2()](#startpayment) ou documentação da classe.) |
| `Intents.action.ACTION_AFTER_REVERSAL` (`br.com.phoebus.android.payments.AFTER_PAYMENT_REVERSAL_FINISHED`) | `Intents.extra.EXTRA_PAYMENT_RETURN`: `ReversePayment` (vide [reversePaymentV2()](#reversepayment) ou documentação da classe.) |
| `Intents.action.ACTION_AFTER_SETTLEMENT` (`br.com.phoebus.android.payments.ACTION_AFTER_SETTLEMENT`) | `Intents.extra.EXTRA_SETTLEMENT_RETURN`: `SettlementBroadcastResponse` (vide [closeBatch()](#closeBatch) ou [documentação da classe](../settlement_broadcast_response/).) |

## Exemplo
```java
public class MyBroadcastReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {

        if (intent.getAction().equals(Intents.action.ACTION_AFTER_PAYMENT)) {
            Payment payment = DataUtils.fromBundle(Payment.class, intent.getExtras(), Intents.extra.EXTRA_PAYMENT_RETURN);

            // Do something!

        } else if (intent.getAction().equals(Intents.action.ACTION_AFTER_REVERSAL)) {
            ReversePayment reversePayment = DataUtils.fromBundle(ReversePayment.class, intent.getExtras(), Intents.extra.EXTRA_PAYMENT_RETURN);

            // Do something!

        } else if (intent.getAction().equals(Intents.action.ACTION_AFTER_SETTLEMENT)) {
            SettlementBroadcastResponse settlementBroadcastResponse = DataUtils.fromBundle(SettlementBroadcastResponse.class, intent.getExtras(), Intents.extra.EXTRA_SETTLEMENT_RETURN);

            // Do something!
        }
    }
}
```


[[Voltar APIs]](./README.md)
