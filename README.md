# Invoice Generator API
For those times when you just need an invoice

## cURL Examples

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