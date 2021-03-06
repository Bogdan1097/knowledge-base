# Tokens issuance

As you know, the key concept is that if user hold your token, he doesn't need to
hold the actual asset it represents.

Every token has it's owner and he decides what the politics of issuing the 
tokens will be. The most straight way is to perform the issuance directly to 
someones account (it can also be the owner itself). 

### Balance comes first

To prevent spam attacks (TokenD user must be sure that he won't get unwanted 
tokens), the issuance is performed to balance, not the account itself. So, for 
example, if Bob want to issue the `BNN` token to the Alice's account and
Alice doesn't have the `BNN` balance, Bob won't be able to perform the 
issuance. If Alice knows about Bob's token and is agreed to hold it, 
she should create the `BNN` balance first. To do this, 
Alice needs to perform [balance creation operation][manage_balance_op]: 

```javascript
async function createBalance () {
    const operation = base.operation.ManageBalance({
        destination: 'GBYMMGDOS32QIMZ2HX4DYVXNFVDEE4G3IUSKNLM44MCTOFSCYRPF7KDE', // Alice's account ID
        asset: 'BNN',
        action: xdr.ManageBalanceAction.create()
    })

    await horizon.transactions.submitOperations(operation)
}
```

After this, Bob can obtain Alice's `BNN` balance ID and issue tokens to her.

> *Note:* The `ManageBalance` operation can be performed not only by Alice 
but by master signer who has the [`BALANCE_MANAGER`][signer_types] policy. 

## Issue tokens to specific balance

Now all preparations are done, and bob is ready to issue the tokens to Alice. 
Let's consider `BNN` token currently has such state:

```json
{
  "code": "BNN",
  "owner": "GCXFATAJU2O2JRNQIXQY6IIXCVDFCENYG4WV43YSWEDYYBPRPMVAVWIE",
  "available_for_issuance": "500.000000",
  "preissued_asset_signer": "GAONUHILXGWHMQNWBE4GZ3GDS6A5KDTTUV7XBDNFRI2JKAPATWVYFEYJ",
  "max_issuance_amount": "500.000000",
  "issued": "0.000000",
  "pending_issuance": "0.000000",
  "policy": 0,
  "policies": []
}
```

We can see here it's `available_for_issuance` field, which tells us how much 
tokens can still be issued. So, Bob wants to issue to Alice `100` tokens. We 
can see that currently available `500` Banana tokens, so all Bob can go on.
At first he needs to get Alice's BNN balance [by her account ID][get_account_balances] 
while account ID can be [obtained by email][get_account_by_email].
All Bob needs to do now is to perform such operation:

```javascript
async function performIssuance () {
    const operation = base.CreateIssuanceRequestBuilder({
      asset: 'BNN',
      amount: '100.000000',
      receiver: 'BBKVOTHCUDI4X5MFYNQN7YEAJYY7OPS3HO7J3BBESPQCV23MXW7LLMKR',
      reference: 'Some unique random string',
      externalDetails: {
        foo: 'bar'
      }
    })

    await horizon.transactions.submitOperations(operation)
}
```

[manage_balance_op]: /tech/operations/manage_balance.md
[signer_types]: /tech/key_entities/signer.md#signer-types
[get_account_balances]: https://tokend.gitlab.io/docs/#get-account-balances
[get_account_by_email]: https://tokend.gitlab.io/docs/#get-account-id-by-email

