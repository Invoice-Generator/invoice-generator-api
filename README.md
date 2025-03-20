# Invoice Generator API

We created a simple API at Invoice-Generator.com to generate invoice PDFs on the fly. This service has been used internally by us for some time. We believe this could be helpful in your project as well.

The API has a primary endpoint that returns a PDF given details of an invoice. We don't store any of your invoice data.

In addition to PDF, the API can also generate e-invoices in UBL (Universal Business Language) with the invoice PDF embedded. This is useful as the world shifts to e-invoicing because UBL invoices are tricky to generate.

### Use Cases
- Creating invoices for VAT compliance
- Generate a PDF of an invoice that you have the details to (recipient, line items, etc)
- Produce invoices for B2B buyers from an order or receipt
- Selling products or services on credit terms
- Creating e-invoices in UBL (Universal Business Language)

## Table of Contents

- [Getting Started](#getting-started)
- [Examples](#examples)
  - [Simple Invoice](#simple-invoice)
  - [JSON Input](#json-input)
  - [VAT Invoice](#vat-invoice)
  - [Localization](#localization)
  - [Custom Fields](#custom-fields)
  - [E-invoice](#e-invoice)
  - [Sample Projects](#sample-projects)
- [API Reference](#api-reference)
  - [Create Invoice PDF](#create-invoice-pdf)
  - [Create E-invoice](#create-e-invoice)
- [Support](#support)

## Getting Started

In order to begin using the Invoice-Generator.com API, you first need to create an API key. The process for obtaining an API key is as follows.

1. Create a free Invoice-Generator.com account [here](https://invoice-generator.com/signup), or sign in if you already have one.
2. Go to the **Settings** page.
3. In the "API Keys" section, click **New API Key**.

Now you have an API key and are ready to start invoicing!

## Examples

### Simple Invoice

```bash
curl https://invoice-generator.com \
  -H "Authorization: Bearer myApiKey" \
  -d from="Nikolaus Ltd" \
  -d to="Acme, Corp." \
  -d logo="https://example.com/img/logo-invoice.png" \
  -d number=1 \
  -d date="Feb 9, 2015" \
  -d due_date="Feb 16, 2015" \
  -d "items[0][name]"="Starter plan monthly" \
  -d "items[0][quantity]"=1 \
  -d "items[0][unit_cost]"=99 \
  -d notes="Thanks for being an awesome customer\!" \
  -d terms="Please pay by the due date." \
> invoice.pdf
```

### VAT Invoice

Here's a simple cURL example for generating invoices with VAT:

```bash
curl https://invoice-generator.com \
  -H "Authorization: Bearer myApiKey" \
  -d from="Nikolaus Ltd%0AVAT ID: 1234" \
  -d to="Foster Moen%0AVAT ID: 4567" \
  -d logo="https://example.com/img/logo-invoice.png" \
  -d number=1 \
  -d date="Feb 9, 2015" \
  -d payment_terms="Charged - Do Not Pay" \
  -d "items[0][name]"="Starter Plan Monthly" \
  -d "items[0][quantity]"=1 \
  -d "items[0][unit_cost]"=99 \
  -d tax_title="VAT" \
  -d "fields[tax]"="%" \
  -d tax=8 \
  -d notes="Thanks for being an awesome customer\!" \
  -d terms="No need to submit payment. You will be auto-billed for this invoice." \
> invoice.vat.pdf
```

### JSON Input

JSON input is also accepted with the `Content-Type` header set to `application/json`

```bash
curl https://invoice-generator.com \
  -H "Authorization: Bearer myApiKey" \
  -H "Content-Type: application/json" \
  -d '{"from":"Nikolaus Ltd","to":"Acme, Corp.","logo":"https://example.com/img/logo-invoice.png","number":1,"items":[{"name":"Starter plan","quantity":1,"unit_cost":99}],"notes":"Thanks for your business!"}' \
> invoice.pdf
```

### Localization

It is possible to change the localization used to generate the invoice by supplying a locale in the `Accept-Language` header. The default locale is `en-US`.

```bash
curl https://invoice-generator.com \
  -H "Authorization: Bearer myApiKey" \
  -H "Accept-Language: fr-FR" \
  -d from="Nikolaus Ltd" \
  -d to="Acme Corp." \
  -d logo="https://example.com/img/logo-invoice.png" \
  -d number=1 \
  -d currency=eur \
  -d date="Feb 9, 2015" \
  -d due_date="Feb 16, 2015" \
  -d "items[0][name]"="Starter plan monthly" \
  -d "items[0][quantity]"=1 \
  -d "items[0][unit_cost]"=99 \
> invoice.pdf
```

#### Supported Languages

We currently have translations available in English, French, German, Spanish, and Thai.

### Custom Fields

```bash
curl https://invoice-generator.com \
  -H "Authorization: Bearer myApiKey" \
  -d from="Nikolaus Ltd" \
  -d to="My Customer" \
  -d ship_to="Shipping Address" \
  -d logo="https://example.com/img/logo-invoice.png" \
  -d number=1 \
  -d date="Feb 9, 2015" \
  -d "custom_fields[0][name]"="My Custom Field" \
  -d "custom_fields[0][value]"="Some Value" \
  -d "items[0][name]"="Starter Plan Monthly" \
  -d "items[0][quantity]"=1 \
  -d "items[0][unit_cost]"=99 \
> invoice.custom_fields.pdf
```

### E-invoice

Here's a simple cURL example for generating e-invoices in UBL XML:

```bash
curl https://invoice-generator.com/ubl \
  -H "Authorization: Bearer myApiKey" \
  -d from="Nikolaus Ltd%0AVAT ID: 1234" \
  -d to="Foster Moen%0AVAT ID: 4567" \
  -d logo="https://example.com/img/logo-invoice.png" \
  -d number=1 \
  -d date="Feb 9, 2015" \
  -d date="Mar 9, 2015" \
  -d payment_terms="NET 30" \
  -d "items[0][name]"="Starter Plan Monthly" \
  -d "items[0][quantity]"=1 \
  -d "items[0][unit_cost]"=99 \
  -d tax_title="VAT" \
  -d "fields[tax]"="%" \
  -d tax=8 \
  -d notes="Thanks for being an awesome customer\!" \
> invoice.xml
```

### Sample Projects

- Go: [generate invoices programmatically](https://github.com/Invoice-Generator/go-invoice-generator-connector)
- Ruby: [generate invoices from Stripe webhooks](https://github.com/Invoice-Generator/ruby-stripe-invoice-generator)
- Node.js: [invoice-generator.js](https://github.com/Invoice-Generator/invoice-generator.js)
- Python: [python-invoice-generator](https://github.com/aleaforny/python-invoice-generator)

************

## API Reference

### Create Invoice PDF

```text
POST https://invoice-generator.com
```

#### Invoice Parameters

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

#### Line Item Parameters

Line items are represented as an array of objects. Here's an example:

```json
{
  "items": [
    {
      "name": "Gizmo",
      "quantity": 10,
      "unit_cost": 99.99,
      "description": "The best gizmos there are around."
    },
    {
      "name": "Gizmo v2",
      "quantity": 5,
      "unit_cost": 199.99
    }
  ]
}
```

#### Subtotal Line Parameters

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

#### Custom Field Parameters

Custom fields allow you to add additional fields to the invoice details in the top-right. Here's an example:

```json
{
  "custom_fields": [
    {
      "name": "Gizmo",
      "value": "PO-1234"
    },
    {
      "name": "Account Number",
      "value": "CUST-456"
    }
  ]
}
```

#### Invoice Template Parameters

These parameters control the titles of the fields on the invoice template. If localization is used, the default values are translated to the specified language. Any invoice template parameter given will override the localized default.

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

### Create E-invoice

```text
POST https://invoice-generator.com/ubl
```

#### Parameters

Creating an invoice with universal business language uses the same parameters as the [Create Invoice PDF](#create-invoice-pdf) endpoint.

## Support

Have a feature request or bug report? We would love to hear your thoughts! You can [create an issue](https://github.com/Invoice-Generator/invoice-generator-api/issues) on GitHub for any issues you encounter or feature requests.

Using invoice-generator.com is subject to the [Privacy Policy and Terms of Use](https://invoice-generator.com/terms).