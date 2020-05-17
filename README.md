# Invoice Generator API
Simple HTTP API to create invoice PDFs.

- [About](#about)
- [Getting Started](#getting-started)
- [Sample Projects](#sample-projects)
- [API Reference](#api-reference)
- [Rate Limiting](#rate-limiting)
- [Feature Requests and Bug Reports](#feature-requests-and-bug-reports)
- [Terms of Use and Privacy Policy](#terms-of-use-and-privacy-policy)

## About

We created a simple API at Invoiced to generate invoice PDFs on the fly. This service has been used internally by us for some time. We believe this could be helpful in your project as well.

The API only has a single endpoint that returns a PDF. We don't store any of your invoice data.

### Use Cases
- Creating invoices for VAT compliance
- Generate a PDF of an invoice that you have the details to (recipieint, line items, etc)
- Produce invoices for B2B buyers from an order or receipt
- Selling products or services on credit terms

### Endpoint

```
https://invoice-generator.com
```

## Examples

### Getting Started

```
curl https://invoice-generator.com \
  -d from="Invoiced, Inc." \
  -d to=Parag \
  -d logo="https://invoiced.com/img/logo-invoice.png" \
  -d number=1 \
  -d date="Feb 9, 2015" \
  -d due_date="Feb 16, 2015" \
  -d items[0][name]="Starter plan monthly" \
  -d items[0][quantity]=1 \
  -d items[0][unit_cost]=99 \
  -d notes="Thanks for being an awesome customer!" \
  -d terms="Please pay by the due date." \
> invoice.pdf
```

### JSON Input

JSON input is also accepted with the `Content-Type` header set to `application/json`

```
curl https://invoice-generator.com \
  -H "Content-Type: application/json" \
  -d '{"from":"Invoiced, Inc.","to":"Parag","logo":"https://invoiced.com/img/logo-invoice.png","number":1,"items":[{"name":"Starter plan","quantity":1,"unit_cost":99}],"notes":"Thanks for your business!"}' \
> invoice.pdf
```

### VAT Example

Here's a simple cURL example for generating invoices with VAT:

```
curl https://invoice-generator.com \
  -d from="Invoiced, Inc.%0AVAT ID: 1234" \
  -d to="Jared%0AVAT ID: 4567" \
  -d logo="https://invoiced.com/img/logo-invoice.png" \
  -d number=1 \
  -d date="Feb 9, 2015" \
  -d payment_terms="Charged - Do Not Pay" \
  -d items[0][name]="Starter Plan Monthly" \
  -d items[0][quantity]=1 \
  -d items[0][unit_cost]=99 \
  -d tax_title="VAT" \
  -d fields[tax]="%" \
  -d tax=8 \
  -d notes="Thanks for being an awesome customer!" \
  -d terms="No need to submit payment. You will be auto-billed for this invoice." \
> invoice.vat.pdf
```

### Localization

It is possible to change the localization used to generate the invoice by supplying a locale in the `Accept-Language` header. The default locale is `en-US`.

```
curl https://invoice-generator.com \
  -H "Accept-Language: fr-FR" \
  -d from="Invoiced, Inc." \
  -d to=Acme Corp. \
  -d logo="https://invoiced.com/img/logo-invoice.png" \
  -d number=1 \
  -d currency=eur \
  -d date="Feb 9, 2015" \
  -d due_date="Feb 16, 2015" \
  -d items[0][name]="Starter plan monthly" \
  -d items[0][quantity]=1 \
  -d items[0][unit_cost]=99 \
> invoice.pdf
```

### Custom Fields

```
curl https://invoice-generator.com \
  -d from="Invoiced, Inc." \
  -d to="My Customer" \
  -d ship_to="Shipping Address" \
  -d logo="https://invoiced.com/img/logo-invoice.png" \
  -d number=1 \
  -d date="Feb 9, 2015" \
  -d custom_fields[0][name]="My Custom Field" \
  -d custom_fields[0][value]="Some Value" \
  -d items[0][name]="Starter Plan Monthly" \
  -d items[0][quantity]=1 \
  -d items[0][unit_cost]=99 \
> invoice.custom_fields.pdf
```

#### Supported Languages

We currently have translations available in:
- English
- French
- German
- Spanish

## Sample Projects

- Go: [generate invoices programmatically](https://github.com/Invoiced/go-invoice-generator-connector)
- Ruby: [generate invoices from Stripe webhooks](https://github.com/Invoiced/ruby-stripe-invoice-generator)
- Node.js: [invoice-generator.js](https://github.com/Invoiced/invoice-generator.js)
- PHP: [generate invoices programmatically](https://github.com/concept-core/Invoiced)

## API Reference

### Invoice

When a value is null or zero, the field will not be shown on the invoice. The exception to this are the required fields `from`, `to`, `date`, and `items`.

|Parameter|Description|Default Value
|:--------|:----------|:------------
`logo`|URL of your logo|*null*
`from`|Your organization billing address and contact info|*null*
`to`|Entity being billed - multiple lines ok|*null*
`ship_to`|Shipping address - multiple lines ok|*null*
`number`|Invoice number|*null*
`currency`|ISO 4217 3-digit currency code|USD
`custom_fields`|Array of objects - see [Custom Fields](#custom-fields) below|*[]*
`date`|Invoice date|current date
`payment_terms`|Payment terms summary (i.e. NET 30)|*null*
`due_date`|Invoice due date|*null*
`items`|Array of objects - see [Line Items](#line-items) below|`[]`
`fields`|Object - see [Subtotal Lines](#subtotal-lines) below|`{"tax":"%","discounts":false,"shipping":false}`
`discounts`|Subtotal discounts - numbers only|0
`tax`|Tax - numbers only|0
`shipping`|Shipping - numbers only|0
`amount_paid`|Amount paid - numbers only|0
`notes`|Notes - any extra information not included elsewhere|*null*
`terms`|Terms and conditions - all the details|*null*

### Line Items

Line items are represented as an array of objects. Here's an example:

```json
{
  "items": [
    {
      "name": "Gizmo",
      "quantity": 10,
      "unit_cost": 99.99,
      "description": "The best gizmos there are around."
    }
    {
      "name": "Gizmo v2",
      "quantity": 5,
      "unit_cost": 199.99
    }
  ]
}
```

### Subtotal Lines

The `fields` object toggles the `discounts`, `tax`, and `shipping` subtotal lines. Each setting can have a value of `%`, `true`, or `false`. For example to add a percent tax rate and flat shipping to your invoice you would send this:

```json
{
  "fields": {
    "tax": "%",
    "discounts": false,
    "shipping": true
  },
  "tax": 7,
  "shipping": 15
}
```

### Custom Fields

Custom fields allow you to add additional fields to the invoice details in the top-right. Here's an example:

```json
{
  "custom_fields": [
    {
      "name": "Gizmo",
      "value": "PO-1234"
    }
    {
      "name": "Account Number",
      "value": "CUST-456"
    }
  ]
}
```

### Invoice Template

These parameters control the titles of the fields on the invoice template.

|Parameter|Default Value
|:--------|:------------
`header`|INVOICE
`to_title`|Bill To
`ship_to_title`|Ship To
`invoice_number_title`|#
`date_title`|Date
`payment_terms_title`|Payment Terms
`due_date_title`|Due Date
`purchase_order_title`|Purchase Order
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

## Rate Limiting

The invoice-generator.com API is rate limited. With almost every use case this should not be an issue. If an API call does trigger rate limiting then we will respond with a `429` status code to let you know to try generating your invoice again later. If you frequently run into these limits then you might also consider using our paid service at [invoiced.com](https://invoiced.com).

## Feature Requests and Bug Reports

We would love to hear your thoughts! You can [create an issue](https://github.com/Invoiced/invoice-generator-api/issues) on GitHub for any issues you encounter or feature requests. If the matter is more private you can reach us at hello@invoiced.com.

## Terms of Use and Privacy Policy

Using invoice-generator.com is subject to the [Privacy Policy and Terms of Use](https://invoice-generator.com/terms).