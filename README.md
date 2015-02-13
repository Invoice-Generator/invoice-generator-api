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

- Go: [generate invoices programatically](https://github.com/Invoiced/go-invoice-generator-connector)
- Ruby: [generate invoices from Stripe webhooks](https://github.com/Invoiced/ruby-stripe-invoice-generator)

## Examples

### Getting Started

```
curl https://invoice-generator.com \
  -d from='Invoiced, Inc.' \
  -d to=Parag \
  -d logo='https://invoiced.com/img/logo-invoice.png' \
  -d number=1 \
  -d date='Feb 9, 2015' \
  -d due_date='Feb 16, 2015' \
  -d items[0][name]='Starter plan monthly' \
  -d items[0][quantity]=1 \
  -d items[0][unit_cost]=99 \
  -d notes='Thanks for being an awesome customer!' \
  -d terms='Please pay by the due date.' \
> invoice.pdf
```

### JSON Input

JSON input is also accepted with the `Content-Type` header set to `application/json`

```
curl https://invoice-generator.com \
  -H 'Content-Type: application/json' \
  -d '{"from":"Invoiced, Inc.","to":"Parag","logo":"https://invoiced.com/img/logo-invoice.png","number":1,"items":[{"name":"Starter plan","quantity":1,"unit_cost":99}],"notes":"Thanks for your business!"}' \
> invoice.pdf
```

### VAT Example

Here's a simple cURL example for generating invoices with VAT:

```
curl https://invoice-generator.com \
  -d from='Invoiced, Inc.\nVAT ID: 1234' \
  -d to="Jared\nVAT ID: 4567" \
  -d logo='https://invoiced.com/img/logo-invoice.png' \
  -d number=1 \
  -d date='Feb 9, 2015' \
  -d payment_terms='Auto-Billed - Do Not Pay' \
  -d items[0][name]='Starter Plan Monthly' \
  -d items[0][quantity]=1 \
  -d items[0][unit_cost]=99 \
  -d tax_title='VAT' \
  -d fields[tax]='%' \
  -d tax='8' \
  -d notes='Thanks for being an awesome customer!' \
  -d terms='No need to submit payment. You will be auto-billed for this invoice.' \
> invoice.vat.pdf
```

## Parameter Reference

### Invoice Options

|Parameter|Description|Default Value
|:--------|:----------|:------------
`currency`|ISO 4217 3-digit currency code|USD
`fields`|Hash - see Invoice Fields below|`{"tax":"%","discounts":false,"shipping":false}`

### Invoice Template

These parameters control the titles of the fields on the invoice template.

|Parameter|Default Value
|:--------|:------------
`header`|INVOICE
`to_title`|Client
`invoice_number_title`|Invoice Number
`date_title`|Date
`payment_terms_title`|Payment Terms
`due_date_title`|Due Date
`quantity_header`|Quantity
`item_header`|Item
`unit_cost_header`|Rate
`amount_header`|Amount
`subtotal_title`|Subtotal
`discounts_title`|Discounts
`tax_title`|Tax
`shipping_title`|Shipping
`total_title`|Total
`amount_paid_title`|Amount Paid
`balance_title`|Balance
`terms_title`|Terms
`notes_title`|Notes

### Invoice

When a value is null or zero, the field will not be shown on the invoice. The exception to this are the required fields `from`, `to`, `date`, and `items`.

|Parameter|Description|Default Value
|:--------|:----------|:------------
`logo`|URL of your logo|*null*
`from`|The name of your organization|*null*
`to`|The entity being billed - multiple lines ok|*null*
`number`|Invoice number|*null*
`purchase_order`|Purchase order number|*null*
`date`|Invoice date|current date
`payment_terms`|Payment Terms summary (i.e. NET 30)|*null*
`due_date`|Invoice due date|*null*
`items`|Array of hashes - see Line Items below|`[]`
`discounts`|Subtotal discounts|0
`tax`|Tax|0
`shipping`|Shipping|0
`amount_paid`|Amount Paid|0
`notes`|Notes|*null*
`terms`|Terms|*null*

### Invoice Fields

Invoice fields can be added at the end of the invoice. We support discounts, tax, and shipping. The fields are expressed as a hash with values that can be either `%`, `true`, or `false`.

```json
{
  "tax": "%",
  "discounts": false,
  "shipping": true
}
```

### Line Items

Line items are a hash that requires the `name` parameter to be not empty. Any or all of these parameters can be used:

```json
{
  "name": "Gizmo",
  "quantity": 10,
  "unit_cost": 99.99,
  "description": "The best gizmos there are around."
}
```

## Feature Requests and Bug Reports

We would love to hear your thoughts! You can [create an issue](https://github.com/Invoiced/invoice-generator-api/issues) on GitHub for any issues you encounter or feature requests. If the matter is more private you can reach us at hello@invoiced.com.

## Terms of Use and Privacy Policy

Invoice-generator.com (a service of Invoiced) does not store any of your information on its servers. The functionality on invoice-generator.com is provided "as is" with no expressed or implied warranties. By using invoice-generator.com you agree to not hold its owner liable for any consequential or incidental damages.

We place no restrictions how you use invoices generated from our service. The service itself including the use of the Invoiced name, invoice template, domain names and other distinctive brand features are protected by copyright and other laws: © Invoiced. All rights reserved.