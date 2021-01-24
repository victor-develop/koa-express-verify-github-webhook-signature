# koa-express-verify-github-webhook-signature
A middlware to verify HMAC with SHA-256 signature. Typically for verifying github webhooks, and many other platforms that is using the same algorithm for webhook signature

# How to quickly secure your git webhook

## Setup in Github
https://docs.github.com/en/developers/webhooks-and-events/securing-your-webhooks#setting-your-secret-token

## For koa

### Usage

```javascript
const verifyHmacSha256 = require('koa-express-verify-github-webhook-signature').koa
const secret = 'the-secert-for-signature' // just demo, do not expose secret in plain code
const Koa = require('koa');
const app = new Koa();
app.use(verifyHmacSha256({secret}));
app.listen(3000);
```

This middleware will validate the incoming requests and throw a 422 using `ctx.throw(422)`. If you want to cutomize the error handling, you can cutomize an `customErrorKey` attribute, which will be carried by the error thrown out in case of faoied verification. Note that 422 won't be given if a customErrorKey is set, and the handling will be up to you.

### Custom error handling

```javascript
app.use(
    verifyHmacSha256({
        secret,
        customErrorKey: 'my custom error'
    })
);
```

And then you can do something like this in the error handling to identify this particular error

```javascript
const failToVerifyWebhookSignature = 'my custom error'

app
.use(
    async function handleError() {
        try {
            await next()
        } catch (err) {
            if (err.customErrorKey === failToVerifyWebhookSignature ) {
                // perform your own handling
            }
            else {
                // other handling
            }
        }
    }
)
.use(
    verifyHmacSha256({
        secret,
        customErrorKey: failToVerifyWebhookSignature
    })
);
```
