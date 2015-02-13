# Invoice Generator API
For those times when you just need an invoice

## About

We created a simple API at Invoiced to generate invoice PDFs on the fly. This service has been used internally by us for some time. We believe this could be useful for other projects as well.

The API only has a single endpoint that returns a PDF. We don't store any of your invoice data.

### Endpoint

``https://invoice-generator.com``

### Rate Limiting

The API is rate limited, however, it should be sufficient for a reasonable amount of testing and use. If you plan to use this in production let's talk at hello@invoiced.com.

## Sample Projects using API

- Go: generate invoices programatically
- Ruby: generate invoices from Stripe webhooks

## Examples

### Getting Started

```
curl https://invoice-generator.com \
  -d from='Invoiced, Inc.' \
  -d to=Parag \
  -d logo='https://invoiced.com/img/header-logo.png' \
  -d number=1 \
  -d date='Feb 9, 2015' \
  -d due_date='Feb 16, 2015' \
  -d items[0][name]='Starter plan monthly' \
  -d items[0][quantity]=1 \
  -d items[0][unit_cost]=99 \
  -d notes='Thanks for being an awesome customer!' \
  -d terms='No need to submit payment. You will be auto-billed for this invoice.' \
> invoice.pdf
```

### JSON Input

JSON input is also accepted with the `Content-Type` header set to `application/json`

```
curl https://invoice-generator.com \
  -H Content-Type=application/json \

  -d from='Invoiced, Inc.' \
  -d to=Parag \
  -d logo='https://invoiced.com/img/header-logo.png' \
  -d number=1 \
  -d date='Feb 9, 2015' \
  -d due_date='Feb 16, 2015' \
  -d items[0][name]='Starter plan monthly' \
  -d items[0][quantity]=1 \
  -d items[0][unit_cost]=99 \
  -d notes='Thanks for being an awesome customer!' \
  -d terms='No need to submit payment. You will be auto-billed for this invoice.' \
> invoice.pdf
```

### VAT Example

Here's a simple cURL example for generating invoices with VAT.

TODO

### Customizable Template

TODO

## Parameter Reference

TODO

## Feature Requests and Bug Reports

We would love to hear your thoughts! You can [create an issue](https://github.com/Invoiced/invoice-generator-api/issues) on GitHub for any issues you encounter or feature requests. If the matter is more private you can reach us at hello@invoiced.com.

## Terms of Use and Privacy Policy

Invoice-generator.com (a service of Invoiced) does not store any of your information on its servers. You may use documents generated from our service for any purpose, including commercially. The functionality on invoice-generator.com is provided "as is" with no expressed or implied warranties. By using invoice-generator.com you agree to not hold its owner liable for any consequential or incidental damages.

The service itself including the use of the Invoiced name, invoice template, domain names and other distinctive brand features are protected by copyright and other laws: © Invoiced. All rights reserved.