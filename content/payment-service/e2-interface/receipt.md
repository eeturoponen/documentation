---
title: "Receiving the Payment Receipt"
draft: false
weight: 3
---

Payment receipt includes fields defined in `PARAMS_OUT`. Receipt always includes values `PAYMENT_ID`, `TIMESTAMP`, `STATUS` and `RETURN_AUTHCODE`.

After the payment has been successfully completed, the customer is redirected to the URL defined in the previous stage (`URL_SUCCESS`). If the payment was cancelled, the customer is directed to the cancelled payment URL (`URL_CANCEL`).

The notification address (`URL_NOTIFY`) is called when Paytrail marks the payment as completed. Typically this happens within a few minutes after redirecting the customer to `URL_SUCCESS`. Note that in some rare cases `URL_NOTIFY` is called before the customer is redirected to `URL_SUCCESS`. `URL_NOTIFY` call includes parameters defined in `PARAMS_OUT_NOTIFY`. The received notification must be acknowledged by responding with an HTTP status in the _200-299_ range.

If the customer does not return to Paytrail's service from the payment method provider's service, the information on successful payment will not be immediately available. In this case `URL_NOTIFY` will be called immediately when that information has arrived. We use payment status query services provided by payment method providers to speed up the process of payment being marked as completed. When we receive information on payment being completed the notification address is called.

In cases where payment method provider does not provide payment status query service, information about completed payment arrives on the next banking day. The only payment method provider currently not providing payment status query service is Ålandsbanken.

The receipt carries unique return information that is used to verify the validity of the receipt and that the payment was actually successful. Return authentication `AUTHCODE` is compared to the hash calculated and if the values match, the payment receipt was not tampered.

In `RETURN_AUTHCODE` calculation, fields are joined using the `|` character (pipe, vertical bar) as separator. Authcode calculation fields are joined in the order defined in `PARAMS_OUT` field. Merchant hash is appended to the string.

{{< table caption="Receipt contains these fields" >}}
    <thead>
        <tr>
            <th>Field name</th>
            <th>Description</th>
            <th>Notes</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ORDER_NUMBER</td>
            <td>This is the same order number that was sent to the Payment Gateway.</td>
            <td>&nbsp;</td>
        </tr>
        <tr>
            <td>PAYMENT_ID</td>
            <td>Unique id for payment.</td>
            <td>Value is always included.</td>
        </tr>
        <tr>
            <td>AMOUNT</td>
            <td>Payment total</td>
            <td>AMOUNT equals the value provided in payment creation.</td>
        </tr>
        <tr>
            <td>CURRENCY</td>
            <td>Currency.</td>
            <td>&nbsp;</td>
        </tr>
        <tr>
            <td>PAYMENT_METHOD</td>
            <td>Payment method which was selected at payment method page (or preselected with value
                PAYMENT_METHODS).</td>
            <td>Value is empty if no payment method has been selected.</td>
        </tr>
        <tr>
            <td>TIMESTAMP</td>
            <td>
                Timestamp generated by the Payment Gateway that is used to calculate the return authentication hash.
                Timestamp is in UNIX format, i.e. seconds from 1/1/1970.
            </td>
            <td>Value is always included.</td>
        </tr>
        <tr>
            <td>STATUS</td>
            <td>It is used to verify the validity of a successful payment.</td>
            <td>Possible values PAID and CANCELLED.</td>
        </tr>
        <tr>
            <td>SETTLEMENT_REFERENCE_NUMBER</td>
            <td>This is the same reference number that was sent to the Payment Gateway or the reference
                number
                generated by Paytrail.</td>
            <td>&nbsp;</td>
        </tr>
        <tr>
            <td>RETURN_AUTHCODE</td>
            <td>
                If the value matches the calculated one, the payment has been completed and the information has not
                been modified after sending.
            </td>
            <td>Value is always included.</td>
        </tr>
    </tbody>
{{< /table >}}
