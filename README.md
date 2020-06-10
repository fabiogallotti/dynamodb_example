# DynamoDB example

To correctly import the starfleet.csv file you need to format it in batches of json file with maximum 25 items.

Run `ruby csv-to-json.rb` to get the json files in the batches folder.

## Create DynamoDB Table

```bash
aws dynamodb create-table \
  --table-name Starships \
  --attribute-definitions \
    AttributeName=ShipClass,AttributeType=S \
    AttributeName=Registry,AttributeType=S \
  --key-schema \
    AttributeName=ShipClass,KeyType=HASH \
    AttributeName=Registry,KeyType=RANGE \
  --provisioned-throughput \
    ReadCapacityUnits=5,WriteCapacityUnits=5
  --region us-east-1
```

## Describe Table

```bash
aws dynamodb describe-table --table-name Starships --output table
```

## Import data, Batch-Write-Item

```bash
aws dynamodb batch-write-item --request-items file://batches/batch-000.json
aws dynamodb batch-write-item --request-items file://batches/batch-001.json
aws dynamodb batch-write-item --request-items file://batches/batch-002.json
aws dynamodb batch-write-item --request-items file://batches/batch-003.json
aws dynamodb batch-write-item --request-items file://batches/batch-004.json
aws dynamodb batch-write-item --request-items file://batches/batch-005.json
aws dynamodb batch-write-item --request-items file://batches/batch-006.json
aws dynamodb batch-write-item --request-items file://batches/batch-007.json
aws dynamodb batch-write-item --request-items file://batches/batch-008.json
aws dynamodb batch-write-item --request-items file://batches/batch-009.json
aws dynamodb batch-write-item --request-items file://batches/batch-010.json
aws dynamodb batch-write-item --request-items file://batches/batch-011.json
aws dynamodb batch-write-item --request-items file://batches/batch-012.json
```

## Get Item in a table

You need to create a key.json file.

```bash
aws dynamodb get-item --table-name Starships --key file://key.json --output text
```

## Get Multiple Items

```bash
aws dynamodb batch-get-item --request-items file://request-items.json
```

## Delete Item

```bash
aws dynamodb delete-item --table-name Starships --key file://key.json
```

## Put Item

```bash
aws dynamodb put-item --table-name Starships --item file://item.json --return-consumed-capacity TOTAL
```

## Update Item

In the update expression you set #D as :d.
#D is defined in the expression-attribute-names file, and :d in the expression-attribute-values.

```bash
aws dynamodb update-item \
    --table-name Starships \
    --key file://key.json \
    --update-expression "SET #D = :d" \
    --expression-attribute-names file://expression-attribute-names.json \
    --expression-attribute-values file://expression-attribute-values.json  \
    --return-values ALL_NEW
```

## Scan

The expression attribute can be inline.

```bash
aws dynamodb scan \
    --table-name Starships \
    --filter-expression "begins_with(Description, :d)" \
    --expression-attribute-values '{":d": {"S": "Destroyed"}}'
```

## Query

With the projection-expression you are selecting the attributes to get.
With the no-scan-index-forward option, you get the data in descending order.

```bash
aws dynamodb query \
    --table-name Starships \
    --projection-expression "Registry" \
    --key-condition-expression "ShipClass = :C" \
    --expression-attribute-values '{":C": {"S": "Galaxy"}}'
    --no-scan-index-forward
```

## Delete Table

```bash
aws dynamodb delete-table --table-name Starships
```
